<properties
    pageTitle="Az SQL Database teljesítménye és beállításai: Szolgáltatásszintek | Microsoft Azure"
    description="Hasonlítsa össze az SQL Database teljesítményét és a szolgáltatásszintek üzleti folytonossági funkcióit a költségek és a képességek kiegyensúlyozására a skálázás során."
    keywords="adatbázis-beállítások, adatbázis-teljesítmény"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>


# Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken

Az [Azure SQL Database](sql-database-technical-overview.md) három, különböző teljesítményszintet képviselő szolgáltatásszinttel rendelkezik a különböző számítási feladatok kezelésére. Mindegyik teljesítményszint egy növekvő erőforráskészletet nyújt az egyre növekvő adatátvitel kézbesítéséhez. Minden adatbázis a saját teljesítményszintjének megfelelő [szolgáltatásszinten](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) kezelhető. Több adatbázis együtt is kezelhető egy [rugalmas készleten](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) belül, ahol közös erőforráskészlettel rendelkeznek. Az önálló adatbázisok számára elérhető erőforrásokat Database Transaction Unitokkal (Adatbázisok tranzakciós egységeivel, DTU-kkal) fejezzük ki, a rugalmas készletek számára elérhető erőforrásokat pedig rugalmas DTU-kkal, (eDTU-kkal). További információ a DTU-król és eDTU-król: [Mi az a DTU?](sql-database-what-is-a-dtu.md) 

A lehetséges szolgáltatásszintek mindkét esetben az **Alapszintű**, a **Standard** és a **Prémium**. A szolgáltatásszintek adatbázis-beállításai hasonlóak a különálló adatbázisok és a rugalmas készletek esetén, de a rugalmas készleteknél további szempontokat is figyelembe kell venni. Ez a cikk az önálló adatbázisok és a rugalmas készletek szolgáltatásszintjeinek részleteit ismerteti.

## Szolgáltatásszintek és adatbázis-beállítások
A Basic, Standard és Premium szolgáltatásszintek egyaránt 99,99 százalékos, szolgáltatói szerződésben (SLA) garantált hasznos üzemidőt, valamint megbízható teljesítményt, rugalmas üzletmenet-folytonossági funkciókat, biztonsági szolgáltatásokat és óradíj alapú számlázást nyújtanak. Az alábbi táblázat példákat tartalmaz arra vonatkozóan, hogy a különböző alkalmazások és teljesítményprofilok esetén melyik a legmegfelelőbb szolgáltatásszint.

| Szolgáltatásszint | Kívánt teljesítményprofilok |
|---|---|
| **Basic** | Leginkább kis adatbázisokhoz felel meg, egyidejűleg általában egyetlen aktív műveletet támogat. Alkalmas például fejlesztési vagy tesztelési célokra használt adatbázisokhoz vagy kisméretű, ritkán használt alkalmazásokhoz. |
| **Standard** | Ez az első számú megoldás a felhőalapú alkalmazások többségéhez, egyidejűleg több lekérdezést támogat. Alkalmas például munkacsoportokhoz vagy webalkalmazásokhoz. |
| **Prémium** | Nagy mennyiségű tranzakcióhoz tervezve, és számos egyidejű felhasználót támogat, továbbá az üzletmenet folytonosságának legmagasabb szintű képességeit követeli meg. Alkalmas például az üzletmenet szempontjából kritikus alkalmazásokat támogató adatbázisokhoz. |

>[AZURE.NOTE] A Web és Business kiadást kivezettük. Ha továbbra is a Web és Business kiadás használatát tervezi, olvassa el a [kivonásokra vonatkozó gyakran ismételt kérdéseket](https://azure.microsoft.com/pricing/details/sql-database/web-business/) ismertető részt.

## Önálló adatbázis-szolgáltatásszintek és -teljesítményszintek
Az önálló adatbázisokhoz minden szolgáltatásszinten több teljesítményszint áll rendelkezésre. Így rugalmasan kiválaszthatja a számítási feladatnak leginkább megfelelő teljesítményszintet. Ha felfelé vagy lefelé kell méreteznie, könnyedén módosíthatja az adatbázis szintjeit. További részleteket az [Adatbázis szolgáltatásszintjeinek és teljesítményszintjeinek módosítása](sql-database-scale-up.md) című részben talál.

Az itt felsorolt teljesítményjellemzők az [SQL Database 12 verzió](sql-database-v12-whats-new.md) használatával létrehozott adatbázisokra érvényesek. Az üzemeltetett adatbázisok számától függetlenül az adatbázis számára garantálva van az erőforrások készlete, és az adatbázis elvárt teljesítményjellemzői nem változnak.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] A szolgáltatásszint táblázatának többi sorára vonatkozó részletes magyarázatokat a [Szolgáltatásszintek lehetőségei és korlátai](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) részben találja.

