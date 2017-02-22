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
ms.date: 01/11/2017
ms.author: carlrab; janeng
translationtype: Human Translation
ms.sourcegitcommit: 0a00aff343bfd31c956f6cbc831e89cc1cc84b23
ms.openlocfilehash: 95ae4bd67b7d08755035e7b5559ca9648d45bdaa


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken
Az [Azure SQL Database](sql-database-technical-overview.md) három, különböző teljesítményszintet képviselő szolgáltatásszinttel rendelkezik a különböző számítási feladatok kezelésére: **Alapszintű**, **Standard** és **Prémium**. A magasabb teljesítményszintek növekvő erőforrásokat nyújtanak az egyre növekvő adatátvitel kiszolgálásához. [A szolgáltatásszintek és a teljesítményszintek dinamikusan](sql-database-scale-up.md) is módosíthatók, állásidő nélkül. Az Alapszintű, Standard és Prémium szolgáltatásszintek egyaránt 99,99 százalékos, szolgáltatói szerződésben (SLA) garantált hasznos üzemidőt, rugalmas üzletmenet-folytonossági funkciókat, biztonsági szolgáltatásokat és óradíj alapú számlázást nyújtanak. 

Létrehozhat önálló adatbázisokat dedikált erőforrásokkal a választott [teljesítményszinten](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels). Több adatbázis együtt is kezelhető egy [rugalmas készleten](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) belül, amelyben az adatbázisok osztoznak az erőforrásokon. Az önálló adatbázisok számára elérhető erőforrásokat Database Transaction Unitokkal (Adatbázisok tranzakciós egységeivel, DTU-kkal) fejezzük ki, a rugalmas készletek számára elérhető erőforrásokat pedig rugalmas DTU-kkal, (eDTU-kkal). További információ a DTU-król és eDTU-król: [Mi az a DTU?](sql-database-what-is-a-dtu.md). 

A lehetséges szolgáltatásszintek mindkét esetben az **Alapszintű**, a **Standard** és a **Prémium**. 

## <a name="choosing-a-service-tier"></a>Szolgáltatásszint kiválasztása
Az alábbi táblázat példákat tartalmaz arra vonatkozóan, hogy a különböző alkalmazások és teljesítményprofilok esetén melyik a legmegfelelőbb szolgáltatásszint.

| Szolgáltatásszint | Kívánt teljesítményprofilok |
| :--- | --- |
| **Basic** | Leginkább kis adatbázisokhoz felel meg, egyidejűleg általában egyetlen aktív műveletet támogat. Alkalmas például fejlesztési vagy tesztelési célokra használt adatbázisokhoz vagy kisméretű, ritkán használt alkalmazásokhoz. |
| **Standard** |Ez az első számú megoldás az alacsony és közepes I/O-teljesítményigényű felhőalapú alkalmazások többségéhez, egyidejűleg több lekérdezést támogat. Alkalmas például munkacsoportokhoz vagy webalkalmazásokhoz. |
| **Prémium** | Nagy mennyiségű, nagy I/O-teljesítményigényű tranzakcióhoz tervezve, számos egyidejű felhasználót támogat. Alkalmas például az üzletmenet szempontjából kritikus alkalmazásokat támogató adatbázisokhoz. |

Először döntse el, hogy önálló adatbázist szeretne-e futtatni, vagy az adatbázisokat csoportosítani szeretné-e, hogy osztozzanak az erőforrásokon. Tekintse át [a rugalmas készletekre vonatkozó megfontolásokat](sql-database-elastic-pool-guidance.md). A szolgáltatásszintre vonatkozó döntés meghozatalakor először határozza meg a minimálisan szükséges adatbázis-jellemzőket:

* Az adatbázis maximális mérete az önálló adatbázisok esetében (legfeljebb 2 GB az Alapszintű, legfeljebb 250 GB a Standard és legfeljebb 500 GB–1 TB a Prémium szint felsőbb teljesítményszintjei esetén)
* Maximális összes tárhely rugalmas készlet esetében (117 GB az Alapszintű, 1200 GB a Standard és 750 GB a Prémium szint esetén)
* Az adatbázisok maximális száma készletenként (400 az Alapszintű, 400 a Standard és 50 a Prémium szint esetén)
* Az adatbázis biztonsági másolatainak megőrzési ideje (7 nap az Alapszintű, 35 nap a Standard és a Prémium szint esetén)

