---
title: "Az Azure SQL Database szolgáltatásban |} Microsoft Docs"
description: "Ismerje meg egyetlen szolgáltatásszintek és teljesítményszintek és tárolási méretek készlet adatbázisok."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 55f59fddee008eb42b7252d6368a56873a6abd16
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Mik az Azure SQL Database szolgáltatási szinteket

[Az Azure SQL Database](sql-database-technical-overview.md) kínál **alapvető**, **szabványos**, **prémium**, és **prémium RS** szolgáltatásszintek mindkét a[adatbázisok egyetlen](sql-database-single-database-resources.md) és [rugalmas készletek](sql-database-elastic-pool.md). Szolgáltatásszintek elsősorban számos teljesítményszint szükséges és a tároló mérete lehetőségek és az ár szerint megkülönböztetett forgalomosztályból meg.  Az összes szolgáltatási szinteket teljesítmény szintjét és a tároló méretének módosítása rugalmasságot biztosítanak.  Önálló adatbázisok és rugalmas készletek számlázása óránként szolgáltatási rétegben, a teljesítményszintet és a tárméret alapján.   

## <a name="choosing-a-service-tier"></a>Szolgáltatásszint kiválasztása

Elsősorban az üzletmenet folytonosságát, a tároló és a teljesítményre vonatkozó követelmények attól függ, egy szolgáltatási réteg kiválasztása.
| | **Basic** | **Standard** |**Prémium** |**Prémium szintű RS** |
| :-- | --: |--:| --:| --:| 
|Cél munkaterhelés|Fejlesztési és éles|Fejlesztési és éles|Fejlesztési és éles|Munkaterhelés, amely tűri az adatveszteség legfeljebb 5-perc szolgáltatás hibák miatt|
|SLA-ban garantált üzemidő|99.99%|99.99%|99.99%|Nincs kép nézetben|
|Biztonsági mentés megőrzése|7 nap|35 nap|35 nap|35 nap|
|CPU|Alacsony|Alacsony, közepes, magas|Közepes, magas|Közepes|
|IO átviteli sebesség|Alacsony  | Közepes | Ez ezerszer nagyobb, mint a Standard|Prémium szintű azonos|
|I/O várakozási ideje|Prémium szintű nagyobb|Prémium szintű nagyobb|Alacsonyabb, mint a Basic és Standard|Prémium szintű azonos|
|Oszlopcentrikus indexelő és a memórián belüli online Tranzakciófeldolgozási|N/A|N/A|Támogatott|Támogatott|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Teljesítmény-szintjét és a tárolási méretkorlátai

Az önálló adatbázisok adatbázis-tranzakciós egységek (dtu-k) és a rugalmas adatbázis-tranzakciós egységek (edtu-k) a rugalmas teljesítményszintet vannak kifejezve. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k?](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Önálló adatbázisok

|  | **Basic** | **Standard** | **Prémium** | **Prémium szintű RS**|
| :-- | --: | --: | --: | --: |
| Maximális méret * | 2 GB | 1 TB | 4 TB  | 1 TB  |
| Maximális dtu-k | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>Rugalmas készletek

| | **Basic** | **Standard** | **Prémium** | **Prémium szintű RS**|
| :-- | --: | --: | --: | --: |
| Maximális tárhelyméretet adatbázis *  | 2 GB | 1 TB | 1 TB | 1 TB |
| Maximális tárhelyméretet a készlet * | 156 GB | 4 TB | 4 TB | 1 TB |
| Maximális edtu-k adatbázisonkénti | 5 | 3000 | 4000 | 1000 |
| Maximális edtu-k száma | 1600 | 3000 | 4000 | 1000 |
| Készletenként adatbázisok maximális számát | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: USA 2. keleti régiója, USA nyugati régiója, USA-beli államigazgatás – Virginia, Nyugat-Európa, Közép-Németország, Délkelet-Ázsia, Kelet-Japán, Kelet-Ausztrália, Közép-Kanada és Kelet-Kanada. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

További részletek a meghatározott teljesítményszintet és a tároló mérete elérhető lehetőségek: [SQL-adatbázis erőforrás korlátok](sql-database-resource-limits.md).


## <a name="next-steps"></a>Következő lépések

- További tudnivalók [egy adatbázis-erőforrások](sql-database-single-database-resources.md).
- További tudnivalók a rugalmas készletek című [rugalmas készletek](sql-database-elastic-pool.md).
- További tudnivalók [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)
* További információ [Dtu és edtu-k](sql-database-what-is-a-dtu.md).
* További tudnivalók a figyelés DTU-használatát, lásd: [figyelés és a teljesítmény hangolására](sql-database-troubleshoot-performance.md).

