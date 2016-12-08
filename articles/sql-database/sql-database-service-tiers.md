---
title: "Az SQL Database teljesítménye: Szolgáltatásszintek | Microsoft Docs"
description: "SQL Database-szolgáltatásszintek összehasonlítása."
keywords: "adatbázis-beállítások, adatbázis-teljesítmény"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>SQL Database-szolgáltatásszintek önálló adatbázisokhoz és rugalmas adatbáziskészletekhez
Az [Azure SQL Database](sql-database-technical-overview.md) három, különböző teljesítményszintet képviselő szolgáltatásszinttel rendelkezik a különböző számítási feladatok kezelésére. A magasabb teljesítményszintek egy növekvő erőforráskészletet nyújtanak az egyre növekvő adatátvitel kiszolgálásához. A szolgáltatásszintek és a teljesítményszintek dinamikusan is módosíthatók. További részleteket az [Adatbázis szolgáltatásszintjeinek és teljesítményszintjeinek módosítása](sql-database-scale-up.md) című részben talál.

Minden adatbázis a saját teljesítményszintjének megfelelő [szolgáltatásszinten](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) kezelhető. Több adatbázis együtt is kezelhető egy [rugalmas adatbáziskészleten](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus) belül, ahol közös erőforráskészlettel rendelkeznek. Az önálló adatbázisok számára elérhető erőforrásokat adatbázis-tranzakciós egységekkel (Database Transaction Unit, DTU) fejezzük ki, a rugalmas adatbáziskészletek számára elérhető erőforrásokat pedig rugalmas DTU-kkal (eDTU-kkal). További információ a DTU-król és eDTU-król: [Mi az a DTU?](sql-database-what-is-a-dtu.md) 

A lehetséges szolgáltatásszintek mindkét esetben az **Alapszintű**, a **Standard** és a **Prémium**. 

## <a name="service-tiers"></a>Szolgáltatásszintek
A Basic, Standard és Premium szolgáltatásszintek egyaránt 99,99 százalékos, szolgáltatói szerződésben (SLA) garantált hasznos üzemidőt, valamint megbízható teljesítményt, rugalmas üzletmenet-folytonossági funkciókat, biztonsági szolgáltatásokat és óradíj alapú számlázást nyújtanak. Az alábbi táblázat példákat tartalmaz arra vonatkozóan, hogy a különböző alkalmazások és teljesítményprofilok esetén melyik a legmegfelelőbb szolgáltatásszint.

| Szolgáltatásszint | Kívánt teljesítményprofilok |
| :--- | --- |
| **Basic** |Leginkább kis adatbázisokhoz felel meg, egyidejűleg általában egyetlen aktív műveletet támogat. Alkalmas például fejlesztési vagy tesztelési célokra használt adatbázisokhoz vagy kisméretű, ritkán használt alkalmazásokhoz. |
| **Standard** |Ez az első számú megoldás a felhőalapú alkalmazások többségéhez, egyidejűleg több lekérdezést támogat. Alkalmas például munkacsoportokhoz vagy webalkalmazásokhoz. |
| **Prémium** |Nagy mennyiségű tranzakcióhoz tervezve, és számos egyidejű felhasználót támogat, továbbá az üzletmenet folytonosságának legmagasabb szintű képességeit követeli meg. Alkalmas például az üzletmenet szempontjából kritikus alkalmazásokat támogató adatbázisokhoz. |

