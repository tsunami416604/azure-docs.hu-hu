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
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805709"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Naplók használata az Azure Data Box-lemezek érvényesítési problémák elhárítása

Ez a cikk a Microsoft Azure Data Box-lemezek vonatkozik. A cikk bemutatja, hogyan sikerült látja, ez a megoldás üzembe helyezésekor érvényesítési hibaelhárítás a naplók használatával.

## <a name="validation-tool-log-files"></a>Érvényesítési eszköz naplófájljai

A lemezek segítségével az adatok minősíti a [fürtérvényesítési eszköz](data-box-disk-deploy-copy-data.md#validate-data), egy *error.xml* jön létre, hibák jelentkezhetnek. A naplófájl található a `Drive:\DataBoxDiskImport\logs` mappát a meghajtó. Egy, a hibanapló hivatkozást ellenőrzés futtatásakor.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Ha ellenőrzés céljából több munkamenet futtatja, majd egy hibanaplóját munkamenetenként jön létre.

- Íme egy példa, hibanapló, amikor betölti az adatokat a `PageBlob` mappa nem 512 bájtos igazítva. Bármely PageBlob feltöltött adatok 512-bájt igazítva, például egy VHD vagy VHDX lehet. Ebben a fájlban a hibák szerepelnek a `<Errors>` és figyelmeztetéseket a `<Warnings>`.

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

- Ha a tároló neve nem érvényes, Íme egy példa, a hibanaplót. A mappa alatt létrehozott `BlockBlob`, `PageBlob`, vagy `AzureFile` a lemezen található mappák válik egy tárolót az Azure Storage-fiókját. A tároló nevét hajtsa végre a [elnevezési konvenciók Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Érvényesség-ellenőrzési eszköz hibák

A hibák található a *error.xml* az alábbi táblázat foglalja össze a megfelelő ajánlott műveletek.

| Hibakód| Leírás                       | Ajánlott műveletek               |
|------------|--------------------------|-----------------------------------|
| `None` | Sikerült ellenőrizni az adatokat. | Semmit nem kell tenni. |
| `InvalidXmlCharsInPath` |Nem sikerült létrehozni a jegyzékfájlt, mert a fájl elérési útja nem érvényes karaktereket. | Távolítsa el ezeket a karaktereket a folytatáshoz.  |
| `OpenFileForReadFailed`| Nem sikerült feldolgozni a fájlt. Ezt okozhatja egy hozzáférési probléma vagy a fájl sérülése.|Egy hiba miatt a fájl nem olvasható be. A hiba részleteit a kivétel találhatók. |
| `Not512Aligned` | A fájl nem PageBlob mappa formátuma érvénytelen.| Csak olyan feltöltési adatkeret 512 bájtos igazodik `PageBlob` mappát. Távolítsa el a fájlt a PageBlob, vagy helyezze át a BlockBlob mappát. Ismételje meg az ellenőrzést.|
| `InvalidBlobPath` | Fájl elérési útja nem egy érvényes blob elérési utat a felhőbeli az elnevezési konvenciók Azure Blob képezze le.|Kövesse a az Azure elnevezési irányelvei átnevezése a fájl elérési útját. |
| `EnumerationError` | Nem tudta számba venni a fájl ellenőrzés céljából. |Ennek a hibának számos oka lehet. A legvalószínűbb oka a fájlhoz való hozzáférés. |
| `ShareSizeExceeded` | Ez a fájl az Azure legfeljebb 5 TB-os az Azure fájlmegosztás méretének okozza.|Az adatok a megosztás méretének csökkentése, hogy megfelel a [Azure objektum méretbeli korlátokat](data-box-disk-limits.md#azure-object-size-limits). Ismételje meg az ellenőrzést. |
| `AzureFileSizeExceeded` | Fájl mérete meghaladja az Azure File méretbeli korlátokat.| A fájl vagy az adatok méretének csökkentése, hogy megfelel a [Azure objektum méretbeli korlátokat](data-box-disk-limits.md#azure-object-size-limits). Ismételje meg az ellenőrzést.|
| `BlockBlobSizeExceeded` | Fájl mérete meghaladja az Azure Blokkblob méretbeli korlátokat. | A fájl vagy az adatok méretének csökkentése, hogy megfelel a [Azure objektum méretbeli korlátokat](data-box-disk-limits.md#azure-object-size-limits). Ismételje meg az ellenőrzést. |
| `ManagedDiskSizeExceeded` | Fájl mérete meghaladja az Azure Managed Disk méretbeli korlátokat. | A fájl vagy az adatok méretének csökkentése, hogy megfelel a [Azure objektum méretbeli korlátokat](data-box-disk-limits.md#azure-object-size-limits). Ismételje meg az ellenőrzést. |
| `PageBlobSizeExceeded` | Fájl mérete meghaladja az Azure Managed Disk méretbeli korlátokat. | A fájl vagy az adatok méretének csökkentése, hogy megfelel a [Azure objektum méretbeli korlátokat](data-box-disk-limits.md#azure-object-size-limits). Ismételje meg az ellenőrzést. |
| `InvalidShareContainerFormat`  |Az Azure elnevezési konvencióinak, tárolók vagy megosztások nem felelnek meg a könyvtár nevét.         |Az első mappa a lemezen már meglévő mappákban létrehozott egy tárolót a tárfiókjában válik. A megosztás vagy a tároló neve nem felel meg az Azure elnevezési konvencióinak. Nevezze át a fájlt, így megfelel [elnevezési konvenciók Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ismételje meg az ellenőrzést.   |
| `InvalidBlobNameFormat` | Fájl elérési útja nem egy érvényes blob elérési utat a felhőbeli az elnevezési konvenciók Azure Blob képezze le.|Nevezze át a fájlt, így megfelel [elnevezési konvenciók Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ismételje meg az ellenőrzést. |
| `InvalidFileNameFormat` | Fájl elérési útja nem egy érvényes fájlelérési utat a felhőbeli az elnevezési konvenciók Azure File képezze le. |Nevezze át a fájlt, így megfelel [elnevezési konvenciók Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ismételje meg az ellenőrzést. |
| `InvalidDiskNameFormat` | Fájl elérési útja nem leképezése a felhőben az Azure Managed Disk elnevezési konvencióinak megfelelően érvényes lemez nevét. |Nevezze át a fájlt, így megfelel [elnevezési konvenciók Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ismételje meg az ellenőrzést.       |
| `NotPartOfFileShare` | Fájlok feltöltése útvonala nem érvényes. A fájlok feltöltése az Azure Files mappába.   | Távolítsa el a fájlokat a hiba, és töltse fel azokat a fájlokat egy precreated mappába. Ismételje meg az ellenőrzést. |
| `NonVhdFileNotSupportedForManagedDisk` | Felügyelt lemez nem VHD-fájl nem tölthető fel. |Távolítsa el a nem VHD-fájlok `ManagedDisk` mappába ezek használata nem támogatott, vagy helyezze át ezeket a fájlokat egy `PageBlob` mappát. Ismételje meg az ellenőrzést. |


## <a name="next-steps"></a>További lépések

- Hibaelhárítás [adatok feltöltéssel kapcsolatos hibák](data-box-disk-troubleshoot-upload.md).