## Rugalmas készlet szolgáltatásszintjei és teljesítménye eDTU-kban megadva
Különálló adatbázis létrehozásán és méretezésén kívül több adatbázist is kezelhet egy [rugalmas készleten](sql-database-elastic-pool.md) belül. Az egy rugalmas készleten belül lévő adatbázisok közös erőforráskészlettel rendelkeznek. A teljesítményjellemzők *eDTU-kban* vannak megadva. Az önálló adatbázisokhoz hasonlóan a készletek három szolgáltatásszinten érhetők el: **Alapszintű**, **Standard** és **Prémium**. Az általános teljesítménykorlátok és számos jellemző a szolgáltatásszintektől függenek.

A készletek lehetővé teszik az adatbázisok számára a DTU-erőforrások anélkül történő megosztását és felhasználását, hogy egy adott teljesítményszintet kellene hozzárendelni a készletben lévő minden egyes adatbázishoz. Például egy különálló adatbázis egy Standard adatbáziskészletben használhat 0 eDTU-t, de használhatja akár az adatbáziskészlet konfigurálásakor beállított maximális eDTU-t is. A készletek különböző munkaterhelésű adatbázisok esetén lehetővé teszik az egész adatbáziskészlet számára rendelkezésre álló eDTU-erőforrások hatékony használatát. A részleteket a [Rugalmas készletek ára és teljesítménye](sql-database-elastic-pool-guidance.md) részben találja.

Az alábbi táblázat ismerteti az adatbáziskészlet szolgáltatásszintjeinek jellemzőit.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

A készletben lévő minden adatbázis követi az adott szinthez tartozó önálló adatbázis jellemzőit. Az Alapszintű készlet például egy készletenként maximálisan 4 800–28 800 értékű munkamenet-korláttal rendelkezik, az Alapszintű készletben lévő önálló adatbázis esetén azonban 300 munkamenet az adatbázis korlátja.

## Szolgáltatásszint kiválasztása

A szolgáltatásszintre vonatkozó döntés meghozatalához először határozza meg, hogy az adatbázis egy önálló adatbázis legyen vagy egy rugalmas készlet része. 

### Szolgáltatásszint kiválasztása önálló adatbázishoz

Egy önálló adatbázishoz tartozó szolgáltatásszintre vonatkozó döntés meghozatalához először határozza meg az SQL Database-kiadás kiválasztásához szükséges adatbázis-jellemzőket:

- Az adatbázis mérete (legfeljebb 2 GB Alapszintű, legfeljebb 250 GB Standard és legfeljebb 500 GB–1 TB Prémium szint esetén – a teljesítményszinttől függően)
- Az adatbázis biztonsági másolatainak megőrzési ideje (7 nap Alapszintű, 35 nap Standard és 35 nap Prémium szint esetén)

Miután eldöntötte, hogy az SQL Database melyik kiadását használja, készen áll az adatbázis teljesítményszintjének (a DTU-k számának) meghatározására. Ezt megteheti becslés alapján, majd [dinamikusan felfelé vagy lefelé méretezheti](sql-database-scale-up.md) a tényleges tapasztalatok alapján. A [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével is megbecsülheti a szükséges DTU-k számát. 

### Szolgáltatásszint kiválasztása rugalmas adatbáziskészlet számára.

Egy rugalmas adatbáziskészlethez tartozó szolgáltatásszintre vonatkozó döntés meghozatalához először határozza meg a készlet szolgáltatásszintjének kiválasztásához szükséges adatbázis-jellemzőket.

- Az adatbázis mérete (2 GB Alapszintű, 250 GB Standard és 500 GB Prémium szint esetén)
- Az adatbázis biztonsági másolatainak megőrzési ideje (7 nap Alapszintű, 35 nap Standard és 35 nap Prémium szint esetén)
- Az adatbázisok száma készletenként (400 Alapszintű, 400 Standard és 50 Prémium szint esetén)
- Maximális tárhely készletenként (117 GB Alapszintű, 1200 GB Standard és 750 GB Prémium szint esetén)

Miután eldöntötte, hogy a készlethez melyik szolgáltatásszintet használja, készen áll a készlet teljesítményszintjének (eDTU-k) meghatározására. Ezt megteheti becslés alapján, majd [dinamikusan felfelé vagy lefelé méretezheti](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) a tényleges tapasztalatok alapján. A [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) használatával is megbecsülheti a készletben lévő egyes adatbázisokhoz szükséges DTU-k számát, amely segít a készlet felső korlátjának meghatározásában.

## Következő lépések
- További információkat a szolgáltatásszintek árképzéséről az [SQL Database - Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/) című részben talál.
- Ismerje meg a [rugalmas készletek](sql-database-elastic-pool-guidance.md) és a [rugalmas készletek árral és teljesítménnyel kapcsolatos megfontolásainak](sql-database-elastic-pool-guidance.md) részleteit.
- Ismerje meg, hogyan történik a [rugalmas készletek megfigyelése, kezelése és átméretezése](sql-database-elastic-pool-manage-portal.md), valamint az [önálló adatbázisok teljesítményének megfigyelése](sql-database-single-database-monitor.md).
- Most, hogy megismerkedett az SQL Database szolgáltatásszintjeivel, próbálja ki őket [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/), és [hozza létre első SQL-adatbázisát](sql-database-get-started.md).

## További források

A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md).



<!--HONumber=Oct16_HO1-->


