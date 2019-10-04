---
title: Az Azure-ba irányuló adatátviteli lehetőségek a készülék használatával | Microsoft Docs
description: Megtudhatja, hogyan választhatja ki a megfelelő készüléket az adatok Azure-ba történő átviteléhez
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965359"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Az StorSimple összehasonlítása Azure File Sync és Data Box Edge adatátviteli beállításokkal 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Ez a dokumentum áttekintést nyújt az Azure-ba irányuló helyszíni adatátviteli lehetőségekről, összehasonlítva: Data Box Edge vs. Azure File Sync vs. StorSimple 8000 sorozat.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – a Data Box Edge egy helyszíni hálózati eszköz, amely az Azure-ba, illetve az Azure-ba helyezi át az adatátvitelt, és az AI-kompatibilis Edge számítási feladatokkal előre feldolgozza az adatok feltöltését. Data Box Gateway az eszköz virtuális verziója azonos adatátviteli képességekkel.
- **[Azure file Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – a Azure file Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. A Azure File Sync általánosan elérhetővé vált a 2018-es verzióban.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – a StorSimple egy hibrid eszköz, amely segít a vállalatok számára az elsődleges tárterület, az adatvédelem, az archiválás és a vész-helyreállítás egyetlen megoldáson való összevonásával, az Azure Storage-nal való szoros integráció révén. A StorSimple termék-életciklusa [itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)található.

## <a name="comparison-summary"></a>Összehasonlító összefoglalás

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Áttekintés         |Többplatformos hibrid tárolás és archiválás|Általános fájlkiszolgáló tároló a felhő-és többhelyes szinkronizálással.  |Tárolási megoldás az adatfeldolgozáshoz és a hálózatról az Azure-ba való küldéséhez.        |
|Forgatókönyvek        |Fájlkiszolgáló, archiválás, biztonsági mentési cél |Fájlkiszolgáló, archiválás (többhelyes)   |Adatátvitel, adatok előfeldolgozása, beleértve a következőket: ML-következtetés, IoT, archiválás    |
|Peremhálózati számítás     |Nem állnak rendelkezésre adatok |Nem állnak rendelkezésre adatok |Támogatja a futó tárolókat Azure IoT Edge    |
|Űrlap tényező      |Fizikai eszköz   |A Windows Serverre telepített ügynök |Fizikai eszköz   |
|Hardver         |A Microsoft által a szolgáltatás részeként biztosított fizikai eszköz | Ügyfél által megadott |A Microsoft által a szolgáltatás részeként biztosított fizikai eszköz  |
|Adatformátum      |Egyéni formátum   |Fájlok         |Blobok vagy fájlok    |
|Protokollok támogatása |iSCSI          |SMB, NFS    | SMB vagy NFS      |
|Díjszabás          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>További lépések

- A [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) és a [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview) ismertetése
- Tudnivalók a [Azure file Sync](/azure/storage/files/storage-sync-files-deployment-guide)