Miután eldöntötte a minimális szolgáltatási szintet, készen áll az adatbázis teljesítményszintjének (a DTU-k számának) meghatározására. A legtöbb esetben a standard S2 és S3 teljesítményszintek valamelyike jó kiindulási pont lehet. A magas CPU- vagy I/O-igényekkel rendelkező adatbázisok esetében a Prémium teljesítményszintek valamelyike a megfelelő kiindulási pont. A Prémium szint nagyobb CPU-teljesítményt és legalább 10-szer akkora I/O-teljesítményt kínál, mint akár a legmagasabb Standard teljesítményszint.

Miután kiválasztotta a kezdő teljesítményszintet, az [önálló adatbázist](sql-database-scale-up.md) vagy [rugalmas készletet](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) aztán dinamikusan felfelé vagy lefelé méretezheti a tényleges tapasztalatok alapján. Áttelepítési forgatókönyvek esetén a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével is megbecsülheti a szükséges DTU-k számát. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

## <a name="single-database-service-tiers-and-performance-levels"></a>Önálló adatbázis szolgáltatásszintjei és teljesítményszintjei
Az önálló adatbázisokhoz minden szolgáltatásszinten több teljesítményszint áll rendelkezésre. Így rugalmasan kiválaszthatja a számítási feladatnak leginkább megfelelő teljesítményszintet. Ha felfelé vagy lefelé kell méreteznie, könnyedén módosíthatja az adatbázis szintjeit. További részleteket az [Adatbázis szolgáltatásszintjeinek és teljesítményszintjeinek módosítása](sql-database-scale-up.md) című részben talál.

Az üzemeltetett adatbázisok számától függetlenül az adatbázis számára garantálva van az erőforrások készlete, és az adatbázis elvárt teljesítményjellemzői nem változnak.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> A szolgáltatásszint táblázatának többi sorára vonatkozó részletes magyarázatokat a [Szolgáltatásszintek lehetőségei és korlátai](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) részben találja.
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Rugalmas készlet szolgáltatásszintjei és teljesítménye eDTU-kban megadva

A készletek lehetővé teszik az adatbázisok számára az eDTU-erőforrások anélkül történő megosztását és felhasználását, hogy egy adott teljesítményszintet kellene hozzárendelni a készletben lévő minden egyes adatbázishoz. Például egy önálló adatbázis egy Standard adatbáziskészletben használhat akár 0 eDTU-t, de használhatja akár az adatbáziskészlet konfigurálásakor beállított maximális eDTU-t is. A készletek különböző munkaterhelésű adatbázisok esetén lehetővé teszik az egész adatbáziskészlet számára rendelkezésre álló eDTU-erőforrások hatékony használatát. A részleteket a [Rugalmas készletek ára és teljesítménye](sql-database-elastic-pool-guidance.md) részben találja.

Az alábbi táblázat ismerteti az adatbáziskészlet szolgáltatásszintjeinek jellemzőit.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Emellett a készletben lévő összes adatbázis követi az adott szinthez tartozó önálló adatbázisok jellemzőit. Az Alapszintű készlet például egy készletenként maximálisan 4 800–28 800 értékű munkamenet-korláttal rendelkezik, az Alapszintű készletben lévő önálló adatbázis esetén azonban 300 munkamenet az adatbázis korlátja.


## <a name="next-steps"></a>Következő lépések

* Ismerje meg a [rugalmas készletek](sql-database-elastic-pool-guidance.md) és a [rugalmas készletek árral és teljesítménnyel kapcsolatos megfontolásainak](sql-database-elastic-pool-guidance.md) részleteit.
* Ismerkedjen meg részletesebben a [rugalmas készletek figyelésével, kezelésével és átméretezésével](sql-database-elastic-pool-manage-portal.md) és az [önálló adatbázisok teljesítményének figyelésével](sql-database-single-database-monitor.md).
* Most, hogy megismerkedett az SQL Database szolgáltatásszintjeivel, próbálja ki őket [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/), és [hozza létre első SQL-adatbázisát](sql-database-get-started.md).




<!--HONumber=Jan17_HO2-->


