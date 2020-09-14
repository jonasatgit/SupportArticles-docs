---
title: Use the APPEND GENERAL command
description: This article describes how to Use the APPEND GENERAL command with an Imager file.
ms.date: 09/08/2020
ms.prod-support-area-path: 
ms.topic: how-to
ms.prod: visual-studio-family
---
# Use the APPEND GENERAL command with an Imager file

This article introduces how to Use the APPEND GENERAL command with an Imager file.

_Original product version:_ &nbsp; Visual FoxPro  
_Original KB number:_ &nbsp; 152370

## Summary

This article describes how to use the `APPEND GENERAL` command to place PICT files into a Visual FoxPro general field. It also provides some information on problems that might occur and how to correct them.

The general syntax of the `APPEND GENERAL` command to append Imager files in Visual FoxPro is:

```console
APPEND GENERAL <generalfield> FROM <filename> ;
CLASS "Microsoft Imager 2.0 Picture"
```

> [!NOTE]
> This command should be all on one line. It is shown here on two lines due to space limitations.

To determine what to enter for the CLASS clause on the `APPEND GENERAL` command, modify a general field in Visual FoxPro. Then choose the Insert Object command from the Edit menu. The items listed are the class names that will work with the `APPEND GENERAL` command.

## More information

Visual FoxPro ships with some PICT files. They are located in the "Microsoft Visual FoxPro:Samples:Graphics" folder. These graphics are contained in self-extracting archive (sea) files. To follow the steps below, double-click the Picts.sea to extract the PICT files. Store them in the default folder specified 'Picts folder'.

### Step-by-Step Example

The following steps are necessary to use the `APPEND GENERAL` command to place a PICT file into a Visual FoxPro general field:

1. Open the PICT file in Microsoft Imager. Imager can also open TIFF (Tag Image File Format) files. Open the Samples:Graphics:Pict Folder:Fox:ADD.PICT file.
2. From the File menu, choose Save As to save the image file. In the Save As dialog, change the File Type to Macintosh PICT. Select Options in the Save As dialog to change the attributes of the PICT file. The defaults are fine for purposes of this article. Saving the file out of Imager will change the Creator to MIMG so that OLE will work with it.

3. Save the ADD.PICT file as *ADDImager.PICT*. Close the PICT file in Imager.

4. In Visual FoxPro, create or open a table that has a general field. Append a blank record to the table, such as APPEND BLANK. In the Visual FoxPro Command window, issue the following command:

```console
 APPEND GENERAL olefield FROM ;
 HOME()+"Samples:Graphics:Fox:ADDImager.PICT" ;
 CLASS "Microsoft Imager 2.0 Picture"
```  

> [!NOTE]
> The above command should be all on one line. It is shown here as three lines due to space limitations. Your general field name and path to the PICT file may vary.

This PICT file should Append with no problems. However, it is small. PICT files larger than the samples shipped with Visual FoxPro may require more memory to be allocated to Imager before they can be successfully Appended. By default, Imager has 4096K allocated to it. Increasing this to 8192 K will allow larger PICT files to be inserted into the general field with the APPEND GENERAL command or by going through the interface and choosing the Insert Object command from the Edit menu when the general field has been opened (MODIFY GENERAL olefield).

The following program can be used to automate changing the creator of the PICT file to Imager and appending the PICTS into a general field. The program assumes that the Picts.sea file found in the Microsoft Visual FoxPro:Samples:Graphics folder has been unstuffed and the following is the folder path to the Outline PICT files:

```console
<foxpro folder>:Samples:Graphics:Picts folder:Outline
```

There are only six Outline PICT files. This process can take a long time if a large number of files are involved.

```console
 SET LIBRARY TO HOME()+"FoxTools.cfm" ADDITIVE
 DIMENSION farray(1,1)
 CLEAR

 y=HOME()+"Samples:Graphics:Picts folder:Outline:*.PICT"
 x=ADIR(farray,"&y")? "Changing Creators of the Pict files to Imager."
 ?
 FOR i=1 TO x
 IF FxSetType(SYS(2027,HOME()+ ;
 "Samples:Graphics:Picts folder:Outline:"+farray(i,1)),"PICT","MIMG")=0
 WAIT farray(i,1)+" changed successfully" TIMEOUT 2
 ELSE
 WAIT farray(i,1)+" not changed successfully" TIMEOUT 2
 ENDIF
 ENDFOR

 SELECT 0
 CREATE TABLE OutlinePicts (descript C(32), olefield G)?
 ?
 ?
 ? "Appending in the Pict files"
 ?
 FOR i=1 TO x
 APPEND BLANK
 REPLACE OutlinePicts.descript WITH farray(i,1)
 y=SYS(2027,HOME()+"Samples:Graphics:Picts folder:Outline:"+farray(i,1))
 APPEND GENERAL olefield FROM (y) CLASS "Microsoft Imager 2.0 Picture"
 WAIT farray(i,1)+" appended" TIMEOUT 2
 ENDFOR

BROWSE NOWAIT
 SET LIBRARY TO
```