---
title: Munkafolyamat-minta a merevlemezek importálási feladatokhoz az Azure Import/Export-előkészítési |} A Microsoft Docs
description: A teljes folyamat meghajtók előkészítése importálási feladatokhoz az Azure Import/Export szolgáltatás egy bemutató témakörben talál.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 42da285fbb55df43959506996bcde9cf547c2a22
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439261"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére

Ez a cikk végigvezeti a meghajtók előkészítése importálási feladatokhoz teljes folyamatán.

## <a name="sample-data"></a>Mintaadatok

Ebben a példában a következő adatokat importál az Azure storage-fiók nevű `mystorageaccount`:

|Hely|Leírás|Adatok mérete|
|--------------|-----------------|-----|
|H:\Video\ |Videók gyűjteménye|12 TB|
|H:\Photo\ |Fényképek gyűjteménye|30 GB|
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ lemezképe|25 GB|
|\\\bigshare\john\music\ |A hálózati megosztás letilthatja a zenei fájlok gyűjteménye|10 GB|

## <a name="storage-account-destinations"></a>Storage-fiók célhelyek

Az importálási feladatot az adatok importálása a storage-fiókban a következő helyre:

|Forrás|Cél virtuális könyvtárat vagy a blob|
|------------|-------------------------------------------|
|H:\Video\ |videó /|
|H:\Photo\ |fénykép /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Zene|

A leképezés, a fájl a `H:\Video\Drama\GreatMovie.mov` importálhatja, hogy a blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Merevlemez követelmények meghatározása

Ezután annak megállapításához, hogy hány merevlemezek szükségesek, számítási az adatok mérete:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Ebben a példában két 8TB merevlemez-meghajtók elegendőnek kell lennie. Mivel azonban a forráskönyvtár `H:\Video` 12TB adat rendelkezik, és a egy merevlemez-területtel csak 8TB, adja meg a következő módszere lesz a **driveset.csv** fájlt:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Az eszköz között az adatokat különböző két merevlemez-meghajtók optimalizált módon.

## <a name="attach-drives-and-configure-the-job"></a>Csatlakoztathat meghajtókat, valamint a feladat
A két lemez csatolása a gép lesz, és hozzon létre köteteket. Ezután hozhat létre **dataset.csv** fájlt:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Emellett állíthatja be a következő metaadatokat az összes fájl:

* **UploadMethod:** Windows Azure Import/Export szolgáltatás
* **DataSetName:** SampleData
* **A CreationDate:** 10/1/2013

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

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **A Cache-Control:** no-cache

Ezek a tulajdonságok, hozzon létre egy szövegfájlt `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Futtassa az Azure Import/Export eszköz (WAImportExport.exe)

Most már készen áll a két merevlemez-meghajtók előkészítése az Azure Import/Export eszköz futtatásához.

**Az első munkamenet:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Ha további adatokat hozzá kell adnia, hozzon létre egy másik adatkészletfájlt (kezdeti adatkészlet megegyező formátumban).

**A második munkamenethez:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Miután elvégezte a másolási munkamenetek, a másolási számítógép bontsa a kapcsolatot a két meghajtó, és küldje el azokat a megfelelő Azure-adatközpontban a. A két naplófájl, feltölthet `<FirstDriveSerialNumber>.xml` és `<SecondDriveSerialNumber>.xml`, amikor az importálási feladatot hoz létre az Azure Portalon.

## <a name="next-steps"></a>További lépések

* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Használt gyakori parancsok gyors áttekintése](../storage-import-export-tool-quick-reference.md)
