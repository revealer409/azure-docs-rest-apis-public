---
title: "Sample Workflow to Prepare Hard Drives for an Import Job"
ms.custom: na
ms.date: 05/25/2015
ms.prod: azure
ms.reviewer: na
ms.service: storage
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
caps.latest.revision: 10
author: tamram
manager: adinah
translation.priority.mt: 
  - de-de
  - es-es
  - fr-fr
  - it-it
  - ja-jp
  - ko-kr
  - pt-br
  - ru-ru
  - zh-cn
  - zh-tw
---
# Sample Workflow to Prepare Hard Drives for an Import Job
This topic walks you through the complete process of preparing drives for an import job.  
  
 This example imports the following data into a Window Azure storage account named `mystorageaccount`:  
  
|Location|Description|  
|--------------|-----------------|  
|H:\Video|A collection of videos, 5 TB in total.|  
|H:\Photo|A collection of photos, 30 GB in total.|  
|K:\Temp\FavoriteMovie.ISO|A Blu-Ray™ disk image, 25 GB.|  
|\\\bigshare\john\music|A collection of music files on a network share, 10 GB in total.|  
  
 The import job will import this data into the following destinations in the storage account:  
  
|Source|Destination virtual directory or blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
 With this mapping, the file `H:\Video\Drama\GreatMovie.mov` will be imported to the blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
 Next, to determine how many hard drives are needed, compute the size of the data:  
  
 `5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
 For this example, two 3TB hard drives should be sufficient. However, since the source directory `H:\Video` has 5TB of data and your single hard drive's capacity is only 3TB, it's necessary to break `H:\Video` into two smaller directories before running the Microsoft Azure Import/Export tool: `H:\Video1` and `H:\Video2`. This step yields the following source directories:  
  
|Location|Size|Destination virtual directory or blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2.5TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2.5TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 Note that even though the `H:\Video`directory has been split to two directories, they point to the same destination virtual directory in the storage account. This way, all video files are maintained under a single `video` container in the storage account.  
  
 Next, the above source directories are evenly distributed to the two hard drives:  
  
||||  
|-|-|-|  
|Hard drive|Source directories|Total size|  
|First Drive|H:\Video1|2.5TB + 30GB|  
||H:\Photo||  
|Second Drive|H:\Video2|2.5TB + 35GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
 In addition, you can set the following metadata for all files:  
  
-   **UploadMethod:** Windows Azure Import/Export Service  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
 To set metadata for the imported files, create a text file, `c:\WAImportExport\SampleMetadata.txt`, with the following content:  
  
```  
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```  
  
 You can also set some properties for the `FavoriteMovie.ISO` blob:  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
 To set these properties, create a text file, `c:\WAImportExport\SampleProperties.txt`:  
  
```  
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```  
  
 Now you are ready to run the Azure Import/Export tool to prepare the two hard drives. Note that:  
  
-   The first drive is mounted as drive X.  
  
-   The second drive is mounted as drive Y.  
  
-   The key for the storage account `mystorageaccount` is `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  
  
 For the first drive, run the Azure Import/Export tool twice to copy the two source directories:  
  
```  
  
      First copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
```  
  
      Second copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
 For the second drive, run the Azure Import/Export tool three times, once each for the source directories and once for the standalone Blu-Ray™ image file):  
  
```  
  
      First copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
```  
  
      Second copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
```  
  
      Third copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```  
  
 Once the copy sessions have completed, you can disconnect the two drives from the copy computer and ship them to the appropriate Windows Azure data center. You'll upload the two journal files, `FirstDrive.jrn` and `SecondDrive.jrn`, when you create the import job in the [Windows Azure Management Portal](https://manage.windowsazure.com/).  
  
## See Also  
 [Preparing Hard Drives for an Import Job](../importexport/Preparing-Hard-Drives-for-an-Import-Job.md)   
 [Quick Reference for Frequently Used Commands](../importexport/Quick-Reference-for-Frequently-Used-Commands-for-Import-Jobs.md)