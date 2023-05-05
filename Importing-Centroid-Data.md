See the sections below for information about importing boards from your preferred CAD software into OpenPnP.

EAGLE
-----
OpenPNP can read EAGLE .brd files directly:
1. File -> Import Board -> CadSoft EAGLE Board

Options:
* Create Missing Parts: Creates new parts in the OpenPnP library if they don't already exist
* Update Existing Parts: Updates or creates footprint outlines and package mappings in the OpenPnP library for parts that already exist
* Add Library Prefix to Part Names: Creates unique part names that include the library name where the same part name might exist in multiple libraries
* Import Parts on Top of the board: Import all parts located on the top side of the board
* Import Parts on Bottom of the board: Import all parts located on the bottom side of the board

You can also export from EAGLE via a ULP:
1. In EAGLE, run mountsmd.ulp, which is included in the EAGLE distribution.
2. In OpenPnP, go to the menu at File -> Import Board -> EAGLE mountsmd.ulp.

KiCAD
-----
1. In OpenPnP, go to the menu at File -> Import Board -> KiCAD .pos.

Altium
------
1. Under Outjob -> Assembly Outputs add Generates pick and place files and set Formats to CSV.
2. or While Viewing the PCB, use File -> Assembly Outputs -> Generate pick and place files and set Formats to CSV.
3. In OpenPnP, use File -> Import Board -> Altium CSV. (In older OpenPnP versions use Named CSV and export "Ref-X" and "Ref-Y". The default "Center-X" and "Center-Y" are not supported.)

Others
------

OpenPnP includes a Reference CSV importer (File -> Import -> Reference CSV) (Named CSV in old OpenPnP versions) which can import many types of CSV files. X, Y and Heights coordinate data is converted automatically from Mil to Millimeter if their field ends (case insensitive) in "(mil)". Units in the data ("mm" or "mil") are automatically removed. Side/TBs data is decoded by looking at the first character only ('b' for bottom and 't' for top side, again case insensitive). Field separator must be a comma. 

Format specifications need to be inside the first 50 lines of file.

The first six fields are required in order to successfully import centroid data.

The fields that the Reference CSV importer will look for are: (not case sensitive)
* Refs: "Designator", "Part", "Component", "RefDes", "Ref"
* Vals: "Value", "Val", "Comment", "Comp_Value"
* Packs: "Footprint", "Package", "Pattern", "Comp_Package"
* Xs: "X", "X (mm)", "Ref X", "PosX", "Ref-X(mm)", "Ref-X(mil)", "Sym_X"
* Ys: "Y", "Y (mm)", "Ref Y", "PosY", "Ref-Y(mm)", "Ref-Y(mil)", "Sym_Y"
* Rots: "Rotation", "Rot", "Rotate", "Sym_Rotate"
* TBs: "Layer", "Side", "TB", "Sym_Mirror"
* Heights: "Height", "Height(mil)", "Height(mm)"

Example:

```
"Designator","Footprint","Mid X","Mid Y","Ref X","Ref Y","Pad X","Pad Y","Layer","Rotation","Comment"
"C1","NICHICON_A","58.674mm","7.2263mm","58.674mm","7.239mm","58.674mm","8.7376mm","T","90.00","10uF"
"C3","CAP0603","54.102mm","8.255mm","54.102mm","8.255mm","54.102mm","9.1694mm","T","270.00","1uF"
```