THIS IS A WORK IN PROGRESS...

A panel is a physical entity consisting of multiple boards and/or subpanels that are physically connected as a single unit for convenience and economy of fabrication and assembly. Once all the boards making up the panel have been assembled, they are physically separated from one another by cutting, snapping, or some other means.

OpenPnP permits any number of different board designs to be part of a single panel design and they can be arranged in any arbitrary manner. However, due to the nature of the PCB fabrication process, all boards of a panel must use the same layer stack-up. Check with the PCB fabricator for any additional restrictions.

# Panel Definitions

In OpenPnP, panels are defined on the Panels tab. See the [Panels](https://github.com/openpnp/openpnp/wiki/User-Manual#panels) section of the User Manual for more details. Each panel definition includes width and length settings to define its physical extent. In addition, each panel definition has two lists: The first is a list of boards and/or subpanels (the panel's "children") that provide the coordinates and orientation of each member of the panel. And the second is a list of fiducials that can be used for "fine tuning" the panel's location and rotation when it is used in a job. Note, as shown in the following figure, all coordinates are defined when viewing the panel from its top side. This includes coordinates of panel fiducials located on the bottom side of the panel, i.e., their coordinates are measured by looking through the panel from its top side. Also note that panels can include a mix of boards oriented with either their top or bottom side towards the panel's top side. The coordinates and rotation angles of the boards within a panel are fully consistent with how the coordinates and rotation angles of boards within a job are defined - the only difference is that they are measured with respect to the panel's coordinate system rather than the machine's coordinate system.

<img width="920" alt="panelDefinition" src="https://github.com/openpnp/openpnp/assets/50550971/ba3032a7-f18e-419c-867b-041cbe04f853">&nbsp;  

# Design Considerations

Since panels are almost always rectangular in shape, it is recommended that the origin of the panel's coordinate system be placed at the lower-left corner of the panel (when viewed from its top side) with the positive X-axis increasing to the right along the lower edge and the positive Y-axis increasing upwards along the left edge of the panel as shown in the above figure. In addition, set the width of the panel to its X extent and the length to its Y extent.  While this isn't an absolute necessity, it does permit panels with placements on both sides to be flipped over easily during a job without the operator having to recapture a new location for the panel. In other words, flipping a panel over to place parts on its other side works exactly the same as when flipping individual boards over. See [[Understanding Board Locations]] for more information.

# Design Example

As an example, the images below depict the top and bottom side of the simulated test panel "pnp-test.panel.xml" included with OpenPnP. It holds three copies of "pnp-test.board.xml" at various orientations.

<img width="882" alt="pnpTestPanelBothAnnotated" src="https://github.com/openpnp/openpnp/assets/50550971/791d841e-9872-4d0c-b817-7d8bcbcf0e78">&nbsp;  

Below is the panel definition as it appears on OpenPnP's Panels tab:

<img width="1097" alt="pnp-test-panelized-panelsTabAnnotated" src="https://github.com/openpnp/openpnp/assets/50550971/d8000bfe-f8e3-4eee-af3d-2b699e82ecda">&nbsp;  

And here is the panel as shown in the Panel Viewer, again showing both its top and bottom sides. Note how the locations of the boards and the panel itself (as denoted by the red and cyan crosshairs) coincide with the boards' and panel's coordinate system origins (as denoted by the red and cyan double arrows) when viewing their respective top sides. Further note that the locations of boards and the panel itself are offset from their respective coordinate system origins by their widths when viewing their bottom sides:

<img width="420" alt="pnpTestPanelTopPanelViewer" src="https://github.com/openpnp/openpnp/assets/50550971/2245529e-a975-47f6-9509-c6e5d29b6f2b">&nbsp;  &nbsp;  &nbsp;  <img width="420" alt="pnpTestPanelBottomPanelViewer" src="https://github.com/openpnp/openpnp/assets/50550971/bed1e9f1-edd3-4af2-a2f5-041a73c8426f">&nbsp;  

# Creating Arrays

<img width="811" alt="pnp-test-panelExampleArray1" src="https://github.com/openpnp/openpnp/assets/50550971/ea011cbb-05f3-40d9-afab-dda80be4ba8d">&nbsp;  
<img width="850" alt="PanelArrayGenerator" src="https://github.com/openpnp/openpnp/assets/50550971/85a97cba-f8c5-487b-9ba8-d62fb2d51ffd">&nbsp;  

<img width="811" alt="pnp-test-panelExampleArray2" src="https://github.com/openpnp/openpnp/assets/50550971/4602fcd7-b16a-43fc-ba81-7afc5f35f3b5">&nbsp;  


# X-Outs
When a large panelized design is sent to a PCB fabrication house, they will want to know if it is acceptable for some of the boards in the panel to be defective (called X-Outs). This is their way of asking for help in terms of improving yield (which, in turn, will mean lower cost for you). As a PCB panel increases in size, the chances of a defect occurring increases. Boards for production go through electrical testing, and thus a PCB manufacturer will usually know which boards in a panel have problems. For example, too much copper was etched away in a certain area resulting in an open. Or not enough copper was etched away in a certain area resulting in a short. In these cases, the manufacturer will mark the board in the panel with a big "X", which indicates to the assembly operator that board should not have parts placed on it due to a fault in that particular board's PCB.

In OpenPnP, there are two ways to deal with X-Outs: The first is to simply find the defective boards in the Job table and mark them as disabled (uncheck the Enabled checkbox). The second is to do it graphically in Job Viewer, open the Job Viewer, locate the defective board(s), right-click on it, and uncheck the Enabled checkbox in the pop-up menu.



# Everything below this line is out-of-date

## Panelization Attributes

The image below shows a panelized design. There are a few key features readily visible on this panel. First, the panel is a 3x3 configuration--there are 3 rows of the design and 3 columns of the design. Each board is 58mm in width and 23mm in length. Second, note that lower left and upper right have fiducials. As with fiducials on a single PCB, fiducials here help the PNP machine orient the board and determine if any rotation is present. In the image below, it's clear that the image is rotated. This was done for testing.

![](https://cloud.githubusercontent.com/assets/24760857/24583526/f4908b70-1701-11e7-8f6c-890540e15435.png)

Note that each board in the panel also has fiducials. If you have fiducials on your panel, in many cases you don't need them on each board. OpenPNP will work with just board fiducials, just panel fiducials, or board and panel fiducials. The final choice will be related to the overall accuracy of your machine. A very accurate machine could get by with just panel fiducials. 

In the panel image above, notice that in the X direction the boards are spaced line-to-line. That is, the right edge of the 1,1 board is butted directly against the left edge of the 1,0 board. This is an X spacing of 0. In the Y direction, there is a small gap because this design uses castellated vias. The Y spacing in this case is 1 mm. 

Below is a drawing highlighting some of the key concepts. Note that PCB 1,1 is the lower left PCB and it is the original PCB you specified. All of the derived PCBs will be based from that location. The lower left of PCB 1,1 is also the design origin. Each PCB will have a unique location in the jobs panel, but the pick and place list for each PCB will be identical,
![](https://user-images.githubusercontent.com/24760857/28502812-766aba98-6faf-11e7-8d72-569b414988d8.PNG)

The X and Y spacing must be specified and will come from the PCB design. 

## Setting the Design Origin 
The lower left corner of PCB 1,1 (the lower-left PCB) is the design origin--**which is NOT NECESSARILY THE LOWER LEFT OF THE panel**. This means the lower left panel fiducial will have a negative Y. We'll enter that information below. This has important implications for your PCB export. **You need to export your PCB such that the lower-left corner of the lower-left PCB is 1,1.** If your design has rails, then some of the those rails (and rail fiducials) will have negative values. 

## Panelizing the Design
To panelize a design, create a job as you normally would, specifying the expected width and height. With the board selected, you'll see the panelize icon become active.

![](https://cloud.githubusercontent.com/assets/24760857/24589463/76e48fa8-178f-11e7-9668-61875589a153.png)

Clicking the panelize design opens a dialog, allowing you specify several parameters. 

![](https://cloud.githubusercontent.com/assets/24760857/24589462/76e110e4-178f-11e7-9085-8078d84d2ee7.png)

In the above image, we've specified the panelized design is a 3x3 array, the X and Y spacings discussed above have been entered, and the panel fiducial locations have been entered. We've also specified a part for the fiducial--in this case the fiducial was 1mm dot. When we select OK, the job list is auto-populated with derived boards. The math at this point is straightforward: We can see the first board location of 1,1 and the derived boards are spaced as expected give the X and Y spacing we entered in the dialog. 

![](https://cloud.githubusercontent.com/assets/24760857/24589460/76d4d0d6-178f-11e7-8112-c5066a2eba87.png)

Note that at this point, the job window acts differently than if we entered all these boards separately. Specifically, you can make changes to the first board in the list in terms of size, XY location, etc. But the other boards in the list cannot be edited: Their size and locations are all derived from the first board. 

Note we could unpanelize the design by clicking on the panelize button again and setting the row and columns both to "1". That would restore the normal behavior of the Job panel.

There is an icon provided in the Job panel to perform a fiducial check on a board. An icon also exists to perform a fiducial check on a panel, shown below.

![](https://cloud.githubusercontent.com/assets/24760857/24589461/76d76ef4-178f-11e7-8756-42ddbb2a25a1.png)

## Fiducials
it is expected that the panel will have two fiducials that are specified in the Panelization dialog. This could be unique fiducials, or they could be any fiducials that are on individual PCBs (located as far apart as possible, ideally). If no fiducials are on the panel or board, then you could leave the fiducial values as zeros and un-tick the "Check Panel Fiducials" checkbox. Doing this would jump straight to the normal board processing.

Very Important: When you specify fiducial locations in the Panelization Settings dialog, you must specify them in panel coordinates. 

If the fiducial information was correctly specified in the panel fiducial dialog shown above, then OpenPNP will calculate the location of all the boards in the panel and update them in the Job window. Below, we can see the Job window _after_ the panel fiducial check was run. As expected, the rotation for all boards in the panel is the same, and the X and Y locations are updated with some rather cryptic values that takes this rotation (and other parameters) into account. 

![](https://cloud.githubusercontent.com/assets/24760857/24589459/76d47ce4-178f-11e7-9e84-810764bc1c1b.png)

## X-Outs
When you have a panelized PCB manufactured for production, the fabrication house will ask you if you can accept "X out" boards. This is their way of asking if you can help them out in terms of improving yield (which, in turn, will mean lower cost for you). As a PCB panel increases in size, the chances of a defect occurring increases. Boards for production go through electrical testing, and thus a PCB manufacturer will usually know that a single board in a larger panel has a problem. For example, too much copper was etched away in a certain area resulting in an open. Or not enough copper was etched away in a certain area resulting in a short. In these cases, the manufacturer will mark the board in the panel with a big "X", which indicates to the operator that board should not be placed due to a fault in that particular board

Since X-outs are rare, most panels will have all boards in the panel populated. But if an X-out is encountered, the location can be specified in OpenPNP using the Panel X-out button

![](https://cloud.githubusercontent.com/assets/24760857/24589458/76d4705a-178f-11e7-8648-3e26e4fc7d99.png)

The button will bring up a dialog allowing you to specify the row and column of the x-out board(s). And upon clicking OK, the corresponding board in the Job list will be disabled. Note that each time you click the Panel X-out button, you start fresh with nothing selected. What you selected last time isn't remembered because each board is different. 

![](https://cloud.githubusercontent.com/assets/24760857/24589457/76d2ae0a-178f-11e7-867d-434e5a1769cb.png)

Upon clicking "OK", the specified board will be disabled for this job run. Note that the X-Out choices and corresponding Enable(s) are not reset after a job run. If you run a panel with an X-out, and the next panel doesn't have an X-out, you will need to specify that the subsequent panel has no X-outs. 

![](https://cloud.githubusercontent.com/assets/24760857/24589456/76d15712-178f-11e7-9214-fd2068b85fef.png)
