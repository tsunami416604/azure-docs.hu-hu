---
title: Adatfeltöltések hibáinak elhárítása naplók használatával
titleSuffix: Azure Data Box Disk
description: Bemutatja, hogyan használhatja a naplókat, és az Azure Data Box Disk-re történő adatok feltöltésekén látható problémák elhárítása.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260136"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Az Azure Data Box Disk adatfeltöltési problémáinak elhárításához szükséges naplók ismertetése

Ez a cikk a Microsoft Azure Data Box Disk lemezre vonatkozik, és ismerteti az okat, amikor adatokat tölt fel az Azure-ba.

## <a name="about-upload-logs"></a>A feltöltési naplók – beteke

Amikor az adatok feltöltése az Azure-ba az adatközpontban, `_error.xml` és `_verbose.xml` a fájlok jönnek létre az egyes tárfiókhoz. Ezek a naplók feltöltése ugyanabba a tárfiókba, amely az adatok feltöltéséhez használt. 

Mindkét naplók ugyanabban a formátumban, és xml-leírást tartalmaznak az események, amelyek az adatok másolása közben a lemezről az Azure Storage-fiókba.

A részletes napló teljes körű információt tartalmaz a másolási művelet állapotáról minden blob vagy fájl esetében, míg a hibanapló csak a blobok vagy a feltöltés során hibákat észlelt fájlok adatait tartalmazza.

A hibanapló szerkezete megegyezik a részletes napló szerkezetének, de kiszűri a sikeres műveleteket.

## <a name="download-logs"></a>Naplók letöltése

A feltöltési naplók megkereséséhez tegye a következő lépéseket.

1. Ha bármilyen hiba merül fel az adatok Azure-ba való feltöltéseksorán, a portál megjeleníti a diagnosztikai naplókat tartalmazó mappához vezető elérési utat.

    ![Hivatkozás a portálon található naplókra](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Tovább a **waies**.

    ![hiba- és részletes naplók](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Minden esetben a hibanaplók és a részletes naplók jelennek meg. Jelölje ki az egyes naplókat, és töltsön le egy helyi példányt.

## <a name="sample-upload-logs"></a>Mintafeltöltési naplók

Az alábbi `_verbose.xml` minta látható. Ebben az esetben a rendelés hiba nélkül sikeresen befejeződött.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Ugyanebben a sorrendben az `_error.xml` alábbiakban egy minta látható.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Az alábbi `_error.xml` minta az alábbiakban látható, ahol a sorrend hibákkal fejeződött be. 

Ebben az esetben a `Summary` hibafájlhoz van egy szakasz és egy másik szakasz, amely tartalmazza az összes fájlszintű hibát. 

A `Summary` és `ValidationErrors` a `CopyErrors`és a . Ebben az esetben 8 fájlt vagy mappát töltöttek fel az Azure-ba, és nem voltak érvényesítési hibák. Amikor az adatokat az Azure Storage-fiókba másolta, 5 fájl vagy mappa feltöltése sikeresen. A fennmaradó 3 fájlokat vagy mappákat átnevezték az Azure-tároló elnevezési konvenciók, majd sikeresen feltöltötte az Azure-ba.

A fájlszint állapota, `BlobStatus` amely leírja a blobok feltöltéséhez tett műveleteket. Ebben az esetben három tárolók átnevezése, mert a mappák, amelyekaz adatok másolása nem felel meg az Azure-elnevezési konvenciók tárolók. Az ezekben a tárolókban feltöltött blobok esetében az új tároló neve, az Azure-beli blob elérési útja, az eredeti érvénytelen fájlelérési út és a blob mérete szerepelnek.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Adatfeltöltési hibák

Az adatok Azure-ba való feltöltésekor keletkezett hibákat az alábbi táblázat foglalja össze.

| Hibakód | Leírás                   |
|-------------|------------------------------|
|`None` |  Sikeresen befejeződött.           |
|`Renamed` | Sikeresen átnevezte a blobot.   |
|`CompletedWithErrors` | A feltöltés hibákkal fejeződött be. A hibás fájlok részletei a naplófájlban találhatók.  |
|`Corrupted`|Az adatbetöltés során kiszámított CRC nem egyezik meg a feltöltés során kiszámított CRC-vel.  |  
|`StorageRequestFailed` | Az Azure storage-kérelem sikertelen.   |     
|`LeasePresent` | Ez az elem bérelt, és egy másik felhasználó használja. |
|`StorageRequestForbidden` |Hitelesítési problémák miatt nem sikerült feltölteni. |
|`ManagedDiskCreationTerminalFailure` | Nem sikerült felügyelt lemezként feltölteni. A fájlok lapblobként érhetők el az átmeneti tárfiókban. A lapblobokat manuálisan konvertálhatja felügyelt lemezekké.  |
|`DiskConversionNotStartedTierInfoMissing` | Mivel a VHD-fájlt az előre létrehozott rétegmappákon kívülre másolták, nem jött létre felügyelt lemez. A fájl feltöltése lapblobként az átmeneti tárfiókba a rendelés létrehozása során megadott módon. Manuálisan is konvertálható felügyelt lemezzé.|
|`InvalidWorkitem` | Nem sikerült feltölteni az adatokat, mert nem felel meg az Azure elnevezési és korlátok konvenciók.|
|`InvalidPageBlobUploadAsBlockBlob` | Blokkblobként feltöltve egy tárolóban `databoxdisk-invalid-pb-`előtaggal.|
|`InvalidAzureFileUploadAsBlockBlob` | Blokkblobként feltöltve egy `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Blokkblobként feltöltve egy `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Oldalblobként feltöltve egy tárolóban `databoxdisk-invalid-md-`előtaggal. |
|`MovedToOverflowShare` |Feltöltött fájlokat egy új megosztásra, mivel az eredeti megosztási méret meghaladta a maximális Azure-méretkorlátot. Az új fájlmegosztási név eredeti neve `-2`a.   |
|`MovedToDefaultAzureShare` |Olyan feltöltött fájlok, amelyek nem voltak egyetlen mappa részét sem, egy alapértelmezett megosztáshoz. A megosztás neve `databox-`a következővel kezdődik: . |
|`ContainerRenamed` |Ezek a fájlok tárolója nem felel meg az Azure elnevezési konvencióknak, és átnevezi. Az új név `databox-` az eredeti név SHA1 kivonatával kezdődik és van utótaggal |
|`ShareRenamed` |Ezek a fájlok megosztása nem felel meg az Azure elnevezési konvencióknak, és átnevezi. Az új név `databox-` az eredeti név SHA1 kivonatával kezdődik, és utótag. |
|`BlobRenamed` |Ezek a fájlok nem feleltek meg az Azure elnevezési konvencióinak, és átnevezték őket. Ellenőrizze `BlobPath` a mezőben az új nevet. |
|`FileRenamed` |Ezek a fájlok nem feleltek meg az Azure elnevezési konvencióinak, és átnevezték őket. Ellenőrizze `FileStoragePath` a mezőben az új nevet. |
|`DiskRenamed` |Ezek a fájlok nem feleltek meg az Azure elnevezési konvencióinak, és átnevezték őket. Ellenőrizze `BlobPath` a mezőben az új nevet. |


## <a name="next-steps"></a>További lépések

- [Nyisson meg egy támogatási jegyet a Data Box Disk problémákhoz.](data-box-disk-contact-microsoft-support.md)
