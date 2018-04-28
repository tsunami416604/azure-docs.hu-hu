---
title: Az Azure SQL-adatbázis teljesítményének hangolása útmutató |} Microsoft Docs
description: További tudnivalók az Azure SQL Database lekérdezések teljesítményének növelése a javaslatok segítségével.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: c84104ac9094980d0e6d16b535dcf13c462a645a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tuning-performance-in-azure-sql-database"></a>Az Azure SQL-adatbázis teljesítményének hangolása

Az Azure SQL Database is tartalmaz [javaslatok](sql-database-advisor.md) , hogy az adatbázis teljesítményének javításával használhatja, vagy hagyhatja, hogy az Azure SQL Database [automatikusan alkalmazkodnak hozzá az alkalmazás](sql-database-automatic-tuning.md) és a módosítások életbe léptetéséhez, javítja a teljesítményt a munkaterhelés.

Az Ön nem rendelkezik megfelelő javaslatokkal és teljesítménnyel kapcsolatos problémák továbbra is fennáll, teljesítményének javítása érdekében a következő módszereket használhatja:
- Növelje a a szolgáltatási rétegekkel a [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) vagy a [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md) további erőforrások az adatbázisba.
- Az alkalmazás hangolás, és alkalmazni, néhány ajánlott eljárás, amely növelheti a teljesítményt. 
- Az adatbázis hangolására indexek és lekérdezéseket, amelyekkel hatékonyabban dolgozhasson adatokkal történő módosításával.

Ezek a kézi módszert, mert később szüksége lesz, hogy mit [DTU-alapú modell erőforrás korlátok](sql-database-dtu-resource-limits.md) és [vCore-alapú modell erőforrás korlátok (előzetes verzió)](sql-database-vcore-resource-limits.md) az igényeinek. Ellenkező esetben kellene írja újra az alkalmazást vagy az adatbázis-kódot, és telepítheti a módosításokat.

## <a name="increasing-performance-tier-of-your-database"></a>Az adatbázis teljesítményének szintjének növelése

