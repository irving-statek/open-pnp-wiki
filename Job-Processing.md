# Overview
In OpenPnP job processing is currently done by a hierarchical system consisting of two steps. The outer one is the _job processor_ which controls the overall process and calls the inner one, the _job planner_ which selects the next placement or placements to process.

## Job Processor
### Principle Of Operation
The job processor consists of a linked list of steps that are executed until no more work is to be done. Each step has to select which step is to be executed next. Even if a step has no work to do, it still has to return the next step to execute.
* If the user starts a job, the job processor executes the **PreFlight**step, which will collect required information, validate everything and prepare the machine for the job. 
* Next the **PanelFiducialCheck** followed by the **BoardLocationFiducialCheck** will be executed. 

Now the actual processing loop starts.

  * In the **Plan**ning step all pending placements are collected, sorted as configured by part height or part id and then the job planner is called to select the ones - one per available nozzle - to be processed next.
  * If required, the **ChangeNozzleTips** and **CalibrateNozzleTips** steps are executed
  * Then parts for all placements are **Pick**ed. If part on checking is enabled, this step may be repeated a configurable amount of times.
  * After picking all parts are optionally **Align**ed using computer vision
  * and finally **Place**d.

This loop continues until no more pending placements where found in the _Plan_ step.

* The **Finish** step then generates some statistics information and informs the user that the job has been executed.

In case of an error, the job processor goes into error state allowing the user to correct what ever is required and restarting from where it was interrupted.

By starting the job processing from the UI, all steps are executed in a row until either the job finishes or goes into error state. At any time, job processing can be pauses, stopping the job processor after executing the current step, and restarted or executed step wise.
Due to the repeated pending placement collection, the user can at anytime change anything in the job and can immediately continue processing taking advantage of the modified configuration. If the job processor is pauses, the user can even change literally anything because there is almost nothing stored insight the job processor. Everything is collected when it is needed. One exception is the board location. As the fiducial check is part of the initialization sequence of the job processor, changing the boards location requires to restart the job. However, even that is transparent for the user as the placed/not placed state is not changed then the job processor is stopped or started. Only parts currently on any nozzle will be discarded using the discard location.

### Configuration
The job processor can be configured using the _Job Processors_ leaf on the _Machine Setup_ tab.

![JobProcessorCfg](https://github.com/user-attachments/assets/006dbf3e-b3f0-4819-9747-dcf3d9db5cfe)

* **Job order** selects the sorting algorithm used in the planning step. One can select between _Part_, which sorts all placements by their part ID or _PartHeight_ which sorts them by their height.
* **Nozzle tip loading strategy** select the strategy the job planner uses to select the next placement to be processed. Possible options are _Minimize_ which will choose any placement that is compatible with the currently loaded nozzle tip, _StartAsPlanned_ will choose the first planned placement (compatible with any nozzle tip compatible with the nozzle) on job start and continue as _Minimize_ does or _FullyAsPlanned_ will always choose the next planned placement. With _Minimize_ a job executed multiple times will run in a forward backward manner as each restart still reuse the nozzle tip(s) currently loaded. With _StartAsPlanned_ a job will always be executed in the same forward direction with the same nozzle tip sorting, potentially triggering a nozzle tip load event on job start. With _FullyAsPlanned_ jobs are executed following the planned job order closely, potentially triggering extra nozzle tip load events. For single nozzle machine the result will be very good, for multi nozzle machines, the result may vary because the job planner favors to utilize all nozzles.
* **Max Vision Retries** is the maximum number of retries, that the job processor will make in the pick step to pick a part. Please note, that this only works if part on checking via vacuum is configured. On vision failure in the align step, the job processor will immediately go into error state without any retry.
* **Step Next Motion** will instruct the job processor to continue executing steps until a motion has been executed. This makes the operation of the job processor more comprehensible and shall be checked for normal users. If unchecked, the job processor will stop even for steps, that do nothing like the _ChangeNozzleTips_ step when there is no nozzle tip change needed.
* **Optimize Multiple Nozzles** will, if checked, optimize the placements on multi-nozzle machines in the _Pick_, _Align_ and _Place_ steps to optimize the travel path. If uncheck the job processor will revert to the old behavior and execute each step in the order of nozzle names.

## Job Planner
The job planner receives a list of pending placement and a head to use and from that selects the placements, that can be executed using least nozzle tip changes. This is done by first collecting all available nozzles and searching the list of placements for the first part, that can be placed using each current nozzle tip. If any nozzle is still unassigned, the first part, that can be handed using any different nozzle tip, is selected.

## Optimization
As part of the recent test version, placement sorting for multi nozzle machine has been added to the job processor. This means that in the _Pick_, _Align_ and _Place_ step current placements will be optimized to minimize the path the head has to travel to execute this step. This is done by collecting all (approximate) locations the head will be moved to. This information will be combined with the heads current location and feed into a traveling salesman algorithm which will sort the nozzles such, that the geometrical distance is shortest. At present, the optimization will not take the location into account where the head is expected to be moved as part of the next step. This may result in not always finding the optimal overall solution for the problem.

## Outlook
For an even better performance of the job processor and job planner the job processor shall be enabled to take the location the head will be moved to in the next step into account. Further the general sorting of placements as part of the job processors initialization voids any user optimized sorting of placements. To overcome this the sorting could be disabled and a user sorting option could be added to the placement table in the job tab. Even better would be to add a sorting step to the job planner in order to select placements for the second and further nozzles based on their distance to the first with respect to their pick and place locations.