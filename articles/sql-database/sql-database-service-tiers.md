<properties
    pageTitle="Az SQL Database teljesítménye és beállításai: Szolgáltatásszintek | Microsoft Azure"
    description="Hasonlítsa össze az SQL Database teljesítményét és a szolgáltatásszintek üzleti folytonossági funkcióit a költségek és a képességek kiegyensúlyozására a skálázás során."
    keywords="database options,database performance"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="05/13/2016"
    ms.author="carlrab"/>

# Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken

Az [Azure SQL Database](sql-database-technical-overview.md) több szolgáltatásszinttel rendelkezik a különböző számítási feladatok kezelésére. A szolgáltatásszintet bármikor módosíthatja az alkalmazás működésének szüneteltetése nélkül. [Önálló adatbázist](sql-database-get-started.md) is létrehozhat meghatározott jellemzőkkel és árképzéssel. Lehetősége van több adatbázis kezelésére is [rugalmas adatbáziskészlet létrehozásával](sql-database-elastic-pool-create-portal.md). A lehetséges szolgáltatásszintek mindkét esetben a **Basic**, a **Standard** és a **Premium**. A szolgáltatásszintek adatbázis-beállításai hasonlóak az önálló adatbázis és a rugalmas készletek esetén, de a rugalmas készleteknél további szempontokat is figyelembe kell venni. A jelen cikkünk részletesen ismerteti a szolgáltatásszinteket önálló és rugalmas adatbázisok esetén.

## Szolgáltatásszintek és adatbázis-beállítások
A Basic, Standard és Premium szolgáltatásszintek egyaránt 99,99 százalékos, szolgáltatói szerződésben (SLA) garantált hasznos üzemidőt, valamint megbízható teljesítményt, rugalmas üzletmenet-folytonossági funkciókat, biztonsági szolgáltatásokat és óradíj alapú számlázást nyújtanak. Az alábbi táblázat példákat tartalmaz arra vonatkozóan, hogy a különböző alkalmazások és teljesítményprofilok esetén melyik a legmegfelelőbb szolgáltatásszint.

| Szolgáltatásszint | Kívánt teljesítményprofilok |
|---|---|
| **Basic** | Leginkább kis adatbázisokhoz felel meg, egyidejűleg általában egyetlen aktív műveletet támogat. Alkalmas például fejlesztési vagy tesztelési célokra használt adatbázisokhoz vagy kisméretű, ritkán használt alkalmazásokhoz. |
| **Standard** | Ez az első számú megoldás a felhőalapú alkalmazások többségéhez, egyidejűleg több lekérdezést támogat. Alkalmas például munkacsoportokhoz vagy webalkalmazásokhoz. |
| **Premium** | Sok egyidejű tranzakcióhoz tervezték, egyidejűleg nagy számú felhasználót támogat, és a legmagasabb szintű üzleti folytonossági funkciókat nyújtja. Alkalmas például az üzletmenet szempontjából kritikus alkalmazásokat támogató adatbázisokhoz. |

