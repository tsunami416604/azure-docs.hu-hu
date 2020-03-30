---
title: Kapcsolat nélküli biztonsági mentés – áttekintés
description: További információ az offline biztonsági mentés összetevőiről. Ezek közé tartozik az Azure Data Box-on alapuló offline biztonsági mentés és az Azure import/export szolgáltatáson alapuló offline biztonsági mentés.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196160"
---
# <a name="overview-of-offline-backup"></a>Kapcsolat nélküli biztonsági mentés – áttekintés

Ez a cikk áttekintést nyújt az offline biztonsági mentésről.

Az Azure-ba történő kezdeti teljes biztonsági mentések általában nagy mennyiségű adatot továbbítanak online, és nagyobb hálózati sávszélességet igényelnek a későbbi biztonsági mentésekhez képest, amelyek csak növekményes módosításokat továbbítanak. Távoli irodák vagy adatközpontok bizonyos földrajzi területeken nem mindig rendelkeznek elegendő hálózati sávszélességgel. Emiatt ezek a kezdeti biztonsági mentések több napot vesz igénybe. Ez idő alatt a biztonsági mentések folyamatosan ugyanazt a hálózatot használják, amely a helyszíni adatközpontban futó alkalmazások számára lett kiépítve.

Az Azure Backup támogatja az offline biztonsági mentést, amely a kezdeti biztonsági mentési adatokat offline, hálózati sávszélesség használata nélkül továbbítja. Lehetővé teszi a biztonsági mentési adatok fizikai tárolóeszközökre történő másolását. Az eszközöket ezután egy közeli Azure-adatközpontba szállítják, és feltöltik egy Recovery Services-tárolóba. Ez a folyamat biztosítja a biztonsági mentési adatok megbízható átvitelét hálózati sávszélesség használata nélkül.

## <a name="offline-backup-options"></a>Kapcsolat nélküli biztonsági mentési beállítások

Az offline biztonsági mentés a tárolóeszközök tulajdonjoga alapján két módban érhető el:

- Offline biztonsági mentés az Azure Data Box alapján (előzetes verzió)
- Offline biztonsági mentés az Azure importálási/exportálási szolgáltatása alapján

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offline biztonsági mentés az Azure Data Box alapján (előzetes verzió)

Ez a mód jelenleg a Microsoft Azure Recovery Services (MARS) Ügynök előzetes verzióban támogatott. Ez a beállítás az [Azure Data Box](https://azure.microsoft.com/services/databox/) előnyeit kihasználva szállítja a Microsoft saját tulajdonú, biztonságos és illetéktelen beavatkozásnak ellenálló átviteli készülékeit USB-csatlakozókkal az adatközpontba vagy a távoli irodába. A biztonsági mentési adatok közvetlenül ezekre az eszközökre vannak írva. Ez a beállítás menti a szükséges erőfeszítést, hogy beszerezze a saját Azure-kompatibilis lemezek és összekötők, vagy átmeneti helyként átmeneti helyként átmeneti tároló kiépítése. A Microsoft kezeli a végpontok között történő átviteli logisztika, amely az Azure Portalon keresztül nyomon követheti. 

Itt látható egy architektúra, amely leírja a biztonsági mentési adatok mozgását ezzel a beállítással.

![Azure biztonsági mentési adatdoboz-architektúra](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Itt egy összefoglaló az architektúra:

1. Az Azure Backup közvetlenül másolja a biztonsági mentési adatokat ezekre az előre konfigurált eszközökre.
2. Ezután ezeket az eszközöket visszaküldheti egy Azure-adatközpontba.
3. Az Azure Data Box átmásolja az adatokat egy ügyfél tulajdonában lévő tárfiókba.
4. Az Azure Backup automatikusan átmásolja a biztonsági mentési adatokat a tárfiókból a kijelölt Helyreállítási szolgáltatások tárolóba. Növekményes online biztonsági mentések vannak ütemezve.

Ha az Azure Data Box szolgáltatáson alapuló offline biztonsági mentést szeretné használni, olvassa el az Offline biztonsági mentés az Azure Data Box használatával című [témakört.](offline-backup-azure-data-box.md)

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline biztonsági mentés az Azure importálási/exportálási szolgáltatása alapján

Ezt a beállítást a Microsoft Azure Backup Server (MABS), a System Center Data Protection Manager (DPM) DPM-A és a MARS Agent támogatja. Az [Azure importálási/exportálási szolgáltatását](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)használja. A kezdeti biztonsági mentési adatokat átviheti az Azure-ba a saját Azure-kompatibilis lemezei és összekötői használatával. Ez a megközelítés megköveteli, hogy az átmeneti helynéven ismert ideiglenes tárolót létesítsen, és előre összeállított segédprogramok használatával formázza és másolja a biztonsági mentési adatokat az ügyfél tulajdonában lévő lemezekre. 

Itt látható egy architektúra, amely leírja a biztonsági mentési adatok mozgását ezzel a beállítással.

![Azure biztonsági mentésimportálási/exportálási szolgáltatásarchitektúrája](./media/offline-backup-overview/azure-backup-import-export.png)

Itt egy összefoglaló az architektúra:

1. Ahelyett, hogy a biztonsági mentési adatokat a hálózaton keresztül, az Azure Backup írja a biztonsági mentési adatokat egy átmeneti helyre.
2. Az átmeneti helyen lévő adatok egy vagy több SATA-lemezre íródnak egy egyéni segédprogram mal.
3. Az előkészítő munka részeként a segédprogram létrehoz egy Azure importálási feladatot. A SATA-meghajtók szállítják a legközelebbi Azure-adatközpontba, és hivatkozzon az importálási feladat a tevékenységek csatlakoztatásához.
4. Az Azure-adatközpontban a lemezeken lévő adatok egy Azure-tárfiókba másolva lesznek.
5. Az Azure Backup átmásolja a biztonsági mentési adatokat a tárfiókból a Recovery Services-tárolóba. Növekményes biztonsági mentések vannak ütemezve.

Ha az Azure Import/Export szolgáltatáson alapuló offline biztonsági mentést szeretne használni a MARS-ügynökkel, olvassa el az Offline biztonsági mentésmunkafolyamat az Azure Backup ban című [témakört.](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

Ha ugyanezt szeretné használni az MABS-szel vagy a DPM-A-val együtt, olvassa el [a DPM és az Azure Backup Server offline biztonsági mentési munkafolyamata című témakört.](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)

## <a name="offline-backup-support-summary"></a>Offline biztonsági mentés támogatás ának összefoglalása

Az alábbi táblázat összehasonlítja a két rendelkezésre álló lehetőséget, így a forgatókönyv alapján meghozhatja a megfelelő döntéseket.

| **Szempont**                                            | **Offline biztonsági mentés az Azure Data Box alapján**                     | **Offline biztonsági mentés az Azure importálási/exportálási szolgáltatása alapján**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Az Azure Backup telepítési modelljei                              | MARS Ügynök (előzetes verzió)                                              | MARS ügynök, MABS, DPM-A                                           |
| A biztonsági mentési adatok maximális adatai kiszolgálónként (MARS) vagy védelmi csoportonként (MABS, DPM-A) | [Azure Data Box lemez](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (egyenként 10 8 TB lemez)                          |
| Biztonság (adat, eszköz és szolgáltatás)                           | [Adat](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256 bites titkosított <br> [Eszköz](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Robusztus eset, saját, hitelesítő adatokon alapuló felület az adatok másolásához <br> [Szolgáltatás](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) – Az Azure biztonsági funkciói védik | Adatok - BitLocker titkosított                                 |
| Ideiglenes átmeneti hely kiépítése                     | Nem kötelező                                                | A biztonsági mentési adatok becsült méreténél nagyobb vagy azzal egyenlő        |
| Támogatott régiók                                           | [Az Azure Data Box lemezrégiói](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Az Azure Data Box régiói](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure importálási/exportálási szolgáltatási régiói](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Országközi szállítás                                     | Nem támogatott  <br>    A forráscímnek és a célállomásnak az Azure-adatközpontnak ugyanabban az országban kell lennie* | Támogatott                                                    |
| Transzfer logisztika (szállítás, szállítás, felvétel)           | Teljes mértékben a Microsoft által kezelt                                     | Vevő által kezelt                                            |
| Díjszabás                                                      | [Az Azure Data Box díjszabása](https://azure.microsoft.com/pricing/details/databox/) <br> [Az Azure Data Box lemezdíjszabása](https://azure.microsoft.com/pricing/details/databox/disk/) | [Az Azure importálási/exportálási szolgáltatásárazása](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Ha az ország nem rendelkezik Azure-adatközponttal, a lemezeket egy másik országban lévő Azure-adatközpontba kell szállítania.

## <a name="next-steps"></a>További lépések

* [Az Azure Backup offline biztonsági mentése az Azure Data Box használatával](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Offline biztonsági mentési munkafolyamat az Azure Backupban](backup-azure-backup-import-export.md) 
* [Offline biztonsági mentési munkafolyamat a DPM és az Azure Backup Server számára](backup-azure-backup-server-import-export-.md)
