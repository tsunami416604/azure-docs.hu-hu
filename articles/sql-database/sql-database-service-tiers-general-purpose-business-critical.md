---
title: Az Azure SQL Database – általános célú és a kritikus fontosságú üzleti |} A Microsoft Docs
description: A cikk ismerteti az általános célú és üzleti kritikus szolgáltatási szinten a vcore magok vásárlási modell a.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 11/30/2018
ms.openlocfilehash: 7de4415dd332254c595b6687dfb9d8db01c42362
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871710"
---
# <a name="azure-sql-database-service-tiers"></a>Az Azure SQL Database szolgáltatási csomagjai

Az Azure SQL Database az SQL Server adatbázismotor architektúra, amely 99,99 %-os rendelkezésre állását, még akkor is, az infrastruktúra-hibák esetekben biztosítása érdekében a felhőalapú környezet módosul alapul. Nincsenek az Azure SQL Database által használt három architekturális modellek:
- [Általános célú](sql-database-service-tier-general-purpose.md) többsége az általános számítási feladatokhoz tervezték.
- [Üzletileg kritikus](sql-database-service-tier-business-critical.md) rövid késleltetésű számítási feladatok és egy olvasható replika tervezve.
- [Nagy kapacitású](sql-database-service-tier-hyperscale.md) nagyon nagy méretű adatbázisok számára hoztuk létre (akár 100 TB-os) rendelkező több olvasható replika.

Ez a cikk ismerteti az általános célú és az üzletileg kritikus szolgáltatási csomagokra a Virtuálismag-alapú vásárlási modell a storage és a biztonsági másolat szempontjai.

> [!NOTE]
> A Virtuálismag-alapú vásárlási modell a nagy kapacitású szolgáltatásszintre vonatkozó részletekért lásd: [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md). A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-service-tiers.md).

## <a name="data-and-log-storage"></a>Adatok és a naplók tárolásához

A következőket ajánljuk figyelmébe:

- A lefoglalt tárolót adatfájlok (MDF) és a naplófájlok fájlok (LDF) használja.
- Minden önálló adatbázis az adatbázis maximális méretét, egy alapértelmezett 32 GB maximális mérettel számítási mérete támogatja.
- Amikor konfigurálja a szükséges egyetlen adatbázis mérete (MDF mérete), 30 %-a további tárhely automatikusan hozzáadott LDF támogatása
- A felügyelt példány tároló mérete 32 GB többszörösének kell adni.
- Kiválaszthatja, hogy minden önálló adatbázis mérete 10 GB-os és a támogatott maximális érték között
  - Standard szintű tárolóra vonatkozó növelése, vagy 10 GB-os lépésekben méretének csökkentése
  - A Premium storage növelheti, vagy 250 GB-os lépésekben méretének csökkentése
- Az általános célú szolgáltatásszinten lévő `tempdb` használ egy csatlakoztatott SSD és a tárolási költségek a virtuális mag díja tartalmazza.
- Az üzletileg kritikus szolgáltatási rétegben található `tempdb` megosztások a csatlakoztatott SSD az MDF és az LDF-fájlok és a TempDB adatbázisban a tárolási költségek a virtuális mag díja tartalmazza.

> [!IMPORTANT]
> A teljes tárterület MDF és az LDF-lefoglalt díjkötelesek.

A jelenlegi teljes mérete MDF és az LDF-monitorozásához használja [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF és az LDF-fájlok aktuális mérete monitorozásához használja [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Biztonsági mentés és tárolás

Az adatbázis biztonsági másolatait lefoglalta a pont támogatja az idő-visszaállítás (PITR) és [hosszú távú adatmegőrzést (LTR)](sql-database-long-term-retention.md) SQL Database képességeit. Ez a tároló lefoglalt külön-külön az egyes adatbázisok, és két külön adatbázis-díj terheli.

- **PITR**: önálló adatbázis biztonsági másolatait másolja [RA-GRS tároló](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatikusan. A tároló mérete dinamikusan növeli, amikor az új biztonsági mentéseket hoz létre.  A tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez, illetve a tranzakciónaplók 5 percenként másolt biztonsági másolataihoz használja a rendszer. A tárhelyhasználat attól függ, hogy az adatbázis és a megőrzési időszak a változási gyakoriság. Beállíthatja, hogy minden egyes adatbázis 7 és 35 nap közötti, egy különálló megőrzési ideje. Egy minimális tárolókapacitás egyenlő 1 x-re az adatok méretének külön díjfizetés nélkül van megadva. A legtöbb adatbázisok esetében ez a mennyiség ez elegendő 7 nap a biztonsági mentések tárolásához.
- **Az LTR**: SQL-adatbázis a teljes biztonsági mentések hosszú távú megőrzésének konfigurálása akár 10 évig lehetőséget kínál. Az LTR-szabályzat engedélyezve van, ha a következő biztonsági másolatai RA-GRS tároló automatikusan, de szabályozhatja, hogy milyen gyakran a biztonsági másolatok másolódnak. A különböző megfelelőségi követelménynek megfelel, heti, havi és/vagy éves biztonsági mentések különböző megőrzési időtartamú választhat. Ez a konfiguráció meghatározzuk, mennyi tárhelyre lesz az LTR biztonsági mentéseket. Használhatja az LTR-díjkalkulátor LTR tárolási költségek becsléséhez. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="next-steps"></a>További lépések

- Részletek az adott számítási méretek és a tároló mérete lehetőségek önálló adatbázis az általános célú és a kritikus fontosságú üzleti szolgáltatási szinten érhető el, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok az önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Részletek az adott számítási méretek és a tároló mérete lehetőségek az általános célú és a kritikus fontosságú üzleti szolgáltatási szinten a rugalmas készletek számára elérhető, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok a rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
