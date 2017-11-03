---
title: "Adatok előkészítése az Azure Import/Export importálási feladat - v1 merevlemezeit, a minta-munkafolyamat |} Microsoft Docs"
description: "Lásd: a forgatókönyv a teljes folyamat meghajtók előkészítése az Azure Import/Export szolgáltatás egy importálási feladat."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 179c6bac9a2d9509baa0007a7008d75d0874a25e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére
Ez a témakör végigvezeti a teljes folyamat meghajtók előkészítése az importálási feladat.  
  
Ebben a példában a következő adatok importálása a Windows Azure storage-fiók nevű `mystorageaccount`:  
  
|Hely|Leírás|  
|--------------|-----------------|  
|H:\Video|Gyűjteménye videók, összesen 5 TB.|  
|H:\Photo|Egy gyűjtemény fényképek, összesen 30 GB.|  
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ lemezképét, 25 GB.|  
|\\\bigshare\john\music|Letilthatja a zenei fájlok egy hálózati megosztáson, 10 GB-os teljes gyűjteményét.|  
  
Az importálási feladat ezeket az adatokat importál a storage-fiókot a következő célhoz:  
  
|Forrás|Cél virtuális könyvtárat vagy blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Photo|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.BLOB.Core.Windows.NET/Music|  
  
A hozzárendelést, a fájl a `H:\Video\Drama\GreatMovie.mov` importálni lehet az blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Ezt követően annak megállapításához, hogy hány merevlemezek szükségesek, számítási az adatok mérete:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Az ebben a példában két 3 TB merevlemezek elegendőnek kell lennie. Mivel azonban a forráskönyvtár `H:\Video` 5 TB adatot, és az egyetlen merevlemez-területtel csak 3 TB szükséges hibájához `H:\Video` be két kisebb könyvtárak: `H:\Video1` és `H:\Video2`, a Microsoft Azure futtatása előtt Import/Export eszköz. Ebben a lépésben adja eredményül a következő forrás-könyvtárak:  
  
|Hely|Méret|Cél virtuális könyvtárat vagy blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Photo|30 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Music|  
  
 Annak ellenére, hogy a `H:\Video`directory felosztott két könyvtárak a cél virtuális könyvtárba a tárfiókban lévő pontok. Így minden videofájlok megmaradnak az egyetlen `video` a tárfiókban lévő tároló.  
  
 Ezt követően az előző forrás könyvtárak egyenletesen oszlanak a két merevlemez-meghajtóval:  
  
||||  
|-|-|-|  
|Merevlemez-meghajtó|Forrás könyvtárak|Teljes méret|  
|Első meghajtó|H:\Video1|2.5 TB + 30 GB|  
||H:\Photo||  
|Második meghajtó|H:\Video2|2.5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Ezenkívül a következő metaadatokat az összes fájl állíthatja be:  
  
-   **UploadMethod:** Windows Azure Import/Export szolgáltatás  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
Ha szeretné beállítani az importált fájlok metaadatait, hozzon létre egy szövegfájlt, `c:\WAImportExport\SampleMetadata.txt`, a következő tartalommal:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Bizonyos tulajdonságait is beállíthat a `FavoriteMovie.ISO` blob:  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==  
  
-   **A Cache-Control:** no-cache  
  
A tulajdonságok beállításáról, hozzon létre egy szövegfájlt `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Most már készen áll a két merevlemez-meghajtók előkészítése az Azure Import/Export eszköz futtatásához. Vegye figyelembe:  
  
-   Az első meghajtó X meghajtóként lett-e csatlakoztatva.  
  
-   A második meghajtó Y meghajtóként lett-e csatlakoztatva.  
  
-   A tárfiók kulcsa `mystorageaccount` van `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Lemez előkészítése importálása, amikor az adatok előzetes betöltése
 
 Ha az adatokat, importálandók már megtalálható a lemezen, használja a jelző /skipwrite. /T és /srcdir értékét kell mindkét pontot a lemezen való importálás. Ha minden az adatok, importálandók nem lesz azonos a cél virtuális könyvtár vagy a storage-fiók gyökérkönyvtárában, futtathatja ugyanezt a parancsot minden célként megadott könyvtár külön-külön tartása /id értéke azonos keresztül minden futtatásakor.

>[!NOTE] 
>Ne adjon meg Format, azt fogja törölni az adatokat a lemezen. Adja meg, / titkosítása vagy /bk attól függően, hogy a lemez már titkosítva van-e. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Másolja a munkamenet - először meghajtó

Az első meghajtó futtassa az Azure Import/Export eszköz kétszer a két forrás könyvtárak másolása:  

**Először másolja az munkamenet**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Második példány munkamenet**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Másolja a munkamenet - meghajtó másodpercenként
 
A második meghajtó, futtassa az Azure Import/Export eszköz háromszorosa, egyszer minden egyes a forrás-könyvtárakhoz, és egyszer az önálló Blu-Ray™ lemezképfájl):  
  
**Először másolja az munkamenet** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Második példány munkamenet**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Harmadik másolatot munkamenet**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Másolja a munkamenet befejezése

Miután végzett a másolat munkamenetek, a két meghajtók leválasztása a másolási számítógépről, és küldje el azokat a megfelelő Windows Azure adatközpontba. A két Adatbázisnapló-fájlok feltöltése `FirstDrive.jrn` és `SecondDrive.jrn`, az importálási feladat létrehozásakor a [Windows Azure portálon](https://manage.windowsazure.com/).  
  
## <a name="next-steps"></a>Következő lépések

* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [A gyakran használt parancsok rövid összefoglalása](../storage-import-export-tool-quick-reference-v1.md) 
