## What is it?

The Fiducial Locator is a piece of software inside OpenPnP which detects the precise fiducial location using the down-looking camera. 

### Operating Principle

1. It first moves the camera to the expected location of the fiducial. This might be slightly off the true location, due to various tolerances in PCB manufacturing, or when mounting the board on your machine. 
1. The Locator then detects the fiducial location inside the camera view using computer vision. 
1. If it is not satisfied with the accuracy, it updates the expected location to the detected location and repeats the process. This can happen multiple times.

By re-centering the camera on top of the fiducial it can improve the accuracy. This is due to the fact that when the camera is perpendicularly on top of the fiducial, the view of the fiducial is _symmetric_, so any errors due to an inaccurate scale of the camera view will be cancelled out, i.e. the fiducial might appear slightly smaller or larger, but that error is symmetrical all around. An error in scale can in turn come from badly calibrated Units per Pixel, or from a difference in true Z, as shown in this illustration:

![parallax-error](https://github.com/openpnp/openpnp/assets/9963310/511bed57-4013-485b-a68f-925d7af249ae)

The re-centering also makes sure that lighting is symmetrical, which might be relevant when the fiducial is very reflective and beveled, which is typically the case for HASL coated PCBs, where the solder coating edges are rounded from surface tension. Asymmetrical lighting might make it appear shifted, inducing positional errors.

### Parallax Operating Principle

As an option, OpenPnP allows for an alternative "Parallax" detection method. 

Highly reflective fiducials, such as HASL (Hot Air Solder Leveling) surface-finished fiducials, are very hard to properly light. They will likely reflect the camera peeking through the diffuser, therefore, the fiducial might appear (partially) dark and/or not well-formed for detection. Only very elaborate lighting systems, such as co-axial (half-way mirror) lights can overcome the problem. Those lights are expensive/difficult to make, and have down-sides like very narrow viewing angles, high weight and bulky size.

By detecting fiducials from a parallax view-point, the fiducial appears at an angle, and if properly spaced, will reflect the bright diffuser of the camera light. Doing this from two opposite, symmetrical viewpoints, the mid-point can be taken to cancel-out any errors introduced by the angled view.

![image](https://github.com/openpnp/openpnp/assets/9963310/f7371c5b-bff5-4350-9048-8338843ce922)

The following animation shows how the fiducial shows a unenven dark center reflection when seen centered, but is lit fully when seen at a parallax. 

![parallax-animation2](https://github.com/openpnp/openpnp/assets/9963310/855dc087-94c0-47af-a8b5-fc4901b42222)


## Using the Stock Vision Settings

If your configuration originally comes from an older OpenPnP version, you might not yet have the new stock fiducial vision enabled. If this is the case, the following will let you try the new visions settings. They can then be made the standard if you're satisfied.

1. Make sure to have **View** / **Selections in Tables** / **Linked** enabled.
1. In the **Placements** table, select the fiducial in question:
   ![image](https://github.com/openpnp/openpnp/assets/9963310/4a9ddaba-a6f9-4661-a415-49c8e577da13)

1. Go to the **Parts** tab, the fiducial part should be selected, and then in the drop-down, select the **Stock Fiducial Vision Settings**:
   ![image](https://github.com/openpnp/openpnp/assets/9963310/752d96d9-ec4a-49df-99a5-91cb445ec27c)

1. Go to the **Fiducial Vision Settings** detail tab and press the **Specialize for ...** button. This way we're any tweaking will be private to that part:
   ![image](https://github.com/openpnp/openpnp/assets/9963310/7b583394-f37c-4c9b-a790-c876e9f5f588)

1. Then press **Pipeline Edit** and note the presence of the **DetectCircularSymmetry** stage that indicates this is the modern stock pipeline:
   ![image](https://github.com/openpnp/openpnp/assets/9963310/70dd853a-3a08-49fc-a97d-fcfc52588e28)

1. For troubleshooting, you can enable the "deb0" and "deb1" stages (see in the screenshot above). The vision process will then write debug images to the disk. See [where to find them](https://github.com/openpnp/openpnp/wiki/FAQ#how-can-i-get-a-native-camera-image).

1. Go on to configure and test the Fiducial Locator as shown in the next sections.

1. Once you're satisfied, you can make it the standard. Use the analog procedure as described for Bottom Vision, [here](https://github.com/openpnp/openpnp/wiki/Computer-Vision#make-stock-vision-the-default).

## Fiducial Vision Settings

### Assigning Fiducial Vision Settings

The **Fiducial Vision Settings** can be assigned to parts and packages. Use the drop-down in the table view:

![image](https://github.com/openpnp/openpnp/assets/9963310/752d96d9-ec4a-49df-99a5-91cb445ec27c)

There is the default assigned in **Machine Setup / Vision / Fiducial Locator**:

![image](https://github.com/openpnp/openpnp/assets/9963310/03c41f6f-c9ec-4642-b55b-c2bd3e6c62a0)

If a part or package has no setting assigned, it will inherit it from the more general level, a part inherits from its package, a package inherits from the default. 

To unassign a setting from a part or package, just select the empty entry from the drop-down. It will then inherit its settings from the more general level.

For further information about this inheritance principle, please [watch the (rather old) intro video](https://youtu.be/W63GbSf5BHk).

### Configuration

![image](https://github.com/openpnp/openpnp/assets/9963310/c217504f-7efb-4b28-aabc-cae73ff21af9)

### General

**Name** of the vision settings.

**Assigned to** tells you which parts, packages, etc. have this particular **Fiducial Vision Settings** assigned. Always be aware that any changes you apply to these settings, affect all the parts and packages that have the same settings assigned, or inherited.

**Specialize for ...** copies the current vision settings and assigns them to the part, package. So any changes you make will only affect this one part or package (in the latter case it might be inherited by parts with that package).

If **Generalize** is pressed on a package, then the specialized vision settings will be unassigned, and they will now inherit our settings.

If **Generalize**  pressed on the **Machine Setup / Vision / Fiducial Locator** then the specialized vision settings on all the packages and parts will be unassigned. OpenPnP will ask you to confirm this.

**Reset to Default** will overwrite the vision settings to the default as set on **Machine Setup / Vision / Fiducial Locator**. 

**Enabled** allows these vision settings to be used in operation.

**Pipeline Edit** allows you to edit the specific pipeline.

**Pipeline Reset** resets the pipeline to the stock vision pipeline.

The green **Copy** and **Paste** buttons, allow you to transfer pipelines from settings to settings, or to share them in the community.

### Fiducial Locator

The **Max. Vision Passes** determines how many times the Fiducial Locator will try to improve the fiducial detection, iteratively centering-in on the detected fiducial. See the [Operating Principle](#operating-principle].

The **Max. Linear Offset** determines the acceptable residual offset for the fiducial detection. If the residual offset is smaller, then the iteration is stopped early.

When the **Parallax Diameter** is given, the Fiduial Locator will automatically switch to the parallax mode and perform its detection from two parallax camera view-points on both sides of the expected location of the fiducial. The distance between the two is set by the **Parallax Diameter**.

The **Parallax Angle** determines the angle at which the opposite view-points are spaced from each other. Choose an angle which favors the faster axis of your machine: 0° favors X, 90° favors Y. 

Other angles might be used to target a specific pair of LEDs of a LED ring reflection, i.e., where the reflection is brightest and most symmetrical around the spot:

![Angled Parallax](https://github.com/openpnp/openpnp/assets/9963310/28baf24b-18b1-432d-b3dc-a2bf57ff1af1)

You can jog-rotate the cross-hairs to measure the angle.


### Testing the Fiducial Locator

Move the camera near a fiducial and use the **Test Fiducial Locator** button to test the detection and centering. 