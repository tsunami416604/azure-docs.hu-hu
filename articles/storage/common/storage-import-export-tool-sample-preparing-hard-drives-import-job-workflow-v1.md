---
title: Munkafolyamat-minta a merevlemezek Azure Import/Export importálási feladatokhoz - v1-előkészítési |} A Microsoft Docs
description: A teljes folyamat meghajtók előkészítése importálási feladatokhoz az Azure Import/Export szolgáltatás egy bemutató témakörben talál.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: ae792df428d897277e15df9db3ff6f99a5b8859e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527288"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére
Ez a témakör végigvezeti a meghajtók előkészítése importálási feladatokhoz teljes folyamatán.  
  
Ebben a példában a következő adatokat importál nevű ablak Azure storage-fiók `mystorageaccount`:  
  
|Hely|Leírás|  
|--------------|-----------------|  
|H:\Video|Gyűjteménye, videók, a teljes 5 TB.|  
|H:\Photo|Egy gyűjtemény fényképek, összesen 30 GB.|  
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ lemezképét, 25 GB.|  
|\\\bigshare\john\music|Letilthatja a zenei fájlok egy hálózati megosztáson, összesen 10 GB-os gyűjteménye.|  
  
Az importálási feladat ezeket az adatokat importál a storage-fiókban a következő helyre:  
  
|Forrás|Cél virtuális könyvtárat vagy a blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
A leképezés, a fájl a `H:\Video\Drama\GreatMovie.mov` importálva lett-e a blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Ezután annak megállapításához, hogy hány merevlemezek szükségesek, számítási az adatok mérete:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Ebben a példában két 3 TB-os merevlemezeket elegendőnek kell lennie. Mivel azonban a forráskönyvtár `H:\Video` 5 TB-nyi adatot, és a egy merevlemez-területtel csak 3 TB, érvényteleníteni szükséges `H:\Video` be két kisebb méretű könyvtárak: `H:\Video1` és `H:\Video2`, mielőtt futtatná a Microsoft Azure Import/Export eszköz. Ebben a lépésben a következő forrás-könyvtárak eredményez:  
  
|Hely|Méret|Cél virtuális könyvtárat vagy a blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 Annak ellenére, hogy a `H:\Video`directory felosztott két címtárat, hogy a cél virtuális könyvtárba a tárfiókban lévő mutassanak. Így minden videofájlok karbantartása alatt egyetlen `video` a storage-fiókban lévő tárolóba.  
  
 Az előző forrás könyvtárakat ezután egyenletesen lesznek elosztva a két merevlemez-meghajtók:  
  
||||  
|-|-|-|  
|Merevlemez-meghajtó|Forrás könyvtárak|Teljes méret|  
|Első meghajtó|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Második meghajtó|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Emellett állíthatja be a következő metaadatokat az összes fájl:  
  
-   **UploadMethod:** Windows Azure Import/Export szolgáltatás  
  
-   **DataSetName:** SampleData  
  
-   **A CreationDate:** 10/1/2013  
  
Az importált fájlok metaadatait beállításához hozzon létre egy szövegfájlt, `c:\WAImportExport\SampleMetadata.txt`, az alábbi tartalommal:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Az egyes tulajdonságok is beállíthat a `FavoriteMovie.ISO` blob:  
  
-   **Content-Type:** application/octet-stream  
  
-   **Tartalom-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==  
  
-   **A Cache-Control:** no-cache  
  
Ezek a tulajdonságok, hozzon létre egy szövegfájlt `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Most már készen áll a két merevlemez-meghajtók előkészítése az Azure Import/Export eszköz futtatásához. Vegye figyelembe:  
  
-   Az első meghajtó X meghajtóként van csatlakoztatva.  
  
-   A második meghajtó csatlakoztatva van, mint Y-meghajtó.  
  
-   A tárfiók kulcsának `mystorageaccount` van `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Előkészítés importáláshoz lemez, amikor adatokat előzetesen fel lett töltve
 
 Ha importálni az adatokat már megtalálható a lemezen, használja a jelző /skipwrite. /T és /srcdir értékét kell mindkét pontot a lemezen való importálás. Ha importálni az adatok nem lesz azonos cél virtuális könyvtárat vagy a storage-fiók gyökérmappájában, futtassa ugyanezt a parancsot az egyes célkönyvtárban duplikátum külön-külön tartja/ID értéke ugyanaz, minden futtatás során.

>[!NOTE] 
>Ne adjon meg Format módon törli az adatokat a lemezen. Adja meg, / titkosítása vagy /bk attól függően, hogy a lemez már titkosítva van-e. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Másolja a munkamenet - először meghajtó

Az első meghajtó futtassa az Azure Import/Export eszköz kétszer átmásolása a két forrás könyvtárak:  

**Először másolja a munkamenet**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Második másolási munkamenet**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Másolja a munkamenet - meghajtó a második
 
A második meghajtó futtatása az Azure Import/Export eszköz három alkalommal, egyszer minden egyes a forrás-könyvtárak, és egyszer az önálló Blu-Ray™ lemezképfájl):  
  
**Először másolja a munkamenet** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Második másolási munkamenet**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Harmadik másolatot munkamenet**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Másolja a munkamenet befejezése

Miután elvégezte a másolási munkamenetek, a másolási számítógép bontsa a kapcsolatot a két meghajtó, és küldje el azokat a megfelelő Windows Azure-adatközpontok a. A két naplófájlok feltöltése `FirstDrive.jrn` és `SecondDrive.jrn`az import-feladat létrehozásakor, a [az Azure portal](https://portal.azure.com).  
  
## <a name="next-steps"></a>További lépések

* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Használt gyakori parancsok gyors áttekintése](../storage-import-export-tool-quick-reference-v1.md) 
