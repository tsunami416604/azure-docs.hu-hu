---
title: Az Azure-ban telepíthetőek át adatok lehetőségei |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki a megfelelő készülék adatok átviteléhez az Azure-bA
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/12/2018
ms.author: alkohli
ms.openlocfilehash: c28eaf22d05bfda5085f9e269bda85ca0d46a7d3
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578124"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Hasonlítsa össze a StorSimple az Azure File Sync és a Data Box Edge adatok átvitel beállításai 
 
Ez a dokumentum a helyszíni adatátvitel lehetőségei áttekintést nyújt az Azure-bA összehasonlítása: Data Box Edge vs. Az Azure File Sync vs. A StorSimple 8000 sorozat.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview.md)**  – Data Box Edge-, Edge AI-kompatibilis számítási előre fel adatokat a feltöltés során, és áthelyezi az adatokat, és az Azure-ból a helyszíni hálózati eszköz. Ignite 2018 rendezvényen mutattuk be és a nyilvános előzetes verzióban érhető el. Data Box átjáró egy virtuális verziója – az eszközt az adatok átvitel ugyanazokat a lehetőségeket.
- **[Az Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide.md)**  – az Azure File Sync segítségével fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Általános rendelkezésre állás az Azure File Sync 2018-ban korábban bejelentettük.
- **[A StorSimple](/azure/storsimple/storsimple-overview.md)**  – StorSimple hibrid eszköz, amellyel a vállalatok számára a tárolási infrastruktúráját elsődleges tárolási, az adatvédelem, az archiválás és vész-helyreállítási ugyanazon a megoldáson konszolidálhatja szorosan való integráció révén a Az Azure storage. A storsimple-höz készült termékéletciklus található [Itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Összefoglaló összehasonlítása

|                           |A StorSimple 8000-es   |Azure File Sync   |Data Box Edge (előzetes verzió)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Áttekintés         |Többrétegű hibrid tárolás és archiválás|Általános fájlkiszolgálói tárolást felhőalapú rétegezési és többhelyes szinkronizálással.  |Tárolási megoldás előre dolgozza fel az adatokat, és az Azure-bA hálózaton keresztüli küldéshez.        |
|Forgatókönyvek        |Fájlkiszolgáló, archiválás, biztonsági mentési cél |Fájlkiszolgáló, archiválási (többhelyes)   |Bejövő adatforgalom, adatok előfeldolgozási például gépi tanulás következtetési, IoT, archiválási    |
|Peremhálózati számítás     |Nem érhető el |Nem érhető el |Támogatja a futó tárolók az Azure IoT Edge használatával    |
|Helyigény      |Fizikai eszköz   |A Windows Server-ügynököt |Fizikai eszköz   |
|Hardver         |A Microsoft a szolgáltatás részeként megadott fizikai eszköz | Vásárló által biztosított |A Microsoft a szolgáltatás részeként megadott fizikai eszköz  |
|Adatformátum      |Egyéni formátum   |Fájlok         |Blobok és fájlok    |
|Protokolltámogatás |iSCSI          |SMB, NFS    | Az SMB vagy NFS      |
|Díjszabás          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure Data Box-Edge](/azure/databox-online/data-box-edge-overview) és [Azure Data Box-átjáró](/azure/databox-online/data-box-gateway-overview)
- Ismerje meg [az Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