>[AZURE.NOTE] A Web és Business kiadást kivezettük. Ha továbbra is a Web és Business kiadás használatát tervezi, kérjük, olvassa el a [Napnyugta: gyakran ismételt kérdések](https://azure.microsoft.com/pricing/details/sql-database/web-business/) részt.

## Önálló adatbázis szolgáltatásszintjei és teljesítményszintjei
Az önálló adatbázisok esetén minden szolgáltatásszinten belül több teljesítményszint található. Így rugalmasan kiválaszthatja a számítási feladatnak leginkább megfelelő teljesítményszintet. Ha vertikális fel- vagy leskálázásra van szükség, könnyen módosíthatja az adatbázis szolgáltatásszintjeit **az alkalmazás leállása nélkül.** További részleteket az [Adatbázis szolgáltatásszintjeinek és teljesítményszintjeinek módosítása](sql-database-scale-up.md) című részben talál.

Az itt felsorolt teljesítményjellemzők az [SQL Database 12 verzió](sql-database-v12-whats-new.md) használatával létrehozott adatbázisokra érvényesek. Ha az alapul szolgáló hardver az Azure platformon több adatbázist üzemeltet, akkor adatbázisa számára továbbra is garantált a szükséges erőforráskészlet, és az adatbázis elvárt teljesítményjellemzői nem változnak.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

A DTU-kra vonatkozó további részletekért olvassa el a jelen témakörben található, a [DTU-kkal foglalkozó szakaszt](#understanding-dtus).

>[AZURE.NOTE] A szolgáltatásszint táblázatának többi sorára vonatkozó részletes magyarázatokat a [Szolgáltatásszintek lehetőségei és korlátai](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) részben találja.

## Rugalmas készlet szolgáltatásszintjei és teljesítménye eDTU-kban megadva
Önálló adatbázis létrehozásán és skálázásán kívül több adatbázist is kezelhet egy [rugalmas készleten](sql-database-elastic-pool.md) belül. Az egy rugalmas készleten belül lévő adatbázisok mind közös erőforráskészlettel rendelkeznek. A teljesítményjellemzők *eDTU-kban* vannak megadva. A önálló adatbázisokhoz hasonlóan a rugalmas adatbáziskészleteknél is három szolgáltatásszint érhető el: **Basic**, **Standard** és **Premium**. Az általános teljesítménykorlátok és számos jellemző a szolgáltatásszintektől függenek.

Az adatbáziskészletek segítségével a rugalmas adatbázisok képesek DTU-erőforrásokat megosztani és használni anélkül, hogy az adatbázishoz hozzá kellene rendelni egy bizonyos teljesítményszintet az adatbáziskészletben. Például egy önálló adatbázis egy Standard adatbáziskészletben használhat akár 0 eDTU-t, de használhatja akár az adatbáziskészlet konfigurálásakor beállított maximális eDTU-t is. Ez különböző munkaterhelésű adatbázisok esetén lehetővé teszi az egész adatbáziskészlet számára rendelkezésre álló eDTU-erőforrások hatékony használatát. A részleteket a [Rugalmas készletek ára és teljesítménye](sql-database-elastic-pool-guidance.md) részben találja.

Az alábbi táblázat ismerteti az adatbáziskészlet szolgáltatásszintjeinek jellemzőit.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Az adott adatbáziskészleten belüli egyes adatbázisok megőrzik az adott szolgáltatásszinthez tartozó önálló adatbázis jellemzőit is. Például egy Basic adatbáziskészlet munkameneteinek maximális száma 4800–28 800, de az adatbáziskészleten belül egy önálló adatbázis munkameneteinek maximális száma 300 (a Basic önálló adatbázisra vonatkozó korlátozás az előző szakaszban meghatározottak szerint).

## A DTU-k ismertetése

[AZURE.INCLUDE [SQL DB DTU description](../../includes/sql-database-understanding-dtus.md)]

## Következő lépések
- További információkat a szolgáltatásszintek árképzéséről az [SQL Database - Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/) című részben talál.
- Ismerje meg a [rugalmas adatbáziskészletekkel](sql-database-elastic-pool-guidance.md) és a [rugalmas adatbáziskészletek árával és teljesítményével ](sql-database-elastic-pool-guidance.md) kapcsolatos részleteket.
- Ismerkedjen meg részletesebben a [rugalmas készletek figyelésével, kezelésével és átméretezésével](sql-database-elastic-pool-manage-portal.md) és az [önálló adatbázisok teljesítményének figyelésével](sql-database-single-database-monitor.md).
- Most, hogy megismerkedett az SQL Database szolgáltatásszintjeivel, próbálja ki őket [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/), és [hozza létre első SQL-adatbázisát](sql-database-get-started.md).



<!--HONumber=Jun16_HO2-->