> [!NOTE]
> A Web és Business kiadást kivezettük. Ha továbbra is a Web és Business kiadás használatát tervezi, olvassa el a [kivonásokra vonatkozó gyakran ismételt kérdéseket](https://azure.microsoft.com/pricing/details/sql-database/web-business/) ismertető részt.
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>Önálló adatbázis szolgáltatásszintjei és teljesítményszintjei
Az önálló adatbázisokhoz minden szolgáltatásszinten több teljesítményszint áll rendelkezésre. Így rugalmasan kiválaszthatja a számítási feladatnak leginkább megfelelő teljesítményszintet. Ha vertikális felskálázásra vagy leskálázásra van szükség, gyorsan módosíthatja az adatbázis teljesítményszintjét. További részleteket az [Adatbázis szolgáltatásszintjeinek és teljesítményszintjeinek módosítása](sql-database-scale-up.md) című részben talál.

Az itt felsorolt teljesítményjellemzők az [SQL Database 12 verzió](sql-database-technical-overview.md) használatával létrehozott adatbázisokra érvényesek. Az üzemeltetett adatbázisok számától függetlenül az adatbázis számára garantálva van az erőforrások készlete, és az adatbázis elvárt teljesítményjellemzői nem változnak.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> A szolgáltatásszint táblázatának többi sorára vonatkozó részletes magyarázatokat a [Szolgáltatásszintek lehetőségei és korlátai](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) részben találja.
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>A rugalmas adatbáziskészlet szolgáltatásszintjei és teljesítménye eDTU-kban megadva
Több adatbázis együtt is kezelhető egy [rugalmas adatbáziskészleten](sql-database-elastic-pool.md) belül. Az egy rugalmas adatbáziskészleten belül lévő adatbázisok közös erőforráskészlettel rendelkeznek. A teljesítményjellemzők *eDTU-kban* vannak megadva. A önálló adatbázisokhoz hasonlóan a rugalmas adatbáziskészleteknél is három szolgáltatásszint érhető el: **Basic**, **Standard** és **Premium**. Az általános teljesítménykorlátok és számos jellemző a szolgáltatásszintektől függenek.

A készletek lehetővé teszik az adatbázisok számára a DTU-erőforrások anélkül történő megosztását és felhasználását, hogy egy adott teljesítményszintet kellene hozzárendelni a készletben lévő minden egyes adatbázishoz. Például egy adatbázis egy Standard adatbáziskészletben használhat 0 eDTU-t, de használhatja akár az adatbáziskészlet konfigurálásakor beállított maximális eDTU-t is. A készletek különböző munkaterhelésű adatbázisok esetén lehetővé teszik az egész adatbáziskészlet számára rendelkezésre álló eDTU-erőforrások hatékony használatát. A részleteket a [rugalmas adatbáziskészletek árazásának és teljesítményének](sql-database-elastic-pool-guidance.md) leírásában találja.

Az alábbi táblázat ismerteti az Alapszintű, Standard és Prémium rugalmas adatbáziskészletek jellemzőit.

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Emellett a készletben lévő összes adatbázis követi az adott szinthez tartozó önálló adatbázisok jellemzőit. Az Alapszintű készlet például egy készletenként maximálisan 4 800–28 800 értékű munkamenet-korláttal rendelkezik, az Alapszintű készletben lévő önálló adatbázis esetén azonban 300 munkamenet az adatbázis korlátja.

## <a name="choosing-a-service-tier"></a>Szolgáltatásszint kiválasztása
A szolgáltatásszintre vonatkozó döntés meghozatalához először határozza meg, hogy az adatbázis egy önálló adatbázis legyen vagy egy rugalmas adatbáziskészlet része. 

### <a name="choosing-a-service-tier-for-a-single-database"></a>Szolgáltatásszint kiválasztása önálló adatbázishoz
Egy önálló adatbázishoz tartozó szolgáltatásszintre vonatkozó döntés meghozatalakor először határozza meg az SQL Database-kiadás kiválasztásához szükséges adatbázis-jellemzőket:

* Az adatbázis mérete (legfeljebb 2 GB Alapszintű, legfeljebb 250 GB Standard és legfeljebb 500 GB–1 TB Prémium szint esetén – a teljesítményszinttől függően)
* Az adatbázis biztonsági másolatainak megőrzési ideje (7 nap Alapszintű, 35 nap Standard és 35 nap Prémium szint esetén)

Miután eldöntötte, hogy az SQL Database melyik kiadását használja, készen áll az adatbázis teljesítményszintjének (a DTU-k számának) meghatározására. Ezt megteheti becslés alapján, majd [dinamikusan felfelé vagy lefelé méretezheti](sql-database-scale-up.md) a tényleges tapasztalatok alapján. A [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével is megbecsülheti a szükséges DTU-k számát. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Szolgáltatásszint kiválasztása rugalmas adatbáziskészlet számára.
Egy rugalmas adatbáziskészlethez tartozó szolgáltatásszintre vonatkozó döntés meghozatalához először határozza meg a készlet szolgáltatásszintjének kiválasztásához szükséges adatbázis-jellemzőket.

* Az adatbázis mérete (2 GB Alapszintű, 250 GB Standard és 500 GB Prémium szint esetén)
* Az adatbázis biztonsági másolatainak megőrzési ideje (7 nap Alapszintű, 35 nap Standard és 35 nap Prémium szint esetén)
* Az adatbázisok száma készletenként (400 Alapszintű, 400 Standard és 50 Prémium szint esetén)
* Maximális tárhely készletenként (117 GB Alapszintű, 1200 GB Standard és 750 GB Prémium szint esetén)

Miután eldöntötte, hogy a készlethez melyik szolgáltatásszintet használja, készen áll a készlet teljesítményszintjének (eDTU-k) meghatározására. Ezt megteheti becslés alapján, majd [dinamikusan felfelé vagy lefelé méretezheti](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) a tényleges tapasztalatok alapján. A [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) használatával is megbecsülheti a készletben lévő egyes adatbázisokhoz szükséges DTU-k számát, amely segít a készlet felső korlátjának meghatározásában.

## <a name="next-steps"></a>Következő lépések
* További információkat a szolgáltatásszintek árképzéséről az [SQL Database - Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/) című részben talál.
* Ismerje meg a [rugalmas adatbáziskészletekkel](sql-database-elastic-pool-guidance.md) és a [rugalmas adatbáziskészletek árával és teljesítményével ](sql-database-elastic-pool-guidance.md) kapcsolatos részleteket.
* Ismerje meg, hogyan történik a [rugalmas adatbáziskészletek megfigyelése, kezelése és átméretezése](sql-database-elastic-pool-manage-portal.md), valamint az [önálló adatbázisok teljesítményének megfigyelése](sql-database-single-database-monitor.md).
* Most, hogy megismerkedett az SQL Database szolgáltatásszintjeivel, próbálja ki őket [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/), és [hozza létre első SQL-adatbázisát](sql-database-get-started.md).

## <a name="additional-resources"></a>További források
* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Microsoft Virtual Academy videotanfolyam az Azure SQL Database rugalmas adatbázis-funkcióiról](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


