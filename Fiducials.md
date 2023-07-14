## About Fiducials

Fiducials, or fiduciary marks, are small visual identifiers CAD designers place on PCBs specifically to help pick-and-place machines and other processes to visually align the board to a known reference point. In addition, fiducials can also be used to compensate for axis scaling and non-squareness differences between the PCB fabrication process and the pick-and-place machine. A typical PCB that is designed for manufacture will have at least 3 fiducials and may have many more. For the best results, the fiducials should be placed on the PCB as far apart from each other as possible. This means they are typically located near the corners of the PCB. If there are placements on both sides of the PCB, fiducials should be added to both sides.

Fiducials are typically created by placing a circular pad on the PCB's outer copper layer surrounded by a keep-out area that is clear of copper and solder mask.  This creates a bright, shiny mark on the PCB that is easy for computer vision algorithms to identify. 

## Fiducials in OpenPnP

OpenPnP can use fiducials to automatically and accurately determine a board's location during job setup as well as during a job run.

The OpenPnP vision system expects fiducials to be round. This makes detecting the fiducial easier since it looks the same regardless of its (and the board's) rotation. The recommended fiducial is a round copper pad 1 mm in diameter with a 2 mm diameter keep-out.

OpenPnP needs a minimum of 2 fiducials to estimate a board's location, rotation, and a single average axis scaling difference. With 3 fiducials, OpenPnP can estimate a board's location and rotation, the scaling differences in both the X and Y directions as well as axis non-squareness. With more than 3 fiducials, OpenPnP makes the same estimates as with 3 fiducials, but "averages" the results to help eliminate noise/errors in the measurements of the fiducial locations. It is recommended at least 4 fiducials be used if possible.

A fiducial in OpenPnP is defined by a package with a footprint that specifies what the fiducial looks like. For instance, a 1mm round fiducial is simply a footprint containing a 1x1mm pad with 100% roundness.

## Creating a Fiducial Package

For some E-CADs, OpenPnP can import the fiducial shape (footprint) automatically with the placements. Otherwise, you need to create them manually. You only need to perform this process once per package (type of fiducial).

1. Create or select an existing Package from the Packages tab.
2. On the right of the window you should see a Footprint tab. Select this and you will see the Footprint editor.
3. Set the Body Width and Body Length to 0, and set the Units to the units of your Fiducial.
4. Click the Add button ![](https://rawgit.com/openpnp/openpnp/develop/src/main/resources/icons/general-add.svg) to add a new Pad. A fiducial will typically have just one pad.
5. Set the name of the Pad to anything you like. I typically just use "1".
6. Set the X and Y position of the Pad to 0, and set the Width and Length to the diameter of the fiducial. If the fidicual is round, set the Roundness to 100%. For example, a 1mm round fiducial would be defined as X = 0, Y = 0, Width = 1, Length = 1, Roundness = 100%.
7. Go to the Parts tab and create a new Part to represent your fiducial. This is the part you will assign to placements to represent fiducials on boards. Set its package to the fiducial package you created.

## Using Fiducials in Boards

Once you have defined your fiducial part, package and footprint you are ready to use it. You can assign that part to any placement in your board and set the placement type to Fiducial. This will tell OpenPnP to consider this placement for any fiducial operation it needs. You should set at least two placements as Fiducials.

For additional information on using your fiducials, please watch this video tutorial showing how to use fiducials: https://www.youtube.com/watch?v=xvmdvTroZj8

## The Fiducial Detector

The computer vision that is used to detect fiducials is described on the separate [[Fiducial Locator]] page.

## Troubleshooting

If OpenPnP is not finding your fiducials, try the following:

1. Make sure you have set the Units Per Pixel values for your camera. A quick way to test this is to right click the camera view, turn on the Ruler Reticle and see if it matches a ruler placed on the machine.
2. Use solder wick to put a thin coating of solder on the fiducial. This will give it a matte finish that can more easily be seen by the machine vision, compared to the reflective finish that is sometimes present on pads straight from manufacture.
3. On HASL boards, try roughing up the fiducial with an eraser to remove the gloss.
4. Make sure that the fiducial is within a certain distance from where it is expected to be. Go to Machine Setup / Vision / Fiducial Locator and adjust **Max. Distance** if needed. This prevents from locking to something in the camera view which is recognized as fiducial, but really isn't.
5. Don't set a **body** width and length for fiducials, leave them 0.  You only need the pad definition.
6. Change the log level to DEBUG or TRACE. See https://github.com/openpnp/openpnp/wiki/FAQ#how-do-i-turn-on-debug-logging for more information.
7. Restart OpenPnP and try to run your fidicual check again.
8. After you run it there will be a new directory in your `.openpnp` [directory](https://github.com/openpnp/openpnp/wiki/FAQ#where-are-configuration-and-log-files-located) called `openpnp/org.openpnp.vision.pipeline.stages.ImageWriteDebug` and under that directory will be some images. The images show the process of trying to find the fiducials.

  If you understand computer vision a bit, take a look at the images and see if you can find any problems. Common problems are lighting and template size related. If it doesn't help, please post your images to [the OpenPnP mailing list](http://groups.google.com/group/openpnp) and someone will try to help.

