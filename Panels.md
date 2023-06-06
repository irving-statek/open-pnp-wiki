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
Often, a panel will consist of multiple copies of the same board design arranged in a regular array. To assist in setting up such panels, OpenPnP provides a Panel Array Generator that can quickly populate a panel with multiple copies of the same board design.

The first step is to create a new panel on the Panels tab. As shown below, set the panel's size and add a single copy of the board design to the panel as usual. This board will become the board in the first row and first column of the array. The locations of all other boards of the array will be offset from this one so be sure its location and rotation are set correctly before generating the array.

<img width="1087" alt="pnp-test-panelExampleArrayAnnotated1" src="https://github.com/openpnp/openpnp/assets/50550971/5a2c7037-afa5-4b12-8a52-27877e56b8f4">&nbsp; 

The next step is to open the Panel Array Generator by clicking the 
<img width="23" alt="arrayGeneratorIcon" src="https://github.com/openpnp/openpnp/assets/50550971/62ab1e06-d941-4ce1-ba81-3d75f08403ec">
button and create the array as shown below. In this example, a 6 row by 5 column rectangular array is being created:
 
<img width="853" alt="PanelArrayGeneratorAnnotated" src="https://github.com/openpnp/openpnp/assets/50550971/61fb84ef-d8e4-4c5b-bbd8-b825201aa8ac">&nbsp;  

Finally, when the OK button is clicked, the panel array is added to the panel as shown below. Notice that the original board Id has been replicated to all boards in the array and is appended with "[row#,col#]" to designate each element of the array:

<img width="1029" alt="pnp-test-panelExampleArrayAnnotated2" src="https://github.com/openpnp/openpnp/assets/50550971/c9ac495c-cd0c-4104-9482-83df0c500b1d">&nbsp;  

# X-Outs
When a large panelized design is sent to a PCB fabrication house, they will want to know if it is acceptable for some of the boards in the panel to be defective (called X-Outs). This is their way of asking for help in terms of improving yield (which, in turn, will mean lower cost for you). As a PCB panel increases in size, the chances of a defect occurring increases. Boards for production go through electrical testing, and thus a PCB manufacturer will usually know which boards in a panel have problems. For example, too much copper was etched away in a certain area resulting in an open. Or not enough copper was etched away in a certain area resulting in a short. In these cases, the manufacturer will mark the board in the panel with a big "X", which indicates to the assembly operator that board should not have parts placed on it due to a fault in that particular board's PCB.

In OpenPnP, there are two ways to deal with X-Outs:

The first is to simply find the defective boards in the Job table and mark them as disabled (uncheck the Enabled checkbox). In the example below, the job contains two copies of the example panel that was created above. Suppose the second panel has an X-Out in the 5th row 2nd column. As shown below, just find that row in the Job table (being careful to pick the second panel) and disable it:

<img width="809" alt="pnp-test-panelizedJobXout1" src="https://github.com/openpnp/openpnp/assets/50550971/8273aa9d-92cf-49ff-9053-ff643adf9ad5">&nbsp;  

The second, and probably easiest, is to do it graphically in Job Viewer. Just open the Job Viewer (by clicking the 
<img width="24" alt="viewerIcon" src="https://github.com/openpnp/openpnp/assets/50550971/3e4b6f48-42ef-4cb2-ac73-2e7d7578132d">
 button), locate the defective board, right-click on it, verify its Id (in this case Pnl2 ⇒Brd[5,2]) in the pop-up menu and uncheck the Enabled checkbox:

<img width="809" alt="pnp-test-panelizedJobXout2" src="https://github.com/openpnp/openpnp/assets/50550971/7bf973ed-bf0c-49f4-ab01-45d21b50d27b">&nbsp;  

Notice that the board in the Job table has become disabled and in the Job Viewer, it is filled with a red hatch pattern so that it is easy to identify as being disabled:

<img width="809" alt="pnp-test-panelizedJobXout3" src="https://github.com/openpnp/openpnp/assets/50550971/7344975c-de9a-4c63-a046-9c1370d5425c">&nbsp;  
