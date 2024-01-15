# Overview
In OpenPnP job processing is currently done by a hierarchical system consisting of two steps. The outer one is the _job processor_ which controls the overall process and calls the inner one, the _job planner_ which selects the next part or parts to process.

## Job Processor
The job processor consists of a linked list of steps that are executed until no more work is to be done. Each step has to select which step is to be executed next. 
* If the user starts a job, the job processor executes the first step, which will collect required information, validate everything and prepare the machine for the job. 
* Next - if configured - the fiducial check will be executed. 
Now the actual processing loop starts. 
** It first collect all pending placements, sorts them as configured by part height or part id and then calls the job planner to select the next placements - one per available nozzle - to be placed.
** If required, nozzle tip changes are executed
** If required, nozzle tip calibrations are executed
** Then parts for all nozzles are picked. If part on checking is enabled, this step may be repeated a configurable amount of times.
** After picking all parts are optionally vision aligned
** and finally placed.
This loop continues until no more pending placements where found.
The last step then generates some statistics information and informs the user about the job status.
In case of an error, the job processor goes into error state allowing the user to correct what ever is required and restarting from where it was interrupted.
By starting the job processing from the UI, all steps are executed in a row until either the job finishes or goes into error state. At any time, job processing can be pauses, stopping the job processor after executing the current step and executed step wise.
Due to the repeated pending placement collection, the user can at anytime change anything in the job and can immediately continue processing taking advantage of the modified configuration. If the job processor is pauses, the user can change literally anything because there is almost nothing stored insight the job processor. Everything is collected when it is needed.

## Job Planner
The job planner receives a list of pending placement and a head to use and from that selects the placements, that can be executed using least nozzle tip changes. This is done by first collecting all available nozzles and searching the list of placements for the first part, that can be placed using each current nozzle tip. If any nozzle is still unassigned, the first part, that can be handed using any different nozzle tip, is selected.
 

As part of the latest test version, placement sorting for multi nozzle machine has been added to the job processor.
