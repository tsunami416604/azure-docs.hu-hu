---
title: Azure Data Box Disk – hibaelhárítás | Microsoft Docs
description: Ez a cikk az Azure Data Box Disk szolgáltatásban jelentkező hibák elhárítását írja le.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805709"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Naplók használata az Azure Data Box Disk érvényesítési problémáinak elhárításához

Ez a cikk a Microsoft Azure Data Box Disk lemezre vonatkozik. A cikk ismerteti, hogyan használhatja a naplókat a megoldás telepítésekor látható érvényesítési problémák elhárításához.

## <a name="validation-tool-log-files"></a>Érvényesítési eszköz naplófájljai

Ha az [érvényesítési eszközzel](data-box-disk-deploy-copy-data.md#validate-data)érvényesíti a lemezeken lévő adatokat, a rendszer *hiba.xml* fájlt hoz létre a hibák naplózásához. A naplófájl a `Drive:\DataBoxDiskImport\logs` meghajtó mappájában található. Az érvényesítés futtatásakor a hibanaplóra mutató hivatkozás található.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Ha több munkamenetet futtat ellenőrzésre, munkamenetenként egy hibanapló jön létre.

- Itt van egy példa a hibanapló, `PageBlob` ha az adatok betöltve a mappába nem 512 bájt igazodik. A PageBlob-ba feltöltött adatoknak 512 bájtosnak kell lenniük, például egy Virtuális merevlemezvagy VHDX. A fájlban lévő hibák `<Errors>` a `<Warnings>`és a figyelmeztetések a ban találhatók.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Itt van egy példa a hibanapló, ha a tároló neve érvénytelen. A , `BlockBlob` `PageBlob`vagy `AzureFile` a lemezen lévő mappák alatt létrehozott mappa az Azure Storage-fiók tárolójává válik. A tároló nevének követnie kell az [Azure elnevezési konvencióit.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Érvényesítési eszköz hibái

Az *error.xml* fájlban található hibákat a megfelelő ajánlott műveletekkel az alábbi táblázat foglalja össze.

| Hibakód| Leírás                       | Ajánlott műveletek               |
|------------|--------------------------|-----------------------------------|
| `None` | Sikeresen érvényesítette az adatokat. | Semmit nem kell tenni. |
| `InvalidXmlCharsInPath` |Nem sikerült létrehozni egy jegyzékfájlt, mert a fájl elérési útja érvénytelen karaktereket tartalmaz. | A folytatáshoz távolítsa el ezeket a karaktereket.  |
| `OpenFileForReadFailed`| A fájl nem dolgozható fel. Ennek oka hozzáférési probléma vagy fájlrendszersérülés lehet.|Hiba miatt nem olvasható a fájl. A hiba részletei a kivételben találhatók. |
| `Not512Aligned` | Ez a fájl nem érvényes formátumú a PageBlob mappához.| Csak a mappához igazított 512 `PageBlob` bájtos adatokat töltse fel. Távolítsa el a fájlt a PageBlob mappából, vagy helyezze át a BlockBlob mappába. Próbálkozzon újra az ellenőrzéssel.|
| `InvalidBlobPath` | A fájl elérési útja nem felel meg egy érvényes blob elérési úta a felhőben, mint az Azure Blob elnevezési konvenciók.|Kövesse az Azure elnevezési irányelveit a fájl elérési útjának átnevezéséhez. |
| `EnumerationError` | Nem lehet felsorolni a fájlt ellenőrzésre. |Ennek a hibának több oka is lehet. Ennek legvalószínűbb oka a fájlhoz való hozzáférés. |
| `ShareSizeExceeded` | Ez a fájl miatt az Azure fájlmegosztás mérete meghaladja az Azure-korlát 5 TB-os.|Csökkentse a megosztásban lévő adatok méretét, hogy azok megfeleljenek az [Azure-objektum méretkorlátainak.](data-box-disk-limits.md#azure-object-size-limits) Próbálkozzon újra az ellenőrzéssel. |
| `AzureFileSizeExceeded` | A fájlméret meghaladja az Azure-fájlméret-korlátokat.| Csökkentse a fájl vagy az adatok méretét, hogy azok megfeleljenek az [Azure-objektum méretkorlátainak.](data-box-disk-limits.md#azure-object-size-limits) Próbálkozzon újra az ellenőrzéssel.|
| `BlockBlobSizeExceeded` | A fájlméret meghaladja az Azure Block Blob méretkorlátját. | Csökkentse a fájl vagy az adatok méretét, hogy azok megfeleljenek az [Azure-objektum méretkorlátainak.](data-box-disk-limits.md#azure-object-size-limits) Próbálkozzon újra az ellenőrzéssel. |
| `ManagedDiskSizeExceeded` | A fájlméret meghaladja az Azure felügyelt lemez méretkorlátját. | Csökkentse a fájl vagy az adatok méretét, hogy azok megfeleljenek az [Azure-objektum méretkorlátainak.](data-box-disk-limits.md#azure-object-size-limits) Próbálkozzon újra az ellenőrzéssel. |
| `PageBlobSizeExceeded` | A fájlméret meghaladja az Azure felügyelt lemez méretkorlátját. | Csökkentse a fájl vagy az adatok méretét, hogy azok megfeleljenek az [Azure-objektum méretkorlátainak.](data-box-disk-limits.md#azure-object-size-limits) Próbálkozzon újra az ellenőrzéssel. |
| `InvalidShareContainerFormat`  |A címtárnevek nem felelnek meg a tárolók vagy megosztások Azure-elnevezési konvencióinak.         |A lemezen lévő, már meglévő mappák alatt létrehozott első mappa tárolóvá válik a tárfiókban. Ez a megosztás vagy tároló név nem felel meg az Azure elnevezési konvenciók. Nevezze át a fájlt úgy, hogy megfeleljen az [Azure elnevezési konvencióinak.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Próbálkozzon újra az ellenőrzéssel.   |
| `InvalidBlobNameFormat` | A fájl elérési útja nem felel meg egy érvényes blob elérési úta a felhőben, mint az Azure Blob elnevezési konvenciók.|Nevezze át a fájlt úgy, hogy megfeleljen az [Azure elnevezési konvencióinak.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Próbálkozzon újra az ellenőrzéssel. |
| `InvalidFileNameFormat` | A fájl elérési útja nem felel meg érvényes fájlelérési útnak a felhőben az Azure-fájlelnevezési konvenciók szerint. |Nevezze át a fájlt úgy, hogy megfeleljen az [Azure elnevezési konvencióinak.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Próbálkozzon újra az ellenőrzéssel. |
| `InvalidDiskNameFormat` | A fájl elérési útja nem felel meg érvényes lemeznévnek a felhőben az Azure felügyelt lemez elnevezési konvencióinak. |Nevezze át a fájlt úgy, hogy megfeleljen az [Azure elnevezési konvencióinak.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Próbálkozzon újra az ellenőrzéssel.       |
| `NotPartOfFileShare` | A fájlok feltöltési útvonala érvénytelen. Töltse fel a fájlokat egy mappába az Azure Files.   | Távolítsa el a hibás fájlokat, és töltse fel azokat egy előre létrehozott mappába. Próbálkozzon újra az ellenőrzéssel. |
| `NonVhdFileNotSupportedForManagedDisk` | Nem Virtuális merevlemezes fájl nem tölthető fel felügyelt lemezként. |Távolítsa el a nem `ManagedDisk` VHD-s fájlokat a mappából, mivel ezek nem támogatottak, vagy helyezze át ezeket a fájlokat egy `PageBlob` mappába. Próbálkozzon újra az ellenőrzéssel. |


## <a name="next-steps"></a>További lépések

- [Adatfeltöltési hibák](data-box-disk-troubleshoot-upload.md)elhárítása .
