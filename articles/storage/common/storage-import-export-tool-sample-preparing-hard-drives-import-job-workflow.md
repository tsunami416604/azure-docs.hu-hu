---
title: "Adatok előkészítése az Azure Import/Export importálási feladatnak merevlemezek minta munkafolyamat |} Microsoft Docs"
description: "Lásd: a forgatókönyv a teljes folyamat meghajtók előkészítése az Azure Import/Export szolgáltatás egy importálási feladat."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére

Ez a cikk végigvezeti a teljes folyamat meghajtók előkészítése az importálási feladat.

## <a name="sample-data"></a>Mintaadatok

Ebben a példában a következő adatokat importálja az Azure-tárfiók nevű `mystorageaccount`:

|Hely|Leírás|Adatok mérete|
|--------------|-----------------|-----|
|H:\Video\ |Videók gyűjteménye|12 TB|
|H:\Photo\ |Fotók|30 GB|
|K:\Temp\FavoriteMovie.ISO|A Blu-ray™ lemezképe|25 GB|
|\\\bigshare\john\music\ |Letilthatja a zenei fájlok egy hálózati megosztáson|10 GB|

## <a name="storage-account-destinations"></a>Tárolási fiók célok

Az importálási feladat az adatok importálása a storage-fiókot a következő célhoz:

|Forrás|Cél virtuális könyvtárat vagy blob|
|------------|-------------------------------------------|
|H:\Video\ |videó /|
|H:\Photo\ |fénykép vagy|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Zene|

A hozzárendelést, a fájl a `H:\Video\Drama\GreatMovie.mov` importálhatja, hogy a blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Merevlemez-meghajtóról követelmények meghatározása

Ezt követően annak megállapításához, hogy hány merevlemezek szükségesek, számítási az adatok mérete:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Az ebben a példában két 8 TB-os merevlemezeket elegendőnek kell lennie. Mivel azonban a forráskönyvtár `H:\Video` 12TB adatot, és az egyetlen merevlemez-területtel csak 8TB, adja meg a következő módon a ez lesz a **driveset.csv** fájlt:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Az eszköz fogja el az adatokat két merevlemez-meghajtók optimalizált módon.

## <a name="attach-drives-and-configure-the-job"></a>Meghajtók csatolja, és a feladat konfigurálása
Ön mindkét lemez csatolása a géphez, és hozzon létre köteteket. Majd szerzői **dataset.csv** fájlt:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Ezenkívül a következő metaadatokat az összes fájl állíthatja be:

* **UploadMethod:** Windows Azure Import/Export szolgáltatás
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

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

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==
* **A Cache-Control:** no-cache

A tulajdonságok beállításáról, hozzon létre egy szövegfájlt `c:\WAImportExport\SampleProperties.txt`:

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

További adatokat hozzá kell adni, ha hozzon létre egy másik dataset fájlt (Initialdataset megegyező formátumban).

**A második munkamenethez:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Miután végzett a másolat munkamenetek, a két meghajtók leválasztása a másolási számítógépről, és küldje el azokat a megfelelő Azure adatközpontba. A két Adatbázisnapló-fájlok feltöltése lesz `<FirstDriveSerialNumber>.xml` és `<SecondDriveSerialNumber>.xml`, az importálási feladat létrehozásakor az Azure portálon.

## <a name="next-steps"></a>Következő lépések

* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import.md)
* [A gyakran használt parancsok rövid összefoglalása](../storage-import-export-tool-quick-reference.md)
