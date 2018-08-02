---
title: Az Azure SQL Database szolgáltatási szinteken - DTU |} A Microsoft Docs
description: Ismerje meg egyetlen szolgáltatásszintek és teljesítményszintek és tárolási méretek készlethez adatbázisok.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411820"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>DTU-alapú szolgáltatásszint, a teljesítményszint és a tárolási erőforrások kiválasztása 

Szolgáltatásszintek által meghatározott memóriamennyiséggel rendelkeznek a csomagban foglalt tárhely, a megőrzési időszak a biztonsági mentések és a rögzített ár rögzített teljesítményszintek számos különbözteti meg. Összes szolgáltatási szint, leállás nélkül a teljesítményszint módosítása rugalmasságot biztosítanak. Önálló adatbázisok és rugalmas készletek számlázása óraszám alapján, szolgáltatásszintet és teljesítményét.

> [!IMPORTANT]
> SQL Database felügyelt példányába, jelenleg nyilvános előzetes verziója nem támogatja a DTU-alapú vásárlási modell. További információkért lásd: [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>DTU-alapú szolgáltatásszint kiválasztása

Szolgáltatásszint kiválasztása elsődlegesen az üzleti folytonosság, tárolás és teljesítmény-követelmények attól függ.
||Alapszintű|Standard|Prémium|
| :-- | --: |--:| --:| --:| 
|Egyes számítási feladatokhoz|Fejlesztési és éles környezetek|Fejlesztési és éles környezetek|Fejlesztési és éles környezetek||
|SLA-ban garantált üzemidő|99.99%|99.99%|99.99%|Nincs előzetes verzióban|
|Biztonsági mentés megőrzése|7 nap|35 napon belül|35 napon belül|
|CPU|Alacsony|Alacsony, közepes, nagy|Közepes, nagy|
|IO-átviteli sebesség (becsült) |2,5 dtu-k IOPS| 2,5 dtu-k IOPS | Dtu-k 48 IOPS|
|IO-késés (becsült)|5 ezredmásodperc (olvasás), 10 ms (írás)|5 ezredmásodperc (olvasás), 10 ms (írás)|2 ms (olvasás/írás)|
|Oszlopcentrikus indexelés |–|S3 vagy újabb verzió|Támogatott|
|Memóriabeli OLTP beállítása|–|–|Támogatott|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Önálló adatbázis DTU- és tárterületi korlátozásai

A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. További információ a dtu-król és Edtu-: [Mik a dtu-król és edtu-k](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Alapszintű|Standard|Prémium|
| :-- | --: | --: | --: | --: |
| Maximális tárterület méretét | 2 GB | 1 TB | 4 TB  | 
| Maximális dtu-k | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Rugalmas készlet edtu-k, a storage és a készletezett adatbázis korlátok

| | **Basic** | **Standard** | **Prémium** | 
| :-- | --: | --: | --: | --: |
| Az adatbázisonkénti maximális mérete  | 2 GB | 1 TB | 1 TB | 
| Készletenkénti maximális mérete | 156 GB | 4 TB | 4 TB | 
| Maximális Edtu adatbázisonként | 5 | 3000 | 4000 | 
| Maximális edtu-k száma készletenként | 1600 | 3000 | 4000 | 
| Készletenkénti adatbázisok maximális száma | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> A következő kivételével minden régióban több mint 1 TB prémium szintű storage jelenleg érhető el: USA nyugati középső Régiója, kelet-Kína, közép-USDoDCentral, Németország, USDoDEast, USA-beli államigazgatás – délnyugati, USGov Iowa, Északkelet-Németország, Észak-Kína. Más régiókban a Prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="next-steps"></a>További lépések

- További információ a meghatározott teljesítményszintet és az önálló adatbázisok számára elérhető tárolási mérete lehetőségeket: [SQL adatbázis DTU-alapú erőforráskorlátok az önálló adatbázisok](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- További információ a meghatározott teljesítményszintet és a tároló mérete választható rugalmas készletek számára elérhető: [SQL Database DTU-alapú erőforráskorlátok](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
