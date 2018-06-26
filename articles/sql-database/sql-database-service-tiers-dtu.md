---
title: Az Azure SQL adatbázis szolgáltatásszintjeinek - DTU |} Microsoft Docs
description: Ismerje meg egyetlen szolgáltatásszintek és teljesítményszintek és tárolási méretek készlet adatbázisok.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: cf17ec616819da94678f2ae4f0f0ca283f99f629
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750424"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>A DTU-alapú szolgáltatásréteghez, a teljesítményszintet és a tárolási erőforrások kiválasztása 

Számos különböző teljesítményszintek az belefoglalt tárolási, megőrzési időszak biztonsági mentések és a rögzített ár rögzített méretű szerint megkülönböztetett forgalomosztályból a szolgáltatási szinteket. Az összes szolgáltatási szinteket a leállás nélküli teljesítményszintet változó rugalmasságot biztosítanak. Önálló adatbázisok és rugalmas készletek számlázása óránként és teljesítményszintet szolgáltatásszint alapján.

> [!IMPORTANT]
> SQL adatbázis-felügyelt példány jelenleg nyilvános előzetes verziójában nem támogatja a DTU-alapú alapjául szolgáló vásárlási modell. További információkért lásd: [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>A DTU-alapú szolgáltatásréteg kiválasztása

Elsősorban az üzletmenet folytonosságát, a tároló és a teljesítményre vonatkozó követelmények attól függ, egy szolgáltatási réteg kiválasztása.
||Alapszintű|Standard|Prémium|
| :-- | --: |--:| --:| --:| 
|Cél munkaterhelés|Fejlesztési és éles|Fejlesztési és éles|Fejlesztési és éles||
|SLA-ban garantált üzemidő|99.99%|99.99%|99.99%|Nincs kép nézetben|
|Biztonsági mentés megőrzése|7 nap|35 nap|35 nap|
|CPU|Alacsony|Alacsony, közepes, magas|Közepes, magas|
|IO átviteli sebesség (hozzávetőleges) |2.5 DTU iops-értéket| 2.5 DTU iops-értéket | DTU 48 iops-értéket|
|I/O várakozási ideje (hozzávetőleges)|10 ms (írás) (olvasás), 5 ms|10 ms (írás) (olvasás), 5 ms|2 ms (olvasás/írás)|
|Oszlopcentrikus indexelő |–|S3 vagy újabb verzió|Támogatott|
|A memórián belüli online Tranzakciófeldolgozási|–|–|Támogatott|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Önálló adatbázis DTU- és tárterületi korlátozásai

A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Alapszintű|Standard|Prémium|
| :-- | --: | --: | --: | --: |
| Maximális tárolóméret | 2 GB | 1 TB | 4 TB  | 
| Maximális dtu-k | 5 | 3000 | 4000 | |
||||||

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>A rugalmas készlet edtu-ra, a tároló és a készletezett adatbázis korlátok

| | **Basic** | **Standard** | **Prémium** | 
| :-- | --: | --: | --: | --: |
| Maximális tárhelyméretet adatbázis  | 2 GB | 1 TB | 1 TB | 
| Készlet maximális tárhelyméretet | 156 GB | 4 TB | 4 TB | 
| Maximális edtu-k adatbázisonkénti | 5 | 3000 | 4000 | 
| Maximális edtu-k száma | 1600 | 3000 | 4000 | 
| Készletenként adatbázisok maximális számát | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Több mint 1 TB-nyi tárhelyre prémium tarifacsomagra a jelenleg rendelkezésre áll a következő kivételével minden régióban: Egyesült Királyság északi régiója, nyugati középső Régiójában, UK South2, Kína keleti, USDoDCentral, Németország központi, USDoDEast, USA – (kormányzati) délnyugati, Velünk – (kormányzati) Dél központi, Németország szerepel, Kína Északi, az USA – (kormányzati) keleti. Más régiókban a Prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="next-steps"></a>További lépések

- További részletek a meghatározott teljesítményszintet és a tárolási méret választható az önálló adatbázisok: [SQL Database DTU-alapú erőforrás-korlátozások az önálló adatbázisok](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- További részletek a meghatározott teljesítményszintet és a tároló mérete lehetőségeit a rugalmas: [SQL Database DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).