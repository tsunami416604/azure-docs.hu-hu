---
title: Az Azure Data Box-lemezek hibaelhárítási adatokat kapcsolatos problémákat naplók feltöltése |} A Microsoft Docs
description: A naplók és elhárítása problémáinak, amikor az adatok feltöltése az Azure Data Box-lemezek módját ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807496"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Az Azure Data Box-lemezek adatok feltöltési hibáinak elhárítása naplók megértése

Ez a cikk a Microsoft Azure Data Box-lemezek vonatkozik, és láthatja, mikor az Azure-bA feltöltött adatok problémákat ismerteti.

## <a name="about-upload-logs"></a>Tudnivalók a naplók feltöltése

Ha az adatokat az Azure-bA feltöltött az Adatközpont `_error.xml` és `_verbose.xml` fájlok jönnek létre minden olyan tárfiókhoz. Ezeket a naplókat a rendszer feltölti a tárfiókon, amellyel az adatok feltöltése. 

Mindkét a naplók ugyanebben a formátumban és az eseményeket, másolja az adatokat a lemezről az Azure Storage-fiók történt XML leírását tartalmazzák.

A részletes napló a másolási művelet minden egyes blob vagy fájlt, az állapot teljes körű információkat tartalmaz, mivel a hibanapló csak a blobok vagy fájlokat, amelyek hibába ütközött a feltöltés során adatait tartalmazza.

A hibanapló szerkezete ugyanaz, mint a részletes napló, de a sikeres műveletek kiszűri rendelkezik.

## <a name="download-logs"></a>Naplók letöltése

Az alábbi lépéseket, keresse meg a naplók feltöltése.

1. Ha bármilyen hiba merül fel, ha az adatok feltöltése az Azure-ba, a portálon azt a mappát, ahol a diagnosztikai naplók találhatók jeleníti meg egy elérési utat.

    ![A portálon a naplók csatolása](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Lépjen a **waies**.

    ![Hiba történt, és részletes naplók](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Minden esetben tekintse meg a hibanaplókat, és a részletes naplókat. Válassza ki az egyes naplókhoz, és töltse le a helyi másolatot.

## <a name="sample-upload-logs"></a>Minta naplók feltöltése

A minta a `_verbose.xml` alább találja. Ebben az esetben a rendelés sikeresen befejeződött hiba nélkül.

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

Az ugyanabban a sorrendben, egy mintát a `_error.xml` alább találja.

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

A minta a `_error.xml` közül, where a rendelés hibákkal fejeződött be. 

A hiba történt a fájl ebben az esetben van egy `Summary` és, amely tartalmazza az összes fájlt egy másik szakaszban szintű hibákat. 

A `Summary` tartalmazza a `ValidationErrors` és a `CopyErrors`. Ebben az esetben 8 fájlok vagy mappák feltöltése az Azure-ba, és nincs érvényesítési hiba történt. Ha az adatok Azure Storage-fiók másolta, 5 fájlok vagy mappák feltöltése sikerült. A fennmaradó 3 fájlokat vagy mappákat is megfelelően az Azure container elnevezési konvencióinak átnevezve, és majd feltöltése sikeresen befejeződött az Azure-bA.

A fájlállapot szintű szerepelnek `BlobStatus` , amely ismerteti a blobfeltöltéshez végrehajtott műveletek. Ebben az esetben a három tárolót is átnevezni, mert nem felelt meg a mappákat, amelyhez az adatokat a rendszer átmásolta-tárolókhoz az Azure elnevezési konvencióinak. Az ezen tárolókban feltöltött blobok az új tároló nevét, az Azure-ban a blob elérési útja, érvénytelen fájl eredeti elérési útja és a blob mérete szerepelnek.
    
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

## <a name="data-upload-errors"></a>Adatok feltöltéssel kapcsolatos hibák

A hibák jönnek létre, ha az adatok feltöltése az Azure-bA az alábbi táblázat foglalja össze.

| Hibakód | Leírás                   |
|-------------|------------------------------|
|`None` |  Sikeresen befejeződött.           |
|`Renamed` | A blob az Átnevezés sikeres volt.   |
|`CompletedWithErrors` | A feltöltés hibákkal fejeződött be. A fájlok a hiba részletei megtalálhatók a naplófájlban.  |
|`Corrupted`|Adatbetöltés során számított CRC nem felel meg a feltöltés számított CRC Értéknek.  |  
|`StorageRequestFailed` | Az Azure storage-kérés sikertelen volt.   |     
|`LeasePresent` | Ez az elem egy bérelt és a egy másik felhasználó használja. |
|`StorageRequestForbidden` |Nem sikerült feltölteni a hitelesítési problémák miatt. |
|`ManagedDiskCreationTerminalFailure` | Nem sikerült feltölteni a felügyelt lemezként. A fájlok és az előkészítési tárfiókból érhető el. A lapblobok manuálisan átalakítása felügyelt lemezeket.  |
|`DiskConversionNotStartedTierInfoMissing` | A VHD-fájlt a precreated szint mappák kívül másolták, mivel nincs létrehozva. egy felügyelt lemezt. Feltöltötte a fájlt, a megadott átmeneti tárfiókban lapblob rendelés létrehozása során. Átalakíthatja manuálisan egy felügyelt lemezre.|
|`InvalidWorkitem` | Nem sikerült feltölteni az adatokat, mert nem felel meg az Azure elnevezési és konvenciói korlátozza.|
|`InvalidPageBlobUploadAsBlockBlob` | Előtaggal rendelkező tárolóban blokkblobként feltölteni `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Előtaggal rendelkező tárolóban blokkblobként feltölteni `databoxdisk-invalid-af`–.|
|`InvalidManagedDiskUploadAsBlockBlob` | Előtaggal rendelkező tárolóban blokkblobként feltölteni `databoxdisk-invalid-md`–.|
|`InvalidManagedDiskUploadAsPageBlob` |Feltöltött előtaggal rendelkező tárolóban lapblobként `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Feltöltött fájlokat egy új megosztást, mint az eredeti megosztás mérete túllépte a maximális mérete legfeljebb az Azure. Az új megosztás nevét az eredeti nevén utótaggal rendelkezik `-2`.   |
|`MovedToDefaultAzureShare` |Nem tartozik egy alapértelmezett megosztást tetszőleges mappájába feltöltött fájlokat. A megosztás neve kezdődik `databox-`. |
|`ContainerRenamed` |A fájlok a tároló nem felel meg az Azure elnevezési konvencióinak, és valaki új nevet. Az új neve kezdődik `databox-` és a rendszer az eredeti nevén SHA1 kivonatát utótaggal |
|`ShareRenamed` |A megosztáshoz a fájlok nem felelnek meg az Azure elnevezési konvencióinak, és új neve. Az új neve kezdődik `databox-` és a rendszer az eredeti nevén SHA1 kivonatát utótaggal. |
|`BlobRenamed` |Ezek a fájlok nem felelnek meg az Azure elnevezési konvencióinak, és lett átnevezve. Ellenőrizze a `BlobPath` az új név mezőt. |
|`FileRenamed` |Ezek a fájlok nem felelnek meg az Azure elnevezési konvencióinak, és lett átnevezve. Ellenőrizze a `FileStoragePath` az új név mezőt. |
|`DiskRenamed` |Ezek a fájlok nem felelnek meg az Azure elnevezési konvencióinak, és lett átnevezve. Ellenőrizze a `BlobPath` az új név mezőt. |


## <a name="next-steps"></a>További lépések

- [Nyisson egy támogatási jegyet a Data Box-lemezek problémák](data-box-disk-contact-microsoft-support.md).
