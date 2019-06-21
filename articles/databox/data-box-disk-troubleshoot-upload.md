---
title: Az Azure Data Box-lemezek hibaelhárítási adatokat kapcsolatos problémákat naplók feltöltése |} A Microsoft Docs
description: A naplók és elhárítása problémáinak, amikor az adatok feltöltése az Azure Data Box-lemezek módját ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148308"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Az Azure Data Box-lemezek adatok feltöltési hibáinak elhárítása naplók megértése

Ez a cikk a Microsoft Azure Data Box-lemezek vonatkozik, és láthatja, mikor az Azure-bA feltöltött adatok problémákat ismerteti.

## <a name="data-upload-logs"></a>Adatok a naplók feltöltése

Ha az adatokat az Azure-bA feltöltött az Adatközpont `_error.xml` és `_verbose.xml` fájlok jönnek létre. Ezeket a naplókat a rendszer feltölti a tárfiókon, amellyel az adatok feltöltése. A minta a `_error.xml` alább találja.
    
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

## <a name="download-logs"></a>Naplók letöltése

Keresse meg és töltse le a diagnosztikai naplók két módja van.

- Ha bármilyen hiba merül fel, ha az adatok feltöltése az Azure-ba, a portálon azt a mappát, ahol a diagnosztikai naplók találhatók jeleníti meg egy elérési utat.

    ![A portálon a naplók csatolása](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Nyissa meg a Data Box-rendelését társított storage-fiókot. Lépjen a **Blob service > Browse blobs** (Blob szolgáltatás > Blobok tallózása) felületre, és keresse meg a tárfióknak megfelelő blobot. Lépjen a **waies**.

    ![Naplók másolása 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

Minden esetben tekintse meg a hibanaplókat, és a részletes naplókat. Válassza ki az egyes naplókhoz, és töltse le a helyi másolatot.


## <a name="data-upload-errors"></a>Adatok feltöltéssel kapcsolatos hibák

A hibák jönnek létre, ha az adatok feltöltése az Azure-bA az alábbi táblázat foglalja össze.

| Hibakód | Leírás                        |
|-------------|------------------------------|
|`None` |  Sikeresen befejeződött.           |
|`Renamed` | A blob az Átnevezés sikeres volt.  |                                                            |
|`CompletedWithErrors` | A feltöltés hibákkal fejeződött be. A fájlok a hiba részletei megtalálhatók a naplófájlban.  |
|`Corrupted`|Adatbetöltés során számított CRC nem felel meg a feltöltés számított CRC Értéknek.  |  
|`StorageRequestFailed` | Az Azure storage-kérés sikertelen volt.   |     |
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
