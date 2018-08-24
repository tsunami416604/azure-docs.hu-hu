---
title: Az Azure SQL-adatbázis teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Ismerje meg a javaslatok használatával Azure SQL Database lekérdezési teljesítmény javításához.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: carlrab
ms.openlocfilehash: 9bf050fba3302de4dba70b128fa3ddc5e824c8bc
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818366"
---
# <a name="tuning-performance-in-azure-sql-database"></a>Az Azure SQL Database teljesítményének hangolása

Az Azure SQL Database által biztosított [javaslatok](sql-database-advisor.md) , hogy segítségével javíthatja az adatbázis teljesítményét, vagy hagyhatja, hogy az Azure SQL Database [automatikusan alkalmazkodik az alkalmazás](sql-database-automatic-tuning.md) és a módosítások alkalmazásához, javítja a számítási feladatok teljesítményére.

Az Ön nincs vonatkozó javaslatok, és továbbra is problémákba ütközik a teljesítmény, a teljesítmény növeléséhez, előfordulhat, hogy használja az alábbi módszerek:
- Növelése a szolgáltatási szinten a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) további erőforrások az adatbázishoz.
- Hallgassa meg az alkalmazást, és a alkalmazni néhány ajánlott eljárást, amely növelheti a teljesítményt. 
- Az adatbázis hangolása indexek és lekérdezések hatékonyabban dolgozhat az adatokkal való módosításával.

Ezek a kézi módszert, mert az erőforrások megfelelnek a mennyisége, hogy az igényeinek. Ellenkező esetben kell írja újra az alkalmazást vagy az adatbázis-kódot, és üzembe helyezése a módosításokat.

## <a name="increasing-performance-tier-of-your-database"></a>Az adatbázis teljesítményszintjét növelése