Az Azure SQL adatbázis két vásárlási modell kínál a [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és egy [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md) , amelyek közül választhat. Egyes szolgáltatásszinteken szigorúan elkülöníti az erőforrásokat, hogy használható-e az SQL-adatbázis, és biztosítja, hogy a szolgáltatási szint kiszámítható teljesítményt. Ebben a cikkben szereplő útmutatást, amelyek segítségével válassza ki a szolgáltatási rétegben, az alkalmazás fel. Azt is ismertetik, hogy az alkalmazás úgy hasznosíthatja a legjobban az Azure SQL Database észlelheti.

> [!NOTE]
> Ez a cikk foglalkozik, a teljesítmény útmutatást az önálló adatbázisok Azure SQL-adatbázisban. Rugalmas készletek kapcsolódó teljesítmény útmutatóért lásd: [rugalmas készletek ára és teljesítménye szempontjai](sql-database-elastic-pool-guidance.md). Vegye figyelembe azonban, hogy ebben a cikkben szereplő hangolási javaslatok alkalmazása az adatbázisok rugalmas készlethez, és beállíthatja hasonló teljesítmény előnyök.
> 

* **Alapszintű**: az alapszintű service réteg ajánlatokat a megfelelő teljesítmény kiszámíthatóságot az egyes adatbázisok, akár óráig óra. Egy alapszintű adatbázis elegendő erőforrással támogatják a több egyidejű kérelmet nem rendelkező kisméretű adatbázisban a megfelelő teljesítmény. Ha alapszintű szolgáltatásréteg szeretné használni a tipikus használati esetek a következők:
  * **Ön éppen használatának megkezdéséhez az Azure SQL Database**. Alkalmazások, amelyek a fejlesztési gyakran nagy teljesítményű szintek nem szükséges. Alapszintű adatbázisok adatbázis fejlesztési vagy tesztelési, egy alacsony ár ponton ideális környezetben futnak.
  * **Egyetlen felhasználó van egy adatbázis**. Alkalmazásokat, amelyek általában egy-egy felhasználóhoz társítása egy adatbázis nem rendelkezik magas egyidejűségi és teljesítménybeli követelményeit. Ezek az alkalmazások esetén a alapvető szolgáltatási réteg esetén használható.
* **Standard**: A Standard service-csomagra kínál javítja a teljesítményt, kiszámíthatóságot és adatbázisok esetén, amelyek több egyidejű kérés, például a munkacsoport és webalkalmazások kiváló teljesítményt nyújt. Ha úgy dönt, a Standard service réteg adatbázis, az adatbázis-alkalmazás, kiszámítható teljesítményt alapján méretét percben percben.
  * **Az adatbázis még több egyidejű kérelmet**. Alkalmazások, amelyeket a szolgáltatás több felhasználó általában egyszerre kell magasabb teljesítményszintet. Például munkacsoportokhoz vagy webalkalmazásokhoz az alkalmazásokat, amelyek alacsony közepes IO forgalom követelményeinek támogatása több egyidejű lekérdezések esetén a szabványos szolgáltatási réteg esetén használható jól az.
* **Prémium szintű**: A prémium szolgáltatásszintet kiszámítható teljesítményt nyújt, adatbázis-második második, egyes Premium és fontos üzleti (előzetes verzió) keresztül. Ha úgy dönt, a prémium szolgáltatásszintet, az adatbázis-alkalmazás az adatbázishoz tartozó a csúcsterhelés alapján méretét. A terv eltávolítja az esetek, amelyek variancia járhat kis lekérdezések késésérzékeny műveletek a vártnál tovább tart. Ez a modell jelentősen egyszerűsítheti a fejlesztési és a termék érvényesítési ciklusok maximális erőforrásigényeivel, a teljesítmény variancia vagy a lekérdezés-késleltetés erős utasítások végrehajtásához igénylő alkalmazásokhoz. Prémium szintű szolgáltatási réteg használata legtöbbször egy vagy több, a következő jellemzőkkel rendelkezik:
  * **Magas csúcsterhelés**. Olyan alkalmazás, amely jelentős Processzor, memória, vagy bemeneti/kimeneti (I/O) a művelet elvégzéséhez szükséges egy dedikált, nagy teljesítményű szintet igényel. Például egy adatbázis-művelet ismert felhasználásához, több processzormag hosszú ideig a prémium szolgáltatásszintet jelöltségét ellenőrző.
  * **Sok egyidejű kérés**. Egyes adatbázis-alkalmazások például szolgáltatás sok egyidejű kérés, amikor a szolgáltató egy webhely, amely rendelkezik egy nagy forgalma. Basic és Standard szolgáltatásszintek korlátozza az adatbázisonként egyidejű kérelmek számát. Több kapcsolatot igénylő alkalmazások kellene válasszon egy megfelelő foglalási méret kezeléséhez szükséges kérelmek maximális számát.
  * **Kis késés**. Egyes alkalmazások szükséges minimális időben választ az adatbázisból biztosításához. Egy adott tárolt eljárás hívása esetén, egy szélesebb körű felhasználói művelet részeként, lehetséges, hogy egy legfeljebb 20 ezredmásodpercben, az idő 99 %-át egy adott hívás visszatérési követelménnyel. Ez az alkalmazástípus a prémium szolgáltatásszintet, győződjön meg arról, hogy rendelkezésre áll-e a szükséges számítási teljesítményt előnyeivel.

A szolgáltatási szint, amelyekre szüksége van az SQL-adatbázis a maximális Adatbetöltési követelményeinek minden erőforrás dimenzió függ. Egyes alkalmazások egyetlen trivial méretű használata, de más erőforrások jelentős követelmények vonatkoznak.

### <a name="service-tier-capabilities-and-limits"></a>Szolgáltatás szolgáltatásszintek lehetőségei és korlátai

Egyes szolgáltatásszinteken, beállíthatja a teljesítményszintet, rugalmasan fizetni csak a szükséges kapacitást. Is [a kapacitás](sql-database-service-tiers-dtu.md), felfelé vagy lefelé, mint a munkaterhelési változások. Például ha a biztonsági-iskolai bevásárlási időszak alatt az adatbázisban munkaterhelés magas, növelheti az adatbázis teljesítményszintjét beállított ideje, július szeptember keresztül. A maximális időszak végén csökkentheti. Minimalizálhatja a vállalat számára a szezonalitás értékének a felhőkörnyezet optimalizálásával fizet. Ez a modell szoftver termék kiadási ciklusok esetén is működik. A teszt csoport kapacitás foglal le, amíg a teszt futtatása, és a kapacitás majd engedje a tesztelés befejezése. A kapacitás kérelem modellben kell fizetnie kapacitás szükség lenne rá, és előfordulhat, hogy ritkán használt dedikált erőforrások kell.

### <a name="why-service-tiers"></a>Miért szolgáltatásszintek?
Bár egyes adatbázis munkaterhelések eltérőek lehetnek, a szolgáltatási rétegekkel célja teljesítmény kiszámíthatóságot különböző teljesítményt nyújtanak. Nagy méretű adatbázist erőforrás-követelményekkel rendelkező ügyfelek a több dedikált számítógépes környezetekben is működik.

## <a name="tune-your-application"></a>Az alkalmazás hangolása
A hagyományos helyszínen SQL Server a folyamat kezdeti kapacitástervezés gyakran elkülönül az alkalmazás futtatása a éles környezetben. Először hardver- és termék licencet vásárolt, és ezt követően teljesítményhangolás történik. Az Azure SQL Database használata esetén érdemes interweave egy alkalmazást futtat, és azt hangolása Az igény szerinti kapacitást fizető modelljében észlelheti a az alkalmazás helyett a jövőbeli növekedésre tervek az alkalmazás, amelyek gyakran helytelen Találgatások alapján hardveren elhelyezésétől, szükséges minimális erőforrások használatára. Egyes ügyfelek előfordulhat, hogy úgy, hogy nem finomhangolhatják a kérelmet, és ehelyett válassza overprovision hardver-erőforrások. Ez a megközelítés lehet hasznos, ha nem kívánja módosítani a kulcsfontosságú alkalmazások foglalt időszakon belül. De, egy alkalmazás hangolása érdekében minimalizálhatja erőforrás-követelmények és alacsonyabb havi számla a szolgáltatási szinteket az Azure SQL Database használatakor.

### <a name="application-characteristics"></a>Alkalmazás jellemzői
Bár az Azure SQL Database szolgáltatási szinteket készültek, a jobb teljesítmény stabilitás és kiszámíthatóságot az alkalmazáshoz, néhány ajánlott eljárás segítségével finomhangolhatják a az alkalmazás teljesítményének szinten jobban az erőforrások kihasználásához. Jóllehet számos alkalmazás teljesítménynövekedéshez egyszerűen váltásával magasabb teljesítményszintre vagy szolgáltatási rétegben, egyes alkalmazásokat kell hangolására is kihasználhatják a magasabb szintű szolgáltatást. A nagyobb teljesítmény elérése érdekében fontolja meg a további alkalmazás hangolása alkalmazásokat, amelyek a következő jellemzőkkel rendelkezik:

* **Az alkalmazásokat, amelyek teljesítménycsökkenést "chatty" viselkedés miatt**. Chatty alkalmazások ellenőrizze túlzott adatelérési műveletek, amelyek a hálózati késés-és nagybetűket. Előfordulhat, hogy módosítania az ilyen típusú alkalmazások az SQL Database adatelérési műveletek számának csökkentése érdekében. Például az alkalmazások teljesítményének javíthatja technikák alkalmi lekérdezések kötegelés vagy a lekérdezések áthelyezése tárolt eljárások segítségével. További információkért lásd: [Batch-lekérdezések](#batch-queries).
* **Egy számításigényes munkaterhelést, amely által egy teljes egyetlen számítógépen nem támogatott az adatbázisok**. Adatbázisok, amelyek mérete meghaladja a prémium szintű legmagasabb teljesítményszintet erőforrásainak előnye származhat a munkaterhelés kiterjesztése. További információkért lásd: [adatbázisok közötti horizontális](#cross-database-sharding) és [működési particionálás](#functional-partitioning).
* **Az alkalmazásokat, amelyek optimálisnál lekérdezések**. Alkalmazások, különösen az adatelérési réteg, rosszul hangolt lekérdezések, előfordulhat, hogy nem tudják igénybe venni egy magasabb teljesítményszintre. Ez magában foglalja a lekérdezések, amely nem rendelkezik a WHERE záradék, a hiányzó indexek vagy statisztika elavult. Ezek az alkalmazások ki a normál lekérdezési teljesítményének hangolása módszerek előnyeit. További információkért lásd: [hiányzó indexek](#identifying-and-adding-missing-indexes) és [hangolása és leképezésére lekérdezése](#query-tuning-and-hinting).
* **Az alkalmazásokat, amelyek optimálisnál adatok hozzáférési tervezési**. Az alkalmazásokat, amelyek rejlő adatok hozzáférési párhuzamossági problémák, például deadlocking, előfordulhat, hogy nem tudják igénybe venni egy magasabb teljesítményszintre. Vegye figyelembe, hogy csökkenti a kiszolgálókkal való adatváltások számát, szemben az Azure SQL Database által a gyorsítótár Azure szolgáltatás vagy egy másik gyorsítótárazási technológiával ügyféloldali gyorsítótárazási adatokat. Lásd: [alkalmazás réteg gyorsítótárazás](#application-tier-caching).

## <a name="tune-your-database"></a>Az adatbázis hangolása
Ebben a szakaszban úgy tekintünk bizonyos Azure SQL-adatbázis is kapnak a legjobb teljesítmény érdekében az alkalmazáshoz, és futtassa a legalacsonyabb lehetséges teljesítmény hangolására használható technikák is. Néhány, az alábbi eljárások egyezik, ajánlott eljárások hangolása hagyományos SQL Server, de az Azure SQL Database adott. Bizonyos esetekben a felhasznált erőforrások található területek további hangolása és kiterjesztése a hagyományos SQL Server technikák működéséhez az Azure SQL Database-adatbázis ellenőrizheti.

### <a name="identify-performance-issues-using-azure-portal"></a>Azure-portál használatával teljesítményproblémák azonosítása
Az Azure-portálon a következő eszközök segítségével elemezheti és hárítsa el az SQL database teljesítménnyel kapcsolatos problémák:

* [Lekérdezési terheléselemző](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Az Azure portálon találhat további információ a mindkét ezek az eszközök és a használatukat. Hatékony felderítésére, és a problémák megoldásához, javasoljuk, először próbálkozzon az eszközök, az Azure portálon. Azt javasoljuk, hogy használja-e a manuális, amely arról lesz szó ezt követően a hiányzó indexek és bizonyos esetekben a lekérdezés hangolási módszerek hangolása.

További információ az Azure SQL Database problémákat azonosítása a [teljesítményfigyelés](sql-database-single-database-monitor.md) cikk.

### <a name="identifying-and-adding-missing-indexes"></a>Azonosítja és a hiányzó indexek hozzáadása
Az OLTP adatbázis teljesítményének gyakori probléma a fizikai adatbázis tervezési vonatkozik. Gyakran adatbázis-sémák tervezhetők meg és tesztelése (vagy a terhelés vagy adatmennyiség) léptékű nélkül. Sajnos lekérdezéstervet teljesítményének előfordulhat, hogy egy kis méretű elfogadható, de jelentősen csökkentheti a termelési szint az adatkötetek alatt. A probléma forrása leggyakoribb szűrők vagy egyéb korlátozások lekérdezésben kielégítéséhez megfelelő indexek hiánya. Gyakran hiányzó indexek jegyzékfájlokat táblázatként vizsgálata, ha egy index seek volt elegendő.

Ebben a példában a kiválasztott lekérdezésterv a vizsgálat használja, ha a seek elegendő lenne:

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

![A hiányzó indexek lekérdezéstervet](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Az Azure SQL Database segítségével, keresése és a javítás közös hiányzó index feltételek. Tekintse meg az Azure SQL Database beépített dinamikus felügyeleti nézetek lekérdezés fordítások, amelyben az index volna jelentősen csökkentheti a becsült költség-lekérdezés futtatható. Lekérdezés-végrehajtás során SQL-adatbázis milyen gyakran minden lekérdezésterv végrehajtásakor, és nyomon követi a becsült résnek között a végrehajtás alatt álló lekérdezésterv, és a imagined ahol adott index már létezett követi nyomon. A dinamikus felügyeleti nézetek segítségével gyorsan kitalálható mely módosításait a fizikai adatbázis tervezési javíthatja az adatbázis és a valós alkalmazások és szolgáltatások teljes munkaterhelés költségének.

Ez a lekérdezés segítségével értékelje ki a lehetséges hiányzó indexek:

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

Létrehozását követően, hogy ugyanazon SELECT utasítás szerzi másik csomagot, amely használ, a keresés nem egy vizsgálatot, és végrehajtja a terv hatékonyabban:

![Javított indexű lekérdezéstervet](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A kulcs betekintést az, hogy egy megosztott, hagyományos rendszer IO-kapacitás korlátozottabb, mint az egy dedikált kiszolgálói számítógép. Nincs a prémium szintű kihasználását maximális a rendszer a dtu-k, az egyes teljesítményszintjének az Azure SQL Database szolgáltatási szinteket a szükségtelen IO minimalizálja. Megfelelő fizikai adatbázis tervezési döntések jelentősen növelheti a várakozási egyes lekérdezések skálázási egység kezeli a párhuzamos kapacitásának javításához, illetve a lekérdezés kielégítéséhez szükséges költségek minimalizálása érdekében. A hiányzó index dinamikus felügyeleti nézetek kapcsolatos további információkért lásd: [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Lekérdezés hangolása és leképezésére
A lekérdezésoptimalizáló az Azure SQL Database a hagyományos SQL Server lekérdezésoptimalizáló hasonlít. A legtöbb, a lekérdezések beállítás, és az indoklást ismertetése ajánlott eljárások a lekérdezésoptimalizáló modell korlátozásai is vonatkoznak az Azure SQL Database. Ha az Azure SQL Database lekérdezések hangolás kaphat további előnye, hogy csökkenti a összesített erőforrás iránti igények kielégítése érdekében. Az alkalmazás lehet, mint egy untuned azzal egyenértékű alacsonyabb költségekkel futtatható, mert azt egy alacsonyabb teljesítményszintre futtatható.

Például, amely az SQL Server általános, és amely akkor is érvényes az Azure SQL Database hogyan a lekérdezésoptimalizáló "bitaláírásait" paraméterek. A lekérdezésoptimalizáló fordításkor, kiértékeli az határozza meg, hogy hozhat létre a több optimális lekérdezéstervet paramétere a jelenlegi érték. Bár ezt a stratégiát gyakran vezethet, amely jelentősen gyorsabb, mint egy ismert paraméterértékek nélkül összeállított tervet készíteni, jelenleg működik imperfectly mind az SQL Server és az Azure SQL-adatbázis. Néha a paraméter nem felszippantásra, és egyes esetekben a paraméter felszippantásra van azonban az előállított terv paraméterértékeket a munkaterhelés a teljes készletét az optimálisnál gyengébb. Microsoft tartalmaz lekérdezésmutatók (irányelvek) több szándékosan megadhatja a célt és felülbírálhatja az alapértelmezett viselkedést, a paraméter elemzés. Gyakran például használatakor megoldhatja esetekben, ahol az alapértelmezett SQL Server vagy az Azure SQL Database viselkedés hiányos egy adott ügyfélhez munkaterhelés számára.

A következő példa bemutatja, hogyan hozhat létre a lekérdezésfeldolgozó egy tervet, amely optimálisnál, mind a teljesítmény- és erőforrás-követelményei. Ez a példa is mutatja, hogy a lekérdezés emlékeztető használatakor csökkentése érdekében lekérdezés futtatása idő és erőforrás-követelményei az SQL-adatbázis:

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

A telepítő kód táblázatot hoz létre, amely rendelkezik válik egyenetlenné adatok terjesztési. Az optimális lekérdezési terv eltér mely paraméter van kiválasztva. Sajnos a terv gyorsítótárazásának nem mindig fordítsa újra a lekérdezést a leggyakrabban használt paraméter értéke alapján. Így lehetőség egy optimálisnál terv gyorsítótárazott és sok értéket, akkor is, ha egy másik csomagot lehet, hogy jobb terv választás átlagosan használt. Ezután a lekérdezésterv hoz létre két tárolt eljárások, amelyek azonos, azzal a különbséggel, hogy egy speciális lekérdezés javaslat.

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

**Példa, 2. rész**

(Javasoljuk, akkor várjon legalább 10 percig példában 2. lépés megkezdése előtt, hogy az eredmények értékei egyediek-e az eredményül kapott telemetriai adatokat.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Ez a példa részét képező összes megpróbál futtatni egy paraméteres insert utasítás 1000 alkalommal (adatok TesztKészlet használandó elegendő terhelés létrehozásához). Végrehajtja a tárolt eljárások, a lekérdezésfeldolgozó megvizsgálja a az eljárás során az első fordítási (paraméter "elemzés") átadott paraméter értéke. A processzor gyorsítótárazza az eredményül kapott terv, és használja a későbbi indítások, még akkor is, ha a paraméter értéke eltérő. Az optimális terv minden esetben előfordulhat, hogy nem használható. Néha kell kiválasztásához, amely jobb, ha az adott esetben helyett az átlagos esetben ha a lekérdezés első lefordított a terv optimalizáló ismerteti. Ebben a példában a kezdeti terv, hogy az olvasások az összes sor minden érték, amely megfelel a paraméter "ellenőrzés" terv állít elő:

![A vizsgálati csomag használatával hangolása lekérdezése](./media/sql-database-performance-guidance/query_tuning_1.png)

Azt az eljárás 1 érték használatával hajtotta végre, mert az eredményül kapott terv el legoptimálisabban az 1 érték volt, de optimálisnál értéket a táblázatban minden más helyen lett. Az eredmény valószínűleg nem mi alapvetően szükség, ha minden terv véletlenszerűen, válasszon, mert a csomag több lelassul, és több erőforrást használ.

A teszt futtatásakor `SET STATISTICS IO` beállítása `ON`, a logikai vizsgálat ebben a példában a munkát a háttérben. Láthatja, hogy vannak-e (ez nem hatékony, ha az átlagos eset az, hogy csak egy sort ad vissza) a terv által végzett 1,148 olvasások:

![A logikai vizsgálat használatával hangolása lekérdezése](./media/sql-database-performance-guidance/query_tuning_2.png)

A példában a második rész lekérdezés mutató állapítható meg, hogy egy adott érték a fordítás során használandó optimalizáló használja. Ebben az esetben arra kényszeríti a lekérdezésfeldolgozó figyelmen kívül hagyása átadott paraméterként, az értéket, és ehelyett azt feltételezni `UNKNOWN`. Ez az érték, amely az átlagos gyakoriság van a táblában (figyelmen kívül hagyva döntés) vonatkozik. Az eredményül kapott terv egy keresési-alapú tervet készíteni, amely gyorsabb és kevesebb erőforrást használ, átlagosan, mint a tervet az ebben a példában 1. rész:

![Lekérdezés lekérdezés mutató használatával hangolása](./media/sql-database-performance-guidance/query_tuning_3.png)

Megtekintheti az hatást a **sys.resource_stats** tábla (késik az, hogy a teszt- és amikor az adatok feltöltése a tábla végrehajtási idő). Ez például 1. rész hajtotta végre a 22:25:00 időszak során, és 2. rész 22:35:00 végre. A korábbi időszak további erőforrások az adott fut, a későbbi (miatt terv hatékonyságát fejlesztései) használt.

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Lekérdezés eredménye. példa hangolási.](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Bár ebben a példában a kötet nem szándékosan kicsi, az optimálisnál gyengébb paraméterek hatásának lehet jelentős, különösen a nagyobb méretű adatbázisokhoz. A különbség a szélsőséges esetben gyors esetekben másodpercig és lassú adódó óra közötti lehet.
> 
> 

Ellenőrizheti **sys.resource_stats** annak meghatározásához, hogy az erőforrás vizsgálat erőforrást használ, több vagy kevesebb mint másik teszttől. Adatok összehasonlításakor külön vizsgálatok ütemezése, hogy azok nem azonos 5 perces ablakában a **sys.resource_stats** nézet. A gyakorlatban célja minimalizálása érdekében a használt teljes mérete, és nem csúcsidőre erőforrások minimalizálása érdekében. Általában olyan kódrészletek, késésének optimalizálása is csökkenti hálózatierőforrás-fogyasztás. Győződjön meg arról, hogy szükség-e az alkalmazás a módosításokat, és, hogy a módosítások nem negatívan befolyásoló kódokat a felhasználói élmény, akiknek a lekérdezésmutatók használhatja az alkalmazás.

Ha egy munkaterhelés lekérdezések ismétlődő készlete, gyakran érdemes rögzítése és a terv a választott optimalizálási érvényesíteni, mert azt a minimális erőforrás mérete egység az adatbázis tárolásához szükséges meghajtók. Után érvényesíti, időnként újra a a tervek segítségével győződjön meg arról, hogy azok rendelkeznek csökkent. További tudnivalók [mutatók (Transact-SQL) lekérdezés](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Adatbázisok közötti horizontális
Mivel az Azure SQL Database fut, a hagyományos hardvereken, a kapacitáskorlátait egyetlen adatbázis alacsonyabb, mint a hagyományos helyszíni SQL Server telepítése a rendszer. Egyes ügyfelek horizontális technikák segítségével terjednek több adatbázis adatbázis-műveletek, a műveletek nem férnek el az Azure SQL Database egy önálló adatbázis keretein belül. A legtöbb felhasználói horizontális technikák az Azure SQL Database-e az adatok egy dimenziót osztani több adatbázis. Ez a módszer az, hogy OLTP-alkalmazások gyakran hajtják végre a tranzakciókat, amelyek csak egy sorra, vagy egy kis csoport azoknak a soroknak a séma alkalmazása tisztában kell.

> [!NOTE]
> SQL-adatbázis most már biztosít egy könyvtár, elősegítve ezzel a horizontális. További információkért lásd: [Elastic Database ügyféloldali kódtárának ismertetése](sql-database-elastic-database-client-library.md).
> 
> 

Például ha egy adatbázis ügyfél neve, a sorrendje, illetve a rendelés részleteit (például a hagyományos példa Northwind adatbázist programcsomagban található SQL Server), akkor sikerült ossza fel ezeket az adatokat több adatbázis csoportosításával rendelkező kapcsolódó sorrendjét és rendelés részletei információ. Garantálható, hogy a felhasználói adatok egyetlen adatbázisban marad. Az alkalmazás különböző ügyfelektől kellene osztani adatbázisok gyakorlatilag terjesszen a terhelés több adatbázis közötti. Horizontális az ügyfelek nem csak elkerülése érdekében az adatbázis maximális méretkorlátot, de az Azure SQL Database is tud feldolgozni, amíg minden egyes adatbázis hogyan illik bele a DTU jelentősen nagyobb, mint a különböző teljesítményszintek határain munkaterhelések.

Bár az adatbázis horizontális nem csökkenthető a megoldás összesített erőforrás-kapacitást, hatékonyan van elosztva több adatbázis nagyon nagy megoldások támogatása. Az egyes adatbázisok is szinten futnak, más-más teljesítménybeli támogatása nagyon nagy méretű, magas erőforrás-követelmények "hatékony" adatbázisokkal.

### <a name="functional-partitioning"></a>Funkcionális particionálás
SQL Server-felhasználók gyakran egyesítése egyetlen adatbázisban számos funkciót. Ha egy alkalmazás logika lehet kezelni a tárolóhoz, hogy az adatbázis Előfordulhat például készlet megrendelések, tárolt eljárások és indexelt vagy materializált nézetek, amely a hónap végi jelentéskészítéshez követési társított logikát. Ezzel a technikával megkönnyíti a műveletek, például a biztonsági másolat adatbázisát felügyeletére, de azt is meg kell kezelni a csúcsterhelés között az alkalmazás összes funkciójának hardver méretezés.

Az Azure SQL Database kibővített architektúrák használatára, célszerű a leíró egy másik adatbázisba alkalmazás különböző funkcióit. Ez a módszer segítségével minden alkalmazás egymástól függetlenül méretezi. Alkalmazás válik busier (és az adatbázis terhelését növeli), a rendszergazda megadhatja az egyes függvények független teljesítményszintek az alkalmazásban. A határt, ezzel az architektúrával alkalmazás lehet nagyobb, mint egy hagyományos egyetlen gép kezelni tud, mert a terhelés több számítógép van elosztva.

### <a name="batch-queries"></a>Kötegelt lekérdezések
A nagy mennyiségű adatok elérő alkalmazások esetében gyakori, az ad hoc kérdez le, válaszidő jelentős mennyiségű töltött az alkalmazás szint és az Azure SQL adatbázis-rétegből közötti hálózati kommunikáció. Akkor is, ha az alkalmazás és az Azure SQL Database ugyanabban az adatközpontban lévő, a kettő között a hálózati késés előfordulhat, hogy lehet nagyítható adatok nagy számú hozzáférési műveleteket. A hálózati csökkentése érdekében a kerekítés utazás közben a hozzáférési műveletek, érdemes lehet a beállítás az ad hoc lekérdezéseket vagy kötegelt vagy fordítási azokat a tárolt eljárások. Ha kötegelt az ad hoc lekérdezéseket, elküldheti több lekérdezés egyetlen út egy nagy köteg az Azure SQL Database. Ad hoc lekérdezéseket egy tárolt eljárás lefordításához, mintha azokat kötegelt érhetők el tudta ugyanazt az eredményt. A tárolt eljárás használatával is lehetővé teszi az az előnye, hogy a gyorsítótár az Azure SQL Database terveket, hogy újra használhassa a tárolt eljárás esélyét növelését.

Egyes alkalmazások írási igényű. Néha egy adatbázis teljes IO terhelését csökkentik hogyan történő írások figyelembe véve. Ez általában más dolga, mint az explicit tranzakciók helyett a tárolt eljárások és alkalmi kötegek automatikus véglegesítésű tranzakciók. Különböző módszereket használhat értékelése, lásd: [Azure SQL adatbázis-alkalmazások technikák kötegelés](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). A saját munkaterhelés találhatók a megfelelő modell kötegelés kísérletezhet. Győződjön meg arról, megérteni, hogy a modell előfordulhat némileg eltérő tranzakciós konzisztencia biztosítja. Keresés, a jobb oldali munkaterhelés erőforrás használata a lehető legkevesebb szükséges konzisztencia és a teljesítmény kompromisszumot alakítson ki a megfelelő kombinációja keresése.

### <a name="application-tier-caching"></a>Alkalmazás szintű gyorsítótárazása
Egyes adatbázis-alkalmazások olvasási műveleteket munkaterhelésekkel rendelkeznek. Rétegek gyorsítótárazás csökkentheti az adatbázis terhelését, és előfordulhat, hogy lehetséges csökkentése az Azure SQL Database segítségével adatbázis támogatásához szükséges teljesítményszint szükséges. A [Azure Redis Cache](https://azure.microsoft.com/services/cache/), ha egy olvasási műveleteket végez, érheti el az adatokat többször (vagy lehet, hogy minden alkalmazás szintű machine konfigurációjától függően), majd helyezze el az adatok az SQL-adatbázis kívül. Ez az adatbázis-terhelésnek (Processzor és olvasási I/O) csökkentheti, de nincs hatással lévő tranzakciós konzisztencia, mivel előfordulhat, hogy az adatok olvasása a gyorsítótárból szinkronban az adatbázis adatai. Bár számos alkalmazás bizonyos fokú inkonzisztenciát elfogadható, hogy igaz nem munkaterhelések. Egy alkalmazás szintű gyorsítótárazási stratégia megvalósítása előtt, teljes mértékben ismernie kell bármely alkalmazás követelményeinek.

## <a name="next-steps"></a>További lépések
* DTU-alapú szolgáltatásrétegeiben használt funkciókkal kapcsolatos további információkért lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és [DTU-alapú modell erőforrás-korlátozások](sql-database-dtu-resource-limits.md)
* VCore-alapú szolgáltatásrétegeiben használt funkciókkal kapcsolatos további információkért lásd: [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md) és [vCore alapuló erőforrás-korlátozások (előzetes verzió)](sql-database-vcore-resource-limits.md)
* További információ a rugalmas készletek: [Mi az Azure rugalmas készletek?](sql-database-elastic-pool.md)
* Teljesítmény és a rugalmas készletek kapcsolatos információkért lásd: [mikor érdemes figyelembe venni a rugalmas készlethez](sql-database-elastic-pool-guidance.md)

