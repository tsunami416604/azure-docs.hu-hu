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
ms.date: 02/28/2018
ms.author: carlrab
ms.openlocfilehash: ebfd44098a81b4a9d8863623458460ceb25f0d5d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Mik az Azure SQL Database szolgáltatási szinteket?

[Az Azure SQL Database](sql-database-technical-overview.md) kínál **alapvető**, **szabványos**, és **prémium** szolgáltatásszintek mindkét [adatbázisok egyszeri](sql-database-single-database-resources.md) és [rugalmas készletek](sql-database-elastic-pool.md). SQL-adatbázis nyújt az általános célú szolgáltatásréteg [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md#managed-instance-service-tier). Szolgáltatásszintek elsősorban számos teljesítményszint szükséges és a tároló mérete lehetőségek és az ár szerint megkülönböztetett forgalomosztályból meg.  Az összes szolgáltatási szinteket teljesítmény szintjét és a tároló méretének módosítása rugalmasságot biztosítanak.  Önálló adatbázisok és rugalmas készletek számlázása óránként szolgáltatási rétegben, a teljesítményszintet és a tárméret alapján.   

> [!IMPORTANT]
> SQL adatbázis-felügyelt példány jelenleg a nyilvános előzetes egy általános célú szolgáltatásréteg nyújt. További információkért lásd: [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md). Ez a cikk fennmaradó kezelt példány nem vonatkozik.

## <a name="choosing-a-service-tier"></a>Szolgáltatásszint kiválasztása

Elsősorban az üzletmenet folytonosságát, a tároló és a teljesítményre vonatkozó követelmények attól függ, egy szolgáltatási réteg kiválasztása.
| | **Basic** | **Standard** |**Prémium**  |
| :-- | --: |--:| --:| --:| 
|Cél munkaterhelés|Fejlesztési és éles|Fejlesztési és éles|Fejlesztési és éles||
|SLA-ban garantált üzemidő|99.99%|99.99%|99.99%|Nincs kép nézetben|
|Biztonsági mentés megőrzése|7 nap|35 nap|35 nap|
|CPU|Alacsony|Alacsony, közepes, magas|Közepes, magas|
|IO átviteli sebesség (hozzávetőleges) |2.5 DTU iops-értéket  | 2.5 DTU iops-értéket | DTU 48 iops-értéket|
|I/O várakozási ideje (hozzávetőleges)|10 ms (írás) (olvasás), 5 ms|10 ms (írás) (olvasás), 5 ms|2 ms (olvasás/írás)|
|Oszlopcentrikus indexelő és a memórián belüli online Tranzakciófeldolgozási|–|–|Támogatott|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Teljesítmény-szintjét és a tárolási méretkorlátai

A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Önálló adatbázisok

|  | **Basic** | **Standard** | **Prémium** | 
| :-- | --: | --: | --: | --: |
| Maximális méret * | 2 GB | 1 TB | 4 TB  | 
| Maximális dtu-k | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>Rugalmas készletek

| | **Basic** | **Standard** | **Prémium** | 
| :-- | --: | --: | --: | --: |
| Maximális tárhelyméretet adatbázis *  | 2 GB | 1 TB | 1 TB | 
| Maximális tárhelyméretet a készlet * | 156 GB | 4 TB | 4 TB | 
| Maximális edtu-k adatbázisonkénti | 5 | 3000 | 4000 | 
| Maximális edtu-k száma | 1600 | 3000 | 4000 | 
| Készletenként adatbázisok maximális számát | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, USA középső régiója, Közép-Franciaország, Közép-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, az Egyesült Királyság déli régiója, az Egyesült Királyság nyugati régiója, USA keleti régiója 2, USA nyugati régiója, USA-beli államigazgatás – Virginia, és Nyugat-Európa. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

További részletek a meghatározott teljesítményszintet és a tároló mérete elérhető lehetőségek: [SQL-adatbázis erőforrás korlátok](sql-database-resource-limits.md).


## <a name="next-steps"></a>További lépések

- További tudnivalók [egy adatbázis-erőforrások](sql-database-single-database-resources.md).
- További tudnivalók a rugalmas készletek című [rugalmas készletek](sql-database-elastic-pool.md).
- További tudnivalók [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)
* További információ [Dtu és edtu-k](sql-database-what-is-a-dtu.md).
* További tudnivalók a figyelés DTU-használatát, lásd: [figyelés és a teljesítmény hangolására](sql-database-troubleshoot-performance.md).

