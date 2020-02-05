---
title: Az offline biztonsági mentés áttekintése
description: Ismerje meg az offline biztonsági mentés különböző összetevőit, beleértve a Azure Data Box-alapú offline biztonsági mentést és az Azure import/export alapú offline biztonsági mentést.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027016"
---
# <a name="overview-of-offline-backup"></a>Az offline biztonsági mentés áttekintése

Ez a cikk áttekintést nyújt az offline biztonsági mentésről.

Az Azure-hoz készült kezdeti teljes biztonsági mentés általában nagy mennyiségű adat online átvitelét és nagyobb hálózati sávszélességet igényel, ha a további, csak növekményes módosításokat átvevő biztonsági másolatokhoz hasonlít. Bizonyos földrajzi területeken a távoli irodák vagy adatközpontok nem mindig rendelkeznek elegendő hálózati sávszélességgel. Ezért ezek a kezdeti biztonsági másolatok több napig tartanak, és ez idő alatt folyamatosan használják ugyanazt a hálózatot, amelyet a helyszíni adatközpontban futtatott alkalmazásokhoz kiosztottak.

A Azure Backup támogatja a "kapcsolat nélküli biztonsági mentést", amely lehetővé teszi a kezdeti biztonsági mentési adatforgalom kapcsolat nélküli átvitelét a hálózati sávszélesség használata nélkül. Egy mechanizmust biztosít a biztonsági mentési adattárolók fizikai tárterületre másolására, majd egy közeli Azure-adatközpontba szállított, majd egy Recovery Services-tárolóba feltöltött eszközökre. Ez a folyamat biztosítja a biztonsági mentési adatok robusztus átvitelét a hálózati sávszélesség használata nélkül.

## <a name="offline-backup-options"></a>Offline biztonsági mentési beállítások

Offline – a biztonsági mentést a tárolóeszközök tulajdonjoga alapján két módban kínáljuk.

1. Azure Data Box alapú offline biztonsági mentés (előzetes verzió)
2. Azure import/export-alapú offline biztonsági mentés

## <a name="azure-data-box-based-offline-backup-preview"></a>Azure Data Box alapú offline biztonsági mentés (előzetes verzió)

Ezt a módot jelenleg a MARS-ügynök támogatja, előzetes verzióban. Ez a lehetőség kihasználja a [Azure Data Box szolgáltatást](https://azure.microsoft.com/services/databox/) , hogy a Microsoft által védett, biztonságos és illetéktelen hozzáférést biztosító adatátviteli berendezéseket az USB-összekötők, az adatközpont vagy a távoli iroda számára, a biztonsági mentési adatok pedig közvetlenül ezekre az eszközökre írják. **Ezzel a beállítással elmentheti a saját Azure-kompatibilis lemezek és összekötők beszerzéséhez, illetve átmeneti tárolási helyként való üzembe helyezéséhez szükséges erőfeszítést.** A Microsoft emellett a végpontok közötti adatátviteli logisztikát is kezeli, amelyet nyomon követhet a Azure Portalon. Alább látható egy architektúra, amely leírja, hogy a biztonsági mentési adatforgalom milyen módon történik.

![Azure Backup Data Box architektúra](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Az architektúra összefoglalása:

1. Azure Backup közvetlenül másolja a biztonsági mentési adatfájlokat ezekre az előre konfigurált eszközökre.
2. Ezután visszaszállíthatja ezeket az eszközöket egy Azure-adatközpontba.
3. A Azure Data Box szolgáltatás egy ügyfél tulajdonában lévő Storage-fiókba másolja az adataikat.
4. Azure Backup automatikusan átmásolja a biztonsági mentési adatokat a Storage-fiókból a kijelölt Recovery Services-tárolóba, és a növekményes online biztonsági mentéseket ütemezi.

Ha Azure Data Box alapú offline biztonsági mentést szeretne használni, tekintse meg [ezt a cikket](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Azure import/export-alapú offline biztonsági mentés

Ezt a beállítást Azure Backup Server (MABS)/DPM-A/MARS-ügynök támogatja. Kihasználja az [Azure import/export szolgáltatást](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) , amely lehetővé teszi a kezdeti biztonsági mentési adatok átvitelét az Azure-ba saját Azure-kompatibilis lemezek és összekötők használatával. Ehhez a megközelítéshez egy ideiglenes tárolót kell kiépíteni, amelyet átmeneti **helyként** és az előre elkészített segédprogramok használatakor kell létrehozni a biztonsági mentési adatok ügyfél által birtokolt lemezekre történő formázásához és másolásához. Alább látható egy architektúra, amely leírja, hogy a biztonsági mentési adatforgalom milyen mozgásban van.

![Azure Backup importálási/exportálási architektúra](./media/offline-backup-overview/azure-backup-import-export.png)

Az architektúra összefoglalása:

1. Ahelyett, hogy a biztonsági mentési adatokat a hálózaton keresztül küldi, Azure Backup a biztonsági mentési adatokat egy átmeneti helyre írja.
2. Az átmeneti hely adatai egy vagy több SATA-lemezre íródnak egy egyéni segédprogram használatával.
3. Az előkészítő munka részeként a segédprogram létrehoz egy Azure importálási feladatot. A rendszer a legközelebbi Azure-adatközpontba szállítja a SATA-meghajtókat, és az importálási feladatokra hivatkozik a tevékenységek összekapcsolásához.
4. Az Azure-adatközpontban a lemezeken lévő adattárakat egy Azure Storage-fiókba másolja a rendszer.
5. Azure Backup a biztonsági mentési adatokat a Storage-fiókból a Recovery Services-tárolóba másolja, és a növekményes biztonsági mentések ütemezve lesznek.

Az Azure import/export-alapú offline biztonsági mentés a MARS-ügynökkel való használatához tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Ha ugyanezt a MABS/DPM-A-vel együtt szeretné használni, tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Offline biztonsági mentési támogatás összegzése

Az alábbi táblázat összehasonlítja a két elérhető lehetőséget, így a forgatókönyv alapján elvégezheti a megfelelő döntéseket.

| **Szempont**                                            | **Azure Data Box alapú offline biztonsági mentés**                     | **Azure import/export-alapú offline biztonsági mentés**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup üzembe helyezési modellek                              | MARS-ügynök (előzetes verzió)                                              | MARS-ügynök, Azure Backup Server (MABS), DPM-A                                           |
| Biztonsági másolatok maximális száma kiszolgálónként (MARS) vagy védelmi csoportonként (MABS, DPM-A) | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) – 7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) – 80 TB       | 80 TB (legfeljebb 10 lemez 8 TB-ig)                          |
| Biztonság (adatközpont & szolgáltatás)                           | Adataes-256 [bites titkosítás](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) <br> [Eszköz](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) – robusztus eset, tulajdonosi hitelesítőadat-alapú felület az adatok másolásához <br> Az Azure biztonsági szolgáltatásai által [védett szolgáltatások](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | Adatátvitel – BitLocker – titkosított                                 |
| Ideiglenes előkészítési hely kiépítés                     | Nem kötelező                                                | A biztonsági másolatok becsült méretének nagyobb vagy egyenlőnek kell lennie        |
| Támogatott régiók                                           | [Azure Data Box Disk régiók](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box régiók](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure importálási/exportálási régiói](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Országok közötti szállítás *                                     | **Nem támogatott**  <br>    *A forráscím & az Azure-adatközpontnak ugyanabban az országban kell lennie* | Támogatott                                                    |
| Adatátviteli logisztika (szállítás, szállítás, felveszés)           | Teljes mértékben Microsoft által felügyelt                                     | Ügyfél által felügyelt                                            |
| Díjszabás                                                      | [Díjszabás Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Díjszabás Azure Data Box Disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Az Azure importálási/exportálási díjszabása](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Ha országa nem rendelkezik Azure-adatközpontgal, akkor egy másik országban lévő Azure-adatközpontba kell szállítani a lemezeket.*

## <a name="next-steps"></a>Következő lépések

* [Azure Data Box alapú offline biztonsági mentés a MARS-ügynökhöz](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure import/export-alapú offline biztonsági mentés a MARS-ügynökhöz](backup-azure-backup-import-export.md)  
* [Azure import/export-alapú offline biztonsági mentés a MABS/DPM-A-hez](backup-azure-backup-server-import-export-.md)
