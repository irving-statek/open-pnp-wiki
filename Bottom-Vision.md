# Introduction

Bottom Vision is a feature in OpenPnP that makes it possible to place components more accurately. Simply put, by using an up looking camera OpenPnP can identify if a part was picked with any offset or rotational error, determine what that error is and then apply a correction before placement. Bottom Vision can also be used to determine if a pick failure occurred.

# Operating Theory

The basic idea for bottom vision is:

1. Pick a part from a feeder.
2. Center the nozzle over an Up looking camera and take an image.
3. Using a [[CvPipeline]], determine the part's offset from center and whether or not it is rotated.
4. Provide the resulting X, Y and Rotation coordinates to the JobProcessor so that the error can be corrected during placement.

# Preparations

## Machine Setup and Calibration

Be sure to have followed [[Issues and Solutions]] to have configured and calibrated your machine, cameras, nozzle offsets etc. including notably the [[Nozzle Tip Background Calibration]].

## Nozzle Tip Configuration

As a further prerequisite, you also need to configure settings on each nozzle tip: 

![Max. Pick Tolerance](https://user-images.githubusercontent.com/9963310/156925117-1c2fbd35-3003-4354-96bd-6481b9920342.png)

**Min. Part Diameter** sets the minimum part dimension assumed to be picked with this nozzle tip. This diameter is used to calculate an _inner_ diameter of the nozzle tip that will always be covered by the part (**Min. Part Diameter** minus two times the **Maximum Pick Tolerance**). The pixels in the _inner_ diameter are then disregarded/blotted-out  by the [Background Calibration](/openpnp/openpnp/wiki/Nozzle-Tip-Background-Calibration).

**Max. Part Diameter** sets the maximum part dimensions (diagonal) assumed to be picked with this nozzle tip. This diameter is used to limit the image area that is taken into consideration. This is also effective as a circular black mask (using MaskCircle). If your camera view has background elements in the periphery that are disruptive to computer vision (there is often some glare), this is a way to cut it off.

**Maximum Pick Tolerance** sets the maximum allowed distance of the detected part, from its expected location. If it is larger than the given distance, an error will be thrown. This also controls the vision process, by limiting the maximum search distance.

# Using the Stock Vision Settings

It is recommended to always first try to use the unchanged stock vision settings for bottom vision. 

NOTE: If your configurations comes from an older version of OpenPnP, chances are that you are not already using the stock vision settings. The following will show you how to select and use them, step by step.

## Select Stock Vision Setting

The first step is to try it out with a specific part that you have available for testing. Go to the **Parts** tab, select that part in the list.

Then back in the list, select the "- Stock Bottom Vision Settings -" in the drop-down in the Bottom **Vision column** cell. If there are any prior Vision Settings, note them down, so you can revert later.

![Select Stock Bottom Vision Settings](https://user-images.githubusercontent.com/9963310/228458733-893feb16-0499-40ae-a9ba-850babc0ae76.png)

Go to the Bottom Vision Settings wizard (lower tab), you should get the so-called [[Exposed Pipeline Parameters]], visible as sliders to control the essential parameters of the vision process.

![Sliders](https://user-images.githubusercontent.com/9963310/155244204-5df01b2c-02be-4475-92b4-4063c36e7acc.png)

## Specialize for Package or Part

Press the **Specialize for XXX** button to make a changed setting special for the part (and _not_ affect other parts and packages). This will clone the prior settings it had before.

![Specialize for Part](https://user-images.githubusercontent.com/9963310/228461736-b28dbd8c-2756-4794-9be8-e27765675090.png)


## Tuning Bottom Vision

Pick a part from the feeder and just try it out using a first **Test Alignment**:

![Test Alignment](https://user-images.githubusercontent.com/9963310/228464032-df99a50c-6a5e-4f3e-925f-fe6c3d95927f.png)

This will perhaps not yet work, but it will move the part to the bottom camera and make sure it is properly lighted. Now you can use the sliders to control the Bottom Vision process.

**Threshold** sets the minimal brightness that isolates the shiny contacts of a part from the background.

**Min. Detail Size** sets the minimum size of any details that should still be considered contacts. You can suppress background specks, image noise etc. that might otherwise spoil the detection.

The effect of the tweaking is directly shown in the Camera Preview. As configured in the parameter, the slider previews the [[Computer Vision pipeline|CvPipeline]] stage that best shows the effect of the adjustments. In addition, if you stop dragging the slider, the computer vision end result is displayed with a slight delay. Watch it in action in the animation:

![Parametric-Pipeline](https://user-images.githubusercontent.com/9963310/155286493-f074b6b0-74c2-4d2e-ac30-3619a70f424d.GIF)

Using these parameters you should get your bottom vision going in almost all cases. 

Use the **Test Aligment** button again, it should now iteratively center and align the part. The iterative process is controlled by some settings in the [Global Configuration](#global-configuration) explained further below.

## Parts Too Big For Camera?

[[Vision Compositing]] a.k.a. Multi-Shot Bottom Vision can help for parts that are larger than the camera view. Or for other problematic/non-rectangular parts.

## Troubleshooting

If the stock bottom vision and tweaking the parameters does not work for you, here are a few ideas:

- Double-check the [Preparations](#preparations) section above.
- Check if [[Camera Settling]] is configured right.
- Check if [[Nozzle Tip Background Calibration]] was configured right, and is up to date. 
- Perhaps watch this video for a way to let Issues & Solutions help you:
  
  [OpenPnP: Issues & Solutions New Machine Setup Guidance](https://youtu.be/Pxg6g3KI5_E)
 
If all this fails, we developers are interested to understand why your case fails, and ask you to contribute your images to the [discussion group](http://groups.google.com/group/openpnp). To do so, double click your camera view and go to your `$HOME/.openpnp2/snapshots` directory, where the camera image was saved (see the [FAQ](https://github.com/openpnp/openpnp/wiki/FAQ#where-are-configuration-and-log-files-located) for where that directory is on your computer).

## Make it the new Default

If you are pleased with the results and want to use the Stock Bottom Vision Settings more generally, you can [make it the system default](https://github.com/openpnp/openpnp/wiki/Computer-Vision#make-stock-vision-the-default). 

## Vision Settings Inheritance

Vision Settings, including the sliders settings, are applied on a multi-level inheritance scheme:

- As the **Default** (in Machine Setup / Vision), effective for all packages and parts, unless specialized there
- On the **Packages** level, effective for all parts with that package, unless specialized there
- On the **Parts** level, effective only for that Part

The idea is to unify and centralize the settings as much as possible in order to make maintenance easier and to easily propagate adjustments to all relevant parts, by inheritance. 

If you need special settings on a specific type of package or part, including special** Threshold** or **Min. Detail Size** slider settings, use the **Specialize for XX** button as described in [Specialize for Package or Part](#specialize-for-package-or-part) further above.

If you want a package or part to inherit the default from the prior level, just unassign the Vision Settings, i.e. select the empty entry:

![Unselect Vision Settings](https://user-images.githubusercontent.com/9963310/228483144-b7308203-cb69-4b13-b5d5-9950de384574.png)

To understand how this all interacts, you may also want to watch the (somewhat outdated) video:

- [OpenPnP New Bottom Vision Settings](https://youtu.be/W63GbSf5BHk)

## How Bottom Vision Works Internally

Core to the working of Bottom Vision is the [[CvPipeline]]. The pipeline describes a series of computer vision operations that will take place to convert the input image to a `RotatedRect`. `RotatedRect` is computer vision speak for a rectangle (width, height, X, Y) with a rotation component. Using this `RotatedRect` we calculate the alignment error and correct it.

# Global Configuration

To configure Bottom Vision visit Machine Setup -> Vision -> Bottom Vision.

![Bottom Vision General Settings](https://user-images.githubusercontent.com/9963310/228593283-60dfbf44-5674-481c-ad13-94628dc234d7.png)

**Enabled?**: Switches on bottom vision.

**Bottom Vision Settings**: Select the Vision Settings you want as the global default. 

**Rotate parts prior to vision?**: Will already pre-rotate the part on the nozzle to its final placement angle. This generally improves precision. This option also enables adaptive multi-pass vision. The option is **mandatory** for nozzles that have limited articulation (i.e. less than 360°). It should also be enabled when the **PlacementAngle** optimization **Rotation Mode** is set on the nozzle. See [[Nozzle Rotation Mode]].

### Multi-pass Vision

The part will first be positioned and rotated as picked from the feeder, which means it might be slightly offset both in location and angle, due to play in the feeder etc. The vision operation then determines these offsets, but these may be inaccurate as the part is seen slightly from the side, which may create parallax errors and slight changes in light reflection angles which might affect how beveled/angled surfaces are lighted. Furthermore, the scale in the camera view (units per pixel) might be slightly in error as the part or some of its features (angled pins etc.) might be slightly outside the focal plane. 

In order to further improve precision, the part is then centered in the camera view, according to the preliminary offsets and the process is repeated. By centering the part (multiple times), the mentioned errors can be cancelled out by symmetry. 

**Max vision passes**: Determines the maximum number of passes used to pinpoint the part (only applies when pre-rotate is active).

**Max linear offset**: As long as preliminary linear offsets are larger than this, further passes are made, up until **Max. vision passes** is reached. Linear offsets are center offsets and corner offsets, see the illustration below. (only applies when pre-rotate is active).

**Max angular offset**: As long as preliminary angular offsets are larger than this, further passes are made, up until **Max. vision passes** is reached (only applies when pre-rotate is active).

![Offsets Explained](https://user-images.githubusercontent.com/9963310/115862405-67b37180-a434-11eb-96ff-1f22ec1c9b0b.png)

# Part Configuration
___
NOTE: the following is quite outdated, not taking into account the new system of **Vision Settings** or **Exposed Pipeline Parameters**.

The following is no longer entirely accurate and left for older versions of OpenPnP.
___

Each Part in your Parts library can have it's own custom pipeline. In most cases the default pipeline will work but this allows you tweak the pipeline for troublesome parts or create entirely new pipelines when the default won't work.

You can also enable or disable bottom vision on for each part.

To access the bottom vision part settings go to the Parts tab in OpenPnP, select a part and look for the Alignment tab on the right.

![Part Bottom Vision Settings](https://user-images.githubusercontent.com/9963310/115862882-f9bb7a00-a434-11eb-92c6-b55d5710af25.png)

**Enabled?**: Switches on bottom vision for this part. This is checked in addition to the global **Enabled?** switch (see above).

**Pre-rotate**: This can either inherit the global setting or override it as **Always On** or **Always Off**. 

![Pre-Rotate Options](https://user-images.githubusercontent.com/9963310/115863124-4e5ef500-a435-11eb-8745-c0ffd324b28e.png)

**Test**: Press the `Test Alignment` button to perform an alignment of that part. It must already be picked and held on the nozzle. 

**Center After Test**: Centers the part after the vision alignment test. 

**Pipeline**: Press the `Edit` button to view and edit the part specific pipeline that will be used to locate parts. Press `Reset to Default` to reset the pipeline to the global default (see [Global Configuration](#global-configuration)). 

**Rotation**: Use the option **Adjust** for all standard pipelines. Only an adjustment of ±45° can be detected and applied, which is usually more than sufficient for parts picked from normal feeders. Use the option **Full** only for special pipelines that specifically support full 360° orientation detection (pin 1 detection). **CAUTION**: You may get very strange and intermittent errors, if you set **Full** and your pipeline is not specifically made for it.  

![Rotation Options](https://user-images.githubusercontent.com/9963310/115863659-10ae9c00-a436-11eb-8a86-1ecb4db357e4.png)

**Part Size Check**: You can add a part size check, where the vision result is compared against a known good part size. This can serve as an alternative to vacuum sensing tests, to detect whether a pick was successful. 

- Use **BodySize** if your pipeline looks for the body of the part (e.g. the black body of lead free packages). 
- Use **PadExtents** if your pipeline looks for the contacts (which the default pipeline does). Note, you need to define the footprint in the Package for this to work.

![Part Size Check Options](https://user-images.githubusercontent.com/9963310/115866469-fd9dcb00-a439-11eb-90b8-6b47ad4c49a6.png)

**Size tolerance (%)**: Determines by how much (relatively speaking) the detected part size may deviate from the footprint defined size. If the tolerance is exceeded, the aligment fails. Most likely the part was then not properly picked. 

# Multi-Shot Bottom Vision

Vision Compositing a.k.a. Multi-Shot Bottom Vision can take more than one vision shot to determine the alignment of a part. See the extra [[Vision Compositing]] page.

# Usage

## Note: This section will be expanded soon.

See https://www.youtube.com/watch?v=pRYQaFKhsuw for a short demonstration of how to pick, test and discard a part for bottom vision.

When bottom vision is enabled in Machine Setup and for a specific part it will be used automatically during a job run. If the system is able to determine the offsets they will be applied. If the operation fails the placement will continue with no offset correction. This will be improved in the future to handle retry and discard.

# Default Pipeline

OpenPnP comes with a default pipeline. The pipeline was developed for one particular machine design but using the [[CvPipeline]] tools it is possible to customize the pipeline for any type of machine. In general, the changes should be minimal if certain rules are followed. If you have not read [[CvPipeline]] it's worth taking a moment to do so as it will help you understand the rest of this.

The default pipeline is described below:

1. ImageCapture: Waits for the camera to settle and captures an image.
2. ImageWriteDebug: Writes the input image to a file on disk to help with debugging.
3. BlurGaussian: Performs minor blurring on the input image. This is used to reduce noise in the image.
4. MaskCircle: Blacks out everything outside of a circle of a given diameter. On the development machine this circle represents a "safe" area in the image where nothing is visible except the nozzle.
5. ConvertColor: Convert from RGB color to HSV color, which is required for the next stage.
6. MaskHsv: Searches the image for any pixels that match a certain hue (the H in HSV) and turns them black. The purpose of this is to remove green and "greenish" pixels from the image. Green is the color of the nozzle holder. This is similar to the concept of "green screening". See also the [[Nozzle Tip Background Calibration]].
7. ConvertColor: Convert back from HSV to RGB. This is required by the next stage.
8. ConvertColor: Convert from RGB to grayscale.
9. Threshold: Turns the image into a binary image - meaning that it has only two colors: white and black. Any gray pixels that are darker than the threshold value turn black and any lighter turn white.
10. FindContours: Find connected contours in the image. Contours are a way to describe simple features in an image such as lines and curves.
11. FilterContours: Removes any contours from the previous stage that are smaller than a specified value. This helps remove noise and features that don't pertain to the main contour around the part.
12. SetColor: Sets the entire image to black. This simply provides a blank canvas for the next stage to draw on.
13. DrawContours: Draw all the of the remaining contours in white on the black background. At this point we hope that we're simply drawing the shape of the part only.
14. MinAreaRect: This is where the magic happens! MinAreaRect creates a `RotatedRect` that fits around any non-black pixels in the image. Since we drew contours representing the part this now finds the bounds and rotation of the part.
15. ImageRecall: Recalls the original input image so that we can show the user the results of all this work.
16. DrawRotatedRects: Draw the `RotatedRect` in red overtop the recalled original image. If all went well we should now see the original input image with a red rectangle surrounding the part.
17. ImageWriteDebug: Writes the resulting image out to a file for help with debugging.

# Tips

* Much of the purpose of the vision pipeline is to filter the image so that the only thing that is visible is the part you are interested in. The various Mask stages and Thresholds can help with this.
* Switch off the camera's auto-exposure. Otherwise, this will never be stable and repeatable. If your camera does not allow that, chose a different model. Seriously.
* You should set your Camera's exposure, so you the [dynamic range](https://en.wikipedia.org/wiki/Dynamic_range) includes the brightest parts of the image without [clipping](https://en.wikipedia.org/wiki/Clipping_(photography)). We need to be able to distinguish the bright tones. Use the shiniest part to set this up. The image will be rather dark for humans, but it is fine for machine vision. 
* The following animation quickly shows you how to initially set or tune a threshold, using the mouse to probe image pixels. Look at the third channel (the `V`) of the `HSV(full)` indicator on the status line (this indicator for brightness will work both for color and grayscale images). Probe the pixels that should be _excluded_ and probe the pixels that should be _included_, then set the threshold to a value in between:

  _While holding down the Shift key, use the context menu to display the image separately. This will restart the animation from the beginning._

  ![BottomVisionThreshold](https://user-images.githubusercontent.com/9963310/96963951-e1ac8180-1509-11eb-87c5-630dec575931.gif)

* If you have green color Juki nozzle tips you can use the MaskHSV stage to mask the green parts reliably. Make yourself familiar with the HSV model ([from the Wikipedia](https://en.wikipedia.org/wiki/HSL_and_HSV)). 

  ![HSV Model](https://user-images.githubusercontent.com/9963310/96978029-335f0700-151e-11eb-9833-802249f8b7ef.png)

* The following animation quickly shows you how to initially set or tune an HSV mask, using the mouse to probe image pixels. First we probe green pixels and look at the first channel (the `H` as in "hue") of the `HSV(full)` indicator on the status line to _include_ what is green. Then we _exclude_ parts that are too bright (the pins) looking at the third value (the `V` as in "value" i.e. brightness) of the `HSV(full)` indicator on the status line. Finally, we _exclude_ the parts that are too dark (background stuff, the IC body), using the same method. 

  _While holding down the Shift key, use the context menu to display the image separately. This will restart the animation from the beginning._

  ![MaskHSVSetup](https://user-images.githubusercontent.com/9963310/96976803-8e8ffa00-151c-11eb-8db1-edc0626316c7.gif)
  
# FAQ

## How do I see debug images?

Enable DEBUG or TRACE level logging. See [this FAQ](https://github.com/openpnp/openpnp/wiki/FAQ#how-do-i-turn-on-debug-logging) for more information. Bottom vision will now produce a pair of debug images in your `.openpnp/org.openpnp.vision.pipeline.stages.ImageWriteDebug` directory.
