---
title: Az Azure SQL Database szolgáltatás - DTU |} Microsoft Docs
description: Ismerje meg egyetlen szolgáltatásszintek és teljesítményszintek és tárolási méretek készlet adatbázisok.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648649"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Az Azure SQL Database alapjául szolgáló vásárlási modell DTU-alapú 


[Az Azure SQL Database](sql-database-technical-overview.md) két vásárlási modell számítási, tárolási és IO erőforrásokat kínál: a DTU-alapú alapjául szolgáló vásárlási modell és a vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió). A következő diagram és táblázat összehasonlítása és két vásárlási modell.

> [!IMPORTANT]
> VCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), lásd: [alapjául szolgáló vásárlási modell vCore-alapú](sql-database-service-tiers-vcore.md)


|**Alapjául szolgáló vásárlási modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell egy számítási, tárolási és IO erőforrások csomagolt mértéken alapul. A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k](sql-database-what-is-a-dtu.md)?|Ajánlott az ügyfelek számára, akik egyszerű, előre konfigurált beállításai.| 
|vCore alapuló modell|Ez a modell lehetővé teszi, hogy egymástól függetlenül válik a számítási és tárolási erőforrások. Lehetővé teszi az SQL Server Azure hibrid juttatás segítségével költséghatékony.|Az ügyfelek, akik a rugalmasságot, a vezérlési és az átláthatóság érték legmegfelelőbb.|
||||  

![árképzési modellt](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>DTU-alapú alapjául szolgáló vásárlási modell

Az adatbázis-átviteli Unit (DTU) jelöli kevert méri, Processzor, memória, beolvassa, és. A DTU-alapú alapjául szolgáló vásárlási modell előre konfigurált együtteséből áll számítási erőforrások olyan készletét kínálja, és a meghajtó különböző szintű alkalmazásteljesítmény-tároló tartalmazza. Az ügyfelek, akik inkább az egyszerűség kedvéért egy előre konfigurált csomagot és a rögzített nagyságú befizetések havonta, előfordulhat, hogy keresse meg az DTU-alapú igényeiknek ideális. A DTU-alapú alapjául szolgáló vásárlási modell, az ügyfelek választhat **alapvető**, **szabványos**, és **prémium** szolgáltatásszintek mindkét [adatbázisokegyszeri](sql-database-single-database-resources.md) és [rugalmas készletek](sql-database-elastic-pool.md). Számos különböző teljesítményszintek az belefoglalt tárolási, megőrzési időszak biztonsági mentések és a rögzített ár rögzített méretű szerint megkülönböztetett forgalomosztályból a szolgáltatási szinteket. Az összes szolgáltatási szinteket a leállás nélküli teljesítményszintet változó rugalmasságot biztosítanak. Önálló adatbázisok és rugalmas készletek számlázása óránként és teljesítményszintet szolgáltatásszint alapján.

> [!IMPORTANT]
> SQL adatbázis-felügyelt példány jelenleg nyilvános előzetes verziójában nem támogatja a DTU-alapú alapjául szolgáló vásárlási modell. További információkért lásd: [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>A DTU-alapú alapjául szolgáló vásárlási modell a szolgáltatási réteg kiválasztása

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Teljesítmény szintjét és a tárolási méretkorlátait a DTU-alapú alapjául szolgáló vásárlási modell a

A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k](sql-database-what-is-a-dtu.md)?

### <a name="single-databases"></a>Önálló adatbázisok

||Alapszintű|Standard|Prémium|
| :-- | --: | --: | --: | --: |
| Maximális méret * | 2 GB | 1 TB | 4 TB  | 
| Maximális dtu-k | 5 | 3000 | 4000 | |
||||||

További részletek a meghatározott teljesítményszintet és a tárolási méret választható az önálló adatbázisok: [SQL Database DTU-alapú erőforrás-korlátozások az önálló adatbázisok](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

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
> - A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és további költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
> - Több mint 1 TB-nyi tárhelyre a Premium szinten lévő érhető el minden régióban, kivéve a következőt: Egyesült Királyság északi régiója, nyugati középső Régiójában, UK South2, Kína keleti, USDoDCentral, Németország központi, USDoDEast, USA – (kormányzati) délnyugati, Velünk – (kormányzati) Dél központi, Németország szerepel, Kína Észak, Velünk – (kormányzati) Keleti. A jövőben a lefedettség bővülni fog. Más régiókban a prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
További részletek a meghatározott teljesítményszintet és a tároló mérete lehetőségeit a rugalmas: [SQL Database DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>További lépések

- A meghatározott teljesítményszintet és a tárolási méret választható részletekért lásd: [SQL Database DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md) és [SQL-adatbázis vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md).
- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- További tudnivalók [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)