Az Azure SQL Database által nyújtott két beszerzési modell, egy [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és a egy [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) , amelyek közül választhat. Minden szolgáltatási szint szigorúan közt megadott kezdőkönyvtárra korlátozása, hogy az SQL-adatbázis használhatja, és kiszámítható teljesítményt biztosítanak a szolgáltatási szint garantálja az erőforrásokat. Ez a cikk útmutatást, amely segítségével kiválaszthatja az alkalmazás a szolgáltatási rétegben biztosítunk. Is tárgyaljuk, hogy a legtöbbet az Azure SQL Database-ből az alkalmazás hangolhassa módon.

> [!NOTE]
> Ez a cikk az Azure SQL Database önálló adatbázisok teljesítményének útmutatást összpontosít. Rugalmas készletek kapcsolatos teljesítmény útmutatóért lásd: [rugalmas készletek ára és teljesítménye szempontjai](sql-database-elastic-pool-guidance.md). Vegye figyelembe, ugyanakkor, hogy is a rugalmas készletben található adatbázisok ebben a cikkben a hangolási javaslatokat alkalmazhatja, és hasonló teljesítmény előnyök.
> 

* **Alapszintű**: az alap szolgáltatási szint ajánlatok jó teljesítmény kiszámíthatóságot nyújtanak az egyes adatbázisok, órában óra. Egy alapszintű adatbázisban elegendő erőforrással támogatja a több egyidejű kérelmek nem rendelkező kisméretű adatbázis a jó teljesítmény. Tipikus használati eseteket mutatunk be alapszintű szolgáltatásszint a következők:
  * **Most csak ismerkedik az Azure SQL Database**. Az alkalmazásokat, amelyek gyakran fejlesztés alatt nem szükséges nagy teljesítményű szinteket. Alapszintű adatbázisok ideális környezetet biztosít az adatbázis-fejlesztési vagy tesztelési, mindezt olyan alacsony díjszabással.
  * **Egy adott felhasználóhoz társítva van egy adatbázis**. Az alkalmazásokat, amelyek általában egy felhasználó társítani egy adatbázis nem rendelkezik magas egyidejűség és a teljesítményre vonatkozó követelményeknek. Ezeket az alkalmazásokat az alapszintű szolgáltatásszinten a deduplikációra.
* **Standard szintű**: A Standard szolgáltatási rétegben az adatbázisok, amelyek több egyidejű kérés, például a munkacsoport-és webalkalmazások jó teljesítményt nyújt, és jobb teljesítményt, kiszámíthatóságot kínál. Ha úgy dönt, hogy egy standard szintű service adatbázisba, az adatbázis-alkalmazás a kiszámítható teljesítmény, alapján méretét percnyi perc.
  * **Az adatbázis még több egyidejű kérelmek**. Az alkalmazásokat, amelyek általában egyszerre több felhasználó szolgáltatás szükséges a magasabb teljesítményszintek. Ha például munkacsoportokhoz vagy webalkalmazásokhoz az alkalmazásokat, alacsony, közepes i/o adatforgalomra egyidejűleg több lekérdezést támogat esetén használható jól a Standard szolgáltatásszinten.
* **Prémium szintű**: A prémium szolgáltatáscsomagban biztosítja kiszámítható teljesítmény, második keresztül másodpercenként, az egyes prémium és üzletileg kritikus fontosságú adatbázisokhoz. Ha úgy dönt, a prémium szintű szolgáltatási rétegben, az adatbázis-alkalmazás alapján a csúcsterhelés között az, hogy az adatbázis méretét. A terv eltávolítja az esetek teljesítményszámlálók eltérés okozhat kis lekérdezések a késésre érzékeny műveletek a vártnál több időt vesz igénybe. Ez a modell nagyban leegyszerűsítheti a fejlesztést és a termék érvényesítési ciklusokat, hogy az erős utasítások csúcs erőforrásigényeivel, a teljesítmény eltérés vagy a Lekérdezések késése igénylő alkalmazások esetében. A legtöbb prémium szintű szolgáltatási szint használati esetek egy vagy több, a következő jellemzőkkel rendelkeznek:
  * **Magas csúcsterhelés**. Olyan alkalmazás, amely jelentős CPU, memória, vagy a bemeneti/kimeneti (I/O) a műveletek végrehajtásához szükséges egy dedikált, nagy teljesítményű szintet igényel. Például egy adatbázis-művelet hosszabb ideig több processzormag felhasználásához ismert jelöltségét ellenőrző a prémium szolgáltatásszintet.
  * **Sok egyidejű kéréssel**. Egyes adatbázis-alkalmazások iránti sok egyidejű kérés, például kiszolgáló a webhely, amely rendelkezik a nagy forgalmú kötet. Alapszintű és standard szintű szolgáltatáscsomagban adatbázisonként egyidejű kérelmek számának korlátozásához. Válasszon egy megfelelő foglalási méret kezeléséhez szükséges kérelmek maximális száma több kapcsolatot igénylő alkalmazások kell.
  * **Kis késés**. Egyes alkalmazások biztosítania kell, hogy minimális időben választ az adatbázisból. Ha egy adott tárolt eljárás neve a egy szélesebb körű ügyfél művelet részeként, lehetséges, hogy olyan követelménnyel rendelkezik egy adott hívás visszatérési legfeljebb 20 ezredmásodpercben, az idő 99 százalékában. Ez az alkalmazástípus a prémium szolgáltatásszinten, győződjön meg arról, hogy a szükséges számítási teljesítmény érhető el, hogy előnyös.

A szolgáltatási szint az SQL-adatbázis szükséges minden egyes erőforrás dimenzió csúcs terhelés követelményeitől függ. Egyes alkalmazások egyetlen erőforrás triviális mennyisége használható, de más erőforrások jelentős követelményekkel rendelkeznek.

### <a name="service-tier-capabilities-and-limits"></a>Szolgáltatás szolgáltatásszintek lehetőségei és korlátai

Minden szolgáltatásszinten, a teljesítmény szint beállítása, így csak azért kell fizetnie a kapacitás szükséges rugalmasságot. Is [a kapacitás](sql-database-service-tiers-dtu.md), vagy leskálázásakor, mint a számítási feladat változását követő. Például ha a biztonsági school vásárlási időszak alatt az adatbázis-munkaterhelés nagy, növelheti az adatbázis teljesítményszintjének beállított ideje, július szeptember keresztül. Amikor befejeződik a csúcsidőszak évszak csökkentheti. Minimalizálhatja az üzleti, a szezonalitás a felhőalapú környezet optimalizálásával fizet. Ez a modell is jól szoftver termék kiadási ciklusokhoz használható. Egy teszt csapat előfordulhat, hogy kapacitás foglalása, amíg azt futtatások tesztelése, és engedje, hogy a kapacitás vizsgálat befejezésekor. A kapacitás a kérelem modellben kell fizetnie kapacitás szükséges, és elkerülheti, előfordulhat, hogy a ritkán használt, dedikált erőforrások költségeit.

### <a name="why-service-tiers"></a>Miért szolgáltatásszintek?
Bár minden egyes adatbázis-munkaterhelés eltérőek lehetnek, a szolgáltatási célja, hogy különböző teljesítmény-szintű teljesítmény kiszámíthatóságot nyújtanak. Nagyméretű adatbázis erőforrás-követelményekkel rendelkező ügyfelek több dedikált számítási környezetben dolgozhat.

## <a name="tune-your-application"></a>Az alkalmazás hangolása
A hagyományos helyszíni SQL Server a folyamat a kezdeti a kapacitástervezés során gyakran elkülönül egy alkalmazást az éles üzemben futó folyamat. Hardver- és licenceket vásárolt először, és ezt követően teljesítményhangolás történik. Azure SQL Database használata esetén célszerű interweave alkalmazások futtatásához és a finomhangolás azt a folyamatot. Az igény szerinti kapacitás és a modell hangolhassa az alkalmazás helyett a későbbi növekedési tervek egy alkalmazáshoz, amely gyakran helytelenek Találgatások alapján hardveren túlzott most szükséges minimális erőforrásokat használhatja. Egyes ügyfelek előfordulhat, hogy nem szeretné egy alkalmazás hangolása, és inkább be hardver több erőforrást. Ez a megközelítés lehet célszerű, ha nem szeretné módosítani egy fő alkalmazást egy foglalt időszakban. De, egy alkalmazás hangolása minimalizálhatja több erőforrást és alacsonyabb havi számlák a szolgáltatási szintek az Azure SQL Database használatakor.

### <a name="application-characteristics"></a>Alkalmazástulajdonságok
Bár az Azure SQL Database szolgáltatási szinteken javíthatja a teljesítményt stabilitását és kiszámíthatóságot nyújtanak az alkalmazás készültek, néhány ajánlott eljárást segíthet hangolása az alkalmazás jobban kihasználhatja az erőforrásokat a teljesítményi szinten. Bár számos alkalmazás jelentős teljesítménynövekedést egyszerűen váltásával egy magasabb teljesítményi szinthez vagy szolgáltatási rétegben, egyes alkalmazások szükség további hangolásra számára, hogy egy magasabb szintű szolgáltatást. A nagyobb teljesítmény érdekében fontolja meg a további alkalmazás hangolása az alkalmazásokat, amelyek a következő jellemzőkkel rendelkezik:

* **Az alkalmazásokat, lassú teljesítmény következtében "forgalmas"**. Forgalmas alkalmazások győződjön meg a túlzott adatelérési műveletek, amelyek a hálózati késés-és nagybetűket. Előfordulhat, hogy módosítania az ilyen típusú alkalmazásokat, az SQL Database adatelérési műveletek számának csökkentése érdekében. Alkalmazások teljesítményének javíthatja például ugyanazzal a módszerrel, például a kötegelés ad hoc lekérdezéseket, és a lekérdezések áthelyezése az tárolt eljárásokat. További információkért lásd: [lekérdezések Batch](#batch-queries).
* **Egy nagy számításigényű számítási feladatot, amely egy teljes egyetlen gép által nem támogatott az adatbázisok**. Adatbázisok, amelyek túllépik a legnagyobb prémium teljesítményszintje erőforrásait előnyös lehet a számítási feladatok skálázása. További információkért lásd: [adatbázisközi horizontális skálázási](#cross-database-sharding) és [funkcionális particionálást](#functional-partitioning).
* **Az alkalmazásokat, amelyek az optimálisnál rosszabb lekérdezéseket**. Alkalmazások, különösen az adatelérési réteg, rosszul van beállítva, lekérdezések, előfordulhat, hogy nem tudják igénybe egy magasabb teljesítményi szinthez. Ez magában foglalja a lekérdezéseket, amelyek nem tartozik a WHERE záradék, a hiányzó indexek vagy statisztika elavult. Ezek az alkalmazások kihasználhatják a standard szintű lekérdezési teljesítmény hangolása módszerek. További információkért lásd: [hiányzó index](#identifying-and-adding-missing-indexes) és [lekérdezések hangolása, és a felhasználóknak](#query-tuning-and-hinting).
* **Az alkalmazásokat, amelyek az optimálisnál rosszabb adatokat hozzáférési tervezési**. Az alkalmazásokat, adatokat rejlő hozzáférés egyidejűségi problémák, például deadlocking, előfordulhat, hogy nem tudják igénybe egy magasabb teljesítményi szinthez. Vegye fontolóra az Azure Caching service és a egy másik gyorsítótárazási technológián ügyféloldali gyorsítótárazási adatok által adatváltások ellen az Azure SQL Database csökkentését. Lásd: [alkalmazás szinten gyorsítótárazás](#application-tier-caching).

## <a name="tune-your-database"></a>Az adatbázis hangolása
Ebben a szakaszban áttekintjük egyes módszereket, amelyek segítségével hangolása az Azure SQL Database a legjobb teljesítmény elérése érdekében az alkalmazás és futtassa azt a legkisebb lehetséges teljesítményi szinten. Néhány, az alábbi eljárások egyezik, hagyományos SQL Server ajánlott hangolási, de mások csak az adott Azure SQL Database. Néhány esetben vizsgálja meg a felhasznált erőforrások területek további finomhangolása és kiterjesztése a hagyományos SQL Server technikákkal működjön az Azure SQL Database-ben található adatbázishoz.

### <a name="identify-performance-issues-using-azure-portal"></a>Az Azure portal használatával, a teljesítménybeli problémák azonosítása
Az Azure Portalon a következő eszközök segíthetnek elemzése és az az SQL database teljesítménnyel kapcsolatos problémák megoldásához:

* [Lekérdezési terheléselemző](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Az Azure Portalon rendelkezik mindkét ezeket az eszközöket és azok használatáról további információt. Hatékony diagnosztizálása, és kijavíthatja a problémák, azt javasoljuk, hogy először próbálja meg az eszközök az Azure Portalon. Azt javasoljuk, hogy használja-e a manuális hangolás megközelítéseket, bemutatjuk a következő, a hiányzó indexek és lekérdezések hangolása, bizonyos esetekben.

További információ az Azure SQL Database problémák azonosításában a [alkalmazásteljesítmény-figyelési](sql-database-single-database-monitor.md) cikk.

### <a name="identifying-and-adding-missing-indexes"></a>Azonosító és a hiányzó indexek hozzáadása
Az OLTP adatbázis-teljesítmény gyakran okoz problémát a fizikai adatbázis-tervezésben vonatkozik. Gyakran adatbázissémák tervezett, és nagy mennyiségű (akár a terhelés, akár az adatok mennyisége) tesztelési nélkül. Sajnos a lekérdezésterv teljesítményét előfordulhat, hogy kis léptékű elfogadható lehet, de jelentősen csökkentheti a termelési szintű adatmennyiség alapján. A leggyakoribb probléma forrása kielégíteni szűrők vagy egyéb korlátozások egy lekérdezést a megfelelő indexek hiánya. Hiányzó indexek jegyzékek táblázatként gyakran előfordul, ha egy index pozicionálási sikerült elegendő vizsgálata.

Ebben a példában a kiválasztott lekérdezésterv vizsgálat használja, amikor egy pozicionálási elegendő lenne:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Hiányzó indexekkel rendelkező lekérdezésterv](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Az Azure SQL Database segítségével, keresés és a javítás közös hiányzó index feltételeket. DMV-vel az Azure SQL Database-be épített tekintse meg a lekérdezés fordítások, amelyben az index lenne jelentősen csökkentheti a becsült költség, a lekérdezés futtatásához. Lekérdezés-végrehajtás során az SQL Database nyomon követi, milyen gyakran az egyes lekérdezési terv végrehajtásakor, és a becsült gap követi a lekérdezést végrehajtó terv és az imagined között, ahol újjáépítésére létezett. Ezeket a dinamikus felügyeleti nézetek segítségével gyorsan kitalálni milyen módosításokat a fizikai adatbázis-tervezésben való javíthatja az adatbázis és a valós számítási feladat a számítási feladatok összköltség.

Ez a lekérdezés segítségével lehetséges a hiányzó indexek kiértékelése:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Ebben a példában a lekérdezés eredményezett, ez a javaslat:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

A létrehozást követően, hogy ugyanazon SELECT utasítás választja ki egy másik sémát használ, a keresés nem egy vizsgálatot, és hatékonyabban végrehajtja a terv:

![Javított indexekkel rendelkező lekérdezésterv](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A legfontosabb elemzéseket, hogy a megosztott, hagyományos rendszer IO-kapacitás korlátozottabb, mint a dedikált kiszolgáló gép. Nincs prémium minimalizálja a szükségtelen I/O igénybe maximális a rendszer az a dtu-k, az egyes szinteken, az Azure SQL Database szolgáltatási csomagjai. Választási lehetőségek jelentősen növelheti a késést az egyes lekérdezések esetén megfelelő fizikai adatbázis-tervezésben javítani az egyidejű kérelmek skálázási egység kezeli, és minimalizálja a költségeket, a lekérdezés kielégítéséhez szükséges. A hiányzó index DMV-vel kapcsolatos további információkért lásd: [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Lekérdezések hangolása, és a felhasználóknak
A lekérdezésoptimalizáló Azure SQL Database-ben a hagyományos SQL Server-lekérdezésoptimalizáló hasonlít. A legtöbb ajánlott eljárást a Lekérdezések finomhangolása és az indoklást ismertetése a lekérdezésoptimalizáló modell korlátozásai is vonatkoznak az Azure SQL Database. Ha az Azure SQL Database lekérdezések hangolás kaphat további előnye, hogy az összesített erőforrás-igényű csökkentését. Lehet, hogy az alkalmazás futtatható, mint a untuned egyenértékű alacsonyabb költségek, hogy futtatható egy alacsonyabb teljesítményszintre, mert.

Ez gyakori az SQL Server, és amely akkor is érvényes az Azure SQL Database például hogyan a lekérdezésoptimalizáló "megszerzésén" paraméterek. A lekérdezésoptimalizáló fordításkor, kiértékeli a meghatározásához, hogy létrehozhat-e több optimális lekérdezésterv paraméter aktuális értékének. Bár ez a stratégia gyakran is vezet, amely jelentősen gyorsabb, mint egy csomag nélkül ismert paraméterértékeket összeállított lekérdezésterv, jelenleg működik imperfectly mind az SQL Server és az Azure SQL Database-ben. Néha az a paraméter nem felszippantásra, és néha a paraméter felszippantásra van, de a létrehozott terv optimálisnál a paraméterértékeket a számítási feladat teljes készletét. Microsoft lekérdezésmutatókat (irányelvek) tartalmaz, adja meg a leképezés több szándékosan, és felülírhatja az alapértelmezett viselkedést, a paraméter elemző. Gyakran mutatók használatakor orvosolásával eseteket, amelyben az alapértelmezett SQL Server vagy az Azure SQL Database viselkedés hiányos, az egy adott ügyfél számítási feladata.

A következő példa bemutatja, hogyan hozhat létre a lekérdezésfeldolgozó optimálisnál, mind a teljesítmény- és erőforrás-igényű csomagot. Ebben a példában is látható, hogy ha a lekérdezés-végrehajtási használja, csökkentheti a lekérdezés futtatása időre és erőforrás-követelmények az SQL Database:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

A telepítő kódot hoz létre egy táblát, amely rendelkezik torzítja az adatok terjesztési. Az optimális lekérdezési terv eltérő alapján mely paraméter van kiválasztva. Sajnos az gyorsítótárazási viselkedésének a csomag nem mindig újrafordítottuk a lekérdezés a leggyakrabban használt paraméter értéke alapján. Így lehetőség egy optimálisnál csomagot a gyorsítótárba, és sok érték, akkor is, ha egy másik csomagot lehet jobb választás terv átlagosan használt. Ezután a lekérdezésterv hoz létre két tárolt eljárások, amelyek azonos, azzal a különbséggel, hogy egy olyan speciális lekérdezés-végrehajtási rendelkezik.

**A példában 1. rész**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Például a 2. rész**

(Javasoljuk, hogy várjon legalább 10 percig, mielőtt megkezdené a példában a 2. rész, hogy az eredmények nem azonos az eredményül kapott telemetriai adatok.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Ebben a példában az egyes részek megpróbál futtatni egy paraméteres insert utasítás 1000 alkalommal (az egy megfelelő terhelést generálhat, használja, mint egy tesztelési adatkészlet). Tárolt eljárások végrehajtása, ha a lekérdezésfeldolgozó megvizsgálja a paraméter értéket, amelyet az eljárás során az első fordítás (paraméter "elemzése"). A processzor gyorsítótárazza az eredményül kapott csomagot, és felhasználja újabb meghívásához, még akkor is, ha a paraméter értéke nem egyezik. Az optimális tervet előfordulhat, hogy nem minden esetben használható. Néha szüksége is az optimalizáló jobb, ha az adott esetben helyett az átlagos eset, amikor először a lekérdezés fordítása csomagot választja ki. Ebben a példában a kezdeti terv, amely beolvassa az összes sort található minden egyes érték, amely megegyezik a paraméter "vizsgálat" csomag hoz létre:

![Lekérdezés hangolása a vizsgálati csomag használatával](./media/sql-database-performance-guidance/query_tuning_1.png)

Az 1 érték hajtottunk végre az eljárást, mert a az eredményül kapott terv optimális 1 érték volt, de nem a táblában szereplő összes egyéb értékek az optimálisnál rosszabb. Az eredmény valószínűleg nem mit érdemes Ha véletlenszerűen kiválasztható minden egyes előfizetés, mert a csomag lassabban hajt végre, és több erőforrást használ.

Ha a teszt- `SET STATISTICS IO` beállítása `ON`, a logikai vizsgálat ebben a példában a munkát a háttérben. Láthatja, hogy nincsenek-e a terv (ez nem elég hatékony, ha az átlagos eset, hogy csak egy sort adja vissza) által végzett 1,148 olvasások:

![Lekérdezés hangolása egy logikai vizsgálat használatával](./media/sql-database-performance-guidance/query_tuning_2.png)

Második része a példában a lekérdezés-végrehajtási állapítható meg, hogy egy adott érték használata a fordítás során az optimalizáló használ. Ebben az esetben arra kényszeríti a lekérdezésfeldolgozó figyelmen kívül hagyja az értéket, amelyet paraméterként, és ehelyett feltételezzük `UNKNOWN`. Ez az érték, amely az átlagos gyakoriság tartozik a táblában (a döntés figyelmen kívül hagyása) hivatkozik. Az eredményül kapott terv Ez gyorsabb és kevesebb erőforrást, átlagosan, mint a csomagot használ, az 1. rész ebben a példában a keresés-alapú csomag:

![A lekérdezés-végrehajtási lekérdezések hangolása](./media/sql-database-performance-guidance/query_tuning_3.png)

Láthatja, hogy a hatása a **sys.resource_stats** tábla (késik, hajtsa végre a vizsgálat, és ha az adatokat tölti fel a tábla kezdve). Ebben a példában, 1. rész a 22-es: 25-ös: 00 időszakban végrehajtott és 2. rész 22:35:00-kor végrehajtott. A korábbi időtartomány további erőforrások az adott időtartományban, mint az újabb már (miatt a terv hatékonysága fejlesztései) használja.

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Lekérdezés hangolása példa eredmények](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Bár ebben a példában a kötet nem szándékosan kisméretű, az optimálisnál rosszabb paraméterek hatását jelentős, különösen a nagyobb adatbázisok lehet. A különbség a szélsőséges esetben másodperc azokra az esetekre, gyors és lassú esetek óra közötti lehet.
> 
> 

Megvizsgálhatja **sys.resource_stats** -e az erőforrás, tesztelje erőforrást használ, több vagy kevesebb mint egy másik meghatározásához. Ha összehasonlítjuk az adatokat, külön tesztek ütemezése úgy, hogy azok nem azonos az 5 perces ablakban az a **sys.resource_stats** megtekintése. A gyakorlat célja használt erőforrások teljes mennyisége minimalizálása érdekében, illetve nem csökkentheti minimálisra csúcs erőforrásokat. Általában a optimalizálása késésének kódrészleteket is csökkenti erőforrás-használat. Győződjön meg arról, hogy szükség az alkalmazáshoz a módosításokat, és, hogy a módosítások nem negatívan befolyásolják a vásárlói élményt, a személy, aki lekérdezésmutatókat használhatja az alkalmazásban.

Ha egy számítási feladat ismétlődő lekérdezések készletével rendelkezik, gyakran logikus rögzítéséhez és érvényesíteni a terv lehetősége az optimalizálási, mert azt a minimális erőforrás mérete egység az adatbázis üzemeltetéséhez szükséges meghajtók. Azt ellenőrzése után időnként újra a segítségével győződjön meg arról, hogy azok rendelkeznek csökkent, a csomagok. További információ [mutatók (Transact-SQL) lekérdezés](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Adatbázisközi horizontális skálázás
Mivel az Azure SQL Database a hagyományos hardvereken fut, egy önálló adatbázis kapacitáskorlátjai alacsonyabb, mint a hagyományos helyszíni SQL Server telepítésekor. Egyes ügyfeleink a horizontális skálázás technikák használatával terjednek több adatbázis adatbázis-műveletek, a műveletek nem illő önálló adatbázis az Azure SQL Database keretein belül. A legtöbb ügyfeleink, akik a horizontális skálázás technikák az Azure SQL Database felosztása egy dimenziót az adataikat több adatbázis között. Az ezzel a módszerrel kell megérteni, hogy OLTP alkalmazások gyakran hajtják végre a tranzakciók, amelyek a alkalmazni egyetlen sort vagy sorokat a sémában egy kisebb csoportot.

> [!NOTE]
> Az SQL Database mostantól lehetőséget nyújt, amelyek segítik a horizontális skálázás könyvtárba. További információkért lásd: [Elastic Database-ügyfélkódtár áttekintése](sql-database-elastic-database-client-library.md).
> 
> 

Például ha egy adatbázis ügyfél neve, sorrend és rendelés részletei (például a hagyományos példa Northwind adatbázist részét képező SQL Server), akkor tehet fel ezeket az adatokat több adatbázis-okat a kapcsolódó rendelési és a rendelések részleteit ügyfél információk. Garantálhatja, hogy az ügyfél adatainak egyetlen adatbázisban marad. Az alkalmazás különböző ügyfelektől lenne elosztja a adatbázisok gyakorlatilag a terhelés elosztható a több adatbázis között. Horizontális skálázási ügyfelek nem csupán elkerülheti a maximális méretkorlátot, de Azure SQL Database is képes feldolgozni, amelyek jelentősen nagyobb, mint a különböző teljesítményszintek korlátozásait, mindaddig, amíg minden egyes adatbázis illik bele a dtu-k számítási feladatokhoz.

Bár az adatbázis horizontális skálázási megoldás összesített erőforrás-kapacitása nem csökkenthető, több adatbázis futó nagyon nagy méretű megoldások támogatása rendkívül hatékony. Minden adatbázis méretez más teljesítményszintre támogatása nagyon nagy méretű, magas erőforrás-igényű adatbázisok "érvényes" parancs.

### <a name="functional-partitioning"></a>Funkcionális particionálás
SQL Server-felhasználók gyakran kombinálásával egyetlen adatbázisban számos függvénye. Például ha egy alkalmazás logikai kezelheti a készletet egy tároló, a, hogy az adatbázis előfordulhat Készletkövetés vásárlási megrendelést, a tárolt eljárások és a hónap vége jelentéskészítés kezelése az indexelt vagy tényleges táblán alapuló nézetek társított logikát. Ez a technika megkönnyíti a különböző műveletek, mint például a biztonsági mentés felügyeletéhez, de azt is szükséges, hogy a hardvert, hogy a maximális terhelés kezeléséhez minden functions-alkalmazások közötti méretre.

Ha egy Azure SQL Database-ben kibővített architektúrát használ, érdemes felosztani a különböző funkciók különböző adatbázisok egy alkalmazás Ez a módszer használata esetén mindegyik alkalmazás egymástól függetlenül méretezhető. Egy alkalmazás elfoglaltabb válik (és az adatbázison a terhelés növekszik), a rendszergazdák kiválaszthatják független teljesítményszintek minden függvény az alkalmazásban. Címen a határt, ebben az architektúrában az alkalmazás lehet nagyobb, mint egy hagyományos egyetlen gép képes kezelni, mert a terhelés több gép között megoszlik.

### <a name="batch-queries"></a>Kötegelt lekérdezések
Alkalmazásokhoz, melyek segítségével a nagy mennyiségű adatok eléréséhez az ad hoc kérdez le, gyakori válaszidő jelentős mennyiségű van költött az alkalmazás és az Azure SQL Database réteget közötti hálózati kommunikáció. Akkor is, ha az alkalmazás és az Azure SQL Database találhatók ugyanabban az adatközpontban, a kettő közötti hálózati késés előfordulhat, hogy lehet rögzített adatok nagy számú adatelérési műveletek. A csökkentéséhez le ciklikus léptető az adatelérési műveletek esetében, érdemes lehet a beállítást vagy a az ad hoc lekérdezéseket kötegelt, illetve a tárolt eljárások fordítsa őket. Ha az ad hoc lekérdezéseket kötegelt, az Azure SQL Database több lekérdezés elküldheti az egyetlen adatváltási egy nagy méretű Batch. Összeállíthatja a tárolt ad hoc lekérdezéseket, mintha azokat, a batch érhet el tudta ugyanazt az eredményt. Tárolt eljárás használatával is biztosít az Azure SQL Database-ben a lekérdezésterveket gyorsítótárazási, így ismét használhatja a tárolt eljárás nagyobb eséllyel előnyeit.

Egyes alkalmazások célozzák. Néha csökkentheti egy adatbázis teljes i/o terhelését a mérlegeli írások kötegelendő hogyan. Ez gyakran előfordul, a bonyolultabb, mint az explicit tranzakciók a tárolt eljárások és ad-hoc kötegek automatikusan véglegesítési tranzakció helyett. Különböző módszereket használhat értékelése, lásd: [Azure SQL Database-alkalmazások technikák kötegelés](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Kísérletezzen a saját számítási feladatok, a jobb oldali modell található a kötegelés. Győződjön meg arról, a modell előfordulhat, hogy kissé eltérő tranzakciós konzisztenciagaranciákat rendelkezni. Keresés, így segít csökkenti az erőforrás-használat megfelelő számítási feladatokra van szükség konzisztencia és a teljesítmény feláldozását kombinációjával keresése.

### <a name="application-tier-caching"></a>Alkalmazásrétegek gyorsítótárazás
Egyes adatbázis-alkalmazások rendelkezik olvasási számítási feladatokhoz. Gyorsítótárazás rétegek előfordulhat, hogy csökkentse az adatbázis terhelését, és előfordulhat, hogy lehetséges csökkentése érdekében a teljesítményszintet, az Azure SQL Database-adatbázis támogatásához szükséges. A [Azure Redis Cache](https://azure.microsoft.com/services/cache/), ha egy olvasási munkaterhelés, többször is beolvashatja az adatokat (vagy egyszer gépenként alkalmazásrétegek – konfigurációjától függően), és biztonságosan tárolja a kívül az SQL-adatbázis az adatokat. Ezzel a módszerrel (Processzor- és olvasási i/o-) adatbázisok terhelésének csökkentése érdekében, de nincs tranzakció-konzisztencia hatással lesz, mivel előfordulhat, hogy az adatok olvasása a gyorsítótárból nincs szinkronban az adatokat az adatbázisban. Számos alkalmazásban a bizonyos fokú inkonzisztenciát nem elfogadható, ez nem igaz, bármely számítási feladat számára. Egy alkalmazás szintű gyorsítótárazási stratégia megvalósítása előtt, teljes mértékben ismernie kell minden olyan alkalmazás követelményeinek.

## <a name="next-steps"></a>További lépések
* További információ a DTU-alapú szolgáltatásszintek: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
* További információ a Virtuálismag-alapú szolgáltatásszintek: [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
* Rugalmas készletek kapcsolatos további információkért lásd: [Mi az az Azure rugalmas készlet?](sql-database-elastic-pool.md)
* Teljesítmény és a rugalmas készletek kapcsolatos információkért lásd: [mikor érdemes rugalmas készlet](sql-database-elastic-pool-guidance.md)

