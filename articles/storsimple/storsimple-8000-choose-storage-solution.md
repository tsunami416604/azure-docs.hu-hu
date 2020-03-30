---
title: Az Azure-ba történő adatátvitel beállításai egy készülék használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan választhatja ki az Adatok Azure-ba történő átvitelének megfelelő berendezést
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965359"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>A StorSimple összehasonlítása az Azure File Synckel és a Data Box Edge adatátviteli lehetőségeivel 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Ez a dokumentum áttekintést nyújt a helyszíni adatátvitel azure-ba, összehasonlítása: Data Box Edge vs. Azure File Sync vs. StorSimple 8000 sorozat.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – A Data Box Edge egy helyszíni hálózati eszköz, amely adatokat mozgat az Azure-ba és az Azure-ból, és AI-kompatibilis Edge-számítással rendelkezik az adatok feltöltés közbeni előzetes feldolgozásához. A Data Box Gateway az eszköz virtuális verziója, amely azonos adatátviteli képességekkel rendelkezik.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – Az Azure File Sync segítségével központosíthatja a szervezet fájlmegosztások az Azure Files, miközben a rugalmasság, a teljesítmény és a kompatibilitás egy helyszíni fájlkiszolgáló. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. Az Azure File Sync általános elérhetőségét 2018-ban jelentették be.
- **[A StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple egy hibrid eszköz, amely segít a vállalatoknak az elsődleges tárolás, az adatvédelem, az archiválás és a vészhelyreállítás egyetlen megoldáson történő konszolidálásában azáltal, hogy szorosan integrálja az Azure storage-ral. A StorSimple termékéletciklusa [itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)található.

## <a name="comparison-summary"></a>Összehasonlítás összegzése

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Áttekintés         |Rétegzett hibrid tárolás és archiválás|Általános fájlkiszolgálói tárolás felhőrétegezéssel és többhelyes szinkronizálással.  |Tárolási megoldás az adatok előzetes feldolgozásához és hálózaton keresztül az Azure-ba való elküldéséhez.        |
|Forgatókönyvek        |Fájlkiszolgáló, archiválás, biztonsági mentési cél |Fájlszerver, archiválás (többhelyes)   |Adatátvitel, előfeldolgozás, beleértve a pénzmosással kapcsolatos következtetéseket, az IoT-t, az archiválást    |
|Peremhálózati számítás     |Nem érhető el |Nem érhető el |Támogatja a futó tárolók at Azure IoT Edge    |
|Alaktényező      |Fizikai eszköz   |A Windows Server rendszerre telepített ügynök |Fizikai eszköz   |
|Hardver         |A Microsoft által a szolgáltatás részeként biztosított fizikai eszköz | Ügyfél által megadott |A Microsoft által a szolgáltatás részeként biztosított fizikai eszköz  |
|Adatformátum      |Egyéni formátum   |Fájlok         |Blobok vagy fájlok    |
|Protokolltámogatás |iSCSI          |SMB, NFS    | SMB vagy NFS      |
|Díjszabás          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>További lépések

- További információ az [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) és az Azure Data Box Gateway [szolgáltatásról](/azure/databox-online/data-box-gateway-overview)
- További információ az [Azure File Sync-ről](/azure/storage/files/storage-sync-files-deployment-guide)
