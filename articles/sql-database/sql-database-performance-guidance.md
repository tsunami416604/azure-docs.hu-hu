---
title: Teljesítmény-finomhangolási útmutató
description: Útmutató a Azure SQL Database lekérdezési teljesítmény manuális finomhangolásához.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 0dc3a121b30f33d533b1079d9c81501130487017
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009107"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>A lekérdezés teljesítményének manuális finomhangolása Azure SQL Database

Ha olyan teljesítménnyel kapcsolatos problémát észlelt, amely SQL Database, akkor ez a cikk a következőkhöz nyújt segítséget:

- Hangolja be az alkalmazást, és alkalmazzon olyan ajánlott eljárásokat, amelyek javítják a teljesítményt.
- Állítsa be az adatbázist úgy, hogy az indexek és a lekérdezések módosításával hatékonyabban működjön együtt az adatmennyiséggel.

Ez a cikk azt feltételezi, hogy már elvégezte a Azure SQL Database [Database Advisor javaslatainak](sql-database-advisor.md) és a Azure SQL Database [Automatikus hangolási](sql-database-automatic-tuning.md)javaslatainak a meglétét. Azt is feltételezi, hogy áttekintette a [figyelési és hangolási áttekintését](sql-database-monitor-tune-overview.md) , valamint a teljesítménnyel kapcsolatos problémák elhárításával kapcsolatos cikkeket. Emellett ez a cikk azt feltételezi, hogy nem rendelkezik processzor-erőforrásokkal, valamint a szolgáltatással kapcsolatos teljesítményproblémák, amelyek feloldhatók a számítási méret vagy a szolgáltatási réteg növelésével, hogy több erőforrást szolgáltassanak az adatbázishoz.

## <a name="tune-your-application"></a>Az alkalmazás hangolása

A hagyományos helyszíni SQL Serverban a kezdeti kapacitás tervezésének folyamata gyakran el van különítve az alkalmazások éles környezetben való futtatásának folyamatával. A hardveres és a termékspecifikus licenceket a rendszer először megvásárolja, a teljesítmény finomhangolását pedig utána végezheti el. Azure SQL Database használatakor érdemes összehangolni az alkalmazások futtatásának folyamatát, Az igény szerinti kapacitás kifizetésének modellje segítségével beállíthatja, hogy az alkalmazás a lehető legkevesebb erőforrást használja, ahelyett, hogy az alkalmazásra vonatkozó jövőbeli növekedési terveket kitalálja, ami gyakran helytelen. Egyes ügyfelek dönthetnek úgy, hogy nem hangolnak be egy alkalmazást, hanem több hardveres erőforrást is kiépítenek. Ez a megközelítés akkor lehet hasznos, ha egy kiemelt időszakban nem kívánja módosítani a kulcskezelő alkalmazást. Az alkalmazások finomhangolása azonban csökkentheti az erőforrás-követelményeket és az alacsonyabb havi számlákat, ha a Azure SQL Database szolgáltatási szintjeit használja.

### <a name="application-characteristics"></a>Alkalmazás jellemzői

Bár Azure SQL Database szolgáltatási rétegek úgy lettek kialakítva, hogy javítják az alkalmazások teljesítményének stabilitását és kiszámíthatóságát, néhány ajánlott eljárás segítségével hangolhatja be az alkalmazást, hogy jobban kihasználhassa az erőforrásokat a számítási méretekben. Bár számos alkalmazásnak jelentős teljesítménye van a nagyobb számítási méretre vagy szolgáltatási szintre való áttéréssel, néhány alkalmazásnak további hangolásra van szüksége a magasabb színvonalú szolgáltatás igénybevételéhez. A teljesítmény növelése érdekében vegye fontolóra a további alkalmazások finomhangolását az alábbi tulajdonságokkal rendelkező alkalmazásokhoz:

- **Lassú teljesítményű alkalmazások a "Csevegő" viselkedés miatt**

  A csevegő alkalmazások a hálózati késésre érzékeny, túlzott adatelérési műveleteket tesznek elérhetővé. Előfordulhat, hogy módosítania kell az ilyen típusú alkalmazásokat, hogy csökkentse az adatelérési műveletek számát az SQL Database-ben. Például javíthatja az alkalmazások teljesítményét olyan technikák használatával, mint az ad hoc lekérdezések kötegelt futtatása vagy a lekérdezések áthelyezése a tárolt eljárásokra. További információ: batch- [lekérdezések](#batch-queries).

- **Nagy mennyiségű, egyetlen gép által nem támogatott intenzív számítási feladattal rendelkező adatbázisok**

   Azok az adatbázisok, amelyek meghaladják a legmagasabb prémium szintű számítási méret erőforrásait, kihasználhatják a munkaterhelés horizontális felskálázását. További információ: [adatbázisok közti](#cross-database-sharding) [felosztás és funkcionális particionálás](#functional-partitioning).

- **Az optimálisnál gyengébb lekérdezésekkel rendelkező alkalmazások**

  A rosszul beállított lekérdezésekkel rendelkező alkalmazások, különösen az adatelérési rétegben lévő alkalmazások esetében nem előnyösek a nagyobb számítási méret. Ide tartoznak azok a lekérdezések, amelyek nem tartalmaznak WHERE záradékot, hiányoznak az indexek, vagy elavult statisztikákkal rendelkeznek. Ezek az alkalmazások a szabványos lekérdezési teljesítmény-hangolási technikák előnyeit élvezik. További információ: [hiányzó indexek](#identifying-and-adding-missing-indexes) és [lekérdezések finomhangolása és célzása](#query-tuning-and-hinting).

- **Az optimálisnál gyengébb adatelérési kialakítással rendelkező alkalmazások**

   Az adathozzáférési párhuzamosságtal kapcsolatos problémákat okozó alkalmazások, például a holtpont, nem részesülhetnek nagyobb számítási méretekben. Érdemes lehet a Azure SQL Database az Azure gyorsítótárazási szolgáltatással vagy más gyorsítótárazási technológiával az ügyfél oldalán tárolt adatgyorsítótárazással csökkenteni a. Lásd: az [alkalmazási rétegek gyorsítótárazása](#application-tier-caching).

## <a name="tune-your-database"></a>Az adatbázis hangolása

Ebben a szakaszban megvizsgáljuk azokat a technikákat, amelyekkel Azure SQL Database hangolhatja az alkalmazás legjobb teljesítményét, és futtathatja a lehető legalacsonyabb számítási mérettel. Ezek a technikák a hagyományos SQL Server hangolják össze az ajánlott eljárásokat, de mások a Azure SQL Databasera vonatkoznak. Bizonyos esetekben megvizsgálhatja az adatbázisok felhasznált erőforrásait, hogy megkeresse a területeken a hagyományos SQL Server technikák további finomhangolását és kiterjesztését Azure SQL Databaseokban való működéshez.

### <a name="identifying-and-adding-missing-indexes"></a>Hiányzó indexek azonosítása és hozzáadása

A OLTP-adatbázis teljesítményével kapcsolatos gyakori probléma a fizikai adatbázis kialakítására vonatkozik. Az adatbázis-sémákat gyakran nagy léptékű tesztelés nélkül tervezték és szállították (a terhelésben vagy az adatmennyiségben). Sajnos előfordulhat, hogy egy lekérdezési terv teljesítménye kis méretben elfogadható, de jelentősen csökkent a termelési szintű adatmennyiségek esetében. Ennek a hibának a leggyakoribb forrása a megfelelő indexek hiánya, hogy a szűrők vagy más korlátozások megfeleljenek a lekérdezésekben. A hiányzó indexek általában táblázatos vizsgálatnak minősülnek, ha az indexek keresése elég.

Ebben a példában a kiválasztott lekérdezési terv egy vizsgálatot használ, ha a keresés elegendő lenne:

```sql
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
```

![Hiányzó indexekkel rendelkező lekérdezési terv](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database segítségével megkeresheti és kijavíthatja a hiányzó indexek általános feltételeit. A Azure SQL Databasebe beépített DMV megtekintheti azokat a lekérdezési fordításokat, amelyekben az index jelentősen csökkentheti a lekérdezés futtatásának becsült költségeit. A lekérdezés végrehajtása során SQL Database nyomon követi, hogy milyen gyakran hajtja végre a rendszer az egyes lekérdezési terveket, és nyomon követi a végrehajtási lekérdezési terv közötti becsült különbséget, valamint azt, hogy az adott index hol található. Ezekkel a DMV gyorsan kitalálhatja, hogy a fizikai adatbázis kialakításának módosításai javíthatják-e az adatbázis általános számítási feladatait és a valós számítási feladatokat.

A lekérdezés segítségével kiértékelheti a lehetséges hiányzó indexeket:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

Ebben a példában a lekérdezés a következő javaslatot eredményezte:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

A létrehozást követően ugyanez a SELECT utasítás egy másik tervet is választ, amely egy keresést használ a vizsgálat helyett, majd a tervet hatékonyabban hajtja végre:

![A javított indexekkel rendelkező lekérdezési terv](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A legfontosabb az, hogy a megosztott, a nyersanyag-rendszerek i/o-kapacitása korlátozottabb, mint egy dedikált kiszolgáló-gép esetén. A felesleges i/o-k minimálisra csökkentése a rendszer maximális kihasználása érdekében a Azure SQL Database szolgáltatási szintjeinek minden számítási méretének DTU. A megfelelő fizikai adatbázis-kialakítási lehetőségek jelentős mértékben javítják az egyes lekérdezések késését, javítják a méretezési egység által kezelt egyidejű kérelmek átviteli sebességét, és minimálisra csökkenthetik a lekérdezés teljesítéséhez szükséges költségeket. A hiányzó index DMV kapcsolatos további információkért lásd: [sys. dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Lekérdezések finomhangolása és célzása

Azure SQL Database a lekérdezés-optimalizáló a hagyományos SQL Server lekérdezés-optimalizáló szolgáltatáshoz hasonló. A lekérdezések finomhangolására vonatkozó ajánlott eljárások többsége az Azure SQL Databasere is vonatkozik. Ha a lekérdezéseket Azure SQL Databaseban hangolja össze, a további előnyökkel jár az összesített erőforrás-igények csökkentése. Előfordulhat, hogy az alkalmazás alacsonyabb áron fut, mint egy nem megfelelően beállított érték, mert alacsonyabb számítási méreten futhat.

A SQL Server gyakori példája, amely a Azure SQL Databasera is vonatkozik, hogy a lekérdezés hogyan optimalizálja a "elemzések" paramétereit. A fordítás során a lekérdezés-optimalizáló kiértékeli egy paraméter aktuális értékét annak meghatározására, hogy az képes-e az optimális lekérdezési terv létrehozásához. Habár ez a stratégia gyakran egy olyan lekérdezési tervhez vezethet, amely jelentősen gyorsabb, mint az ismert paraméterérték nélküli csomag, amely jelenleg nem tökéletesen működik a SQL Server és a Azure SQL Database. Előfordulhat, hogy a paraméter nem kerül a szippantás állapotba, és néha a paraméter elemzése megtörténik, de a generált terv a számítási feladatokhoz tartozó paraméterek teljes készletének optimálisnál rosszabb. A Microsoft lekérdezési mutatókat (irányelveket) is tartalmaz, amelyekkel szándékosan megadhatja a leképezést, és felülbírálhatja a paraméterek elemzésének alapértelmezett viselkedését. Gyakran előfordul, hogy ha tippeket használ, megjavíthatja azokat az eseteket, amelyekben az alapértelmezett SQL Server vagy Azure SQL Database viselkedése nem tökéletes egy adott ügyfél-munkaterheléshez.

A következő példa azt mutatja be, hogyan hozhatja ki a lekérdezési processzor olyan tervet, amely optimálisan használható a teljesítményre és az erőforrásokra vonatkozó követelményekhez. Ez a példa azt is mutatja, hogy ha egy lekérdezési mutatót használ, csökkentheti a lekérdezés futási idejét és az SQL-adatbázishoz szükséges erőforrás-követelményeket:

```sql
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
```

A telepítési kód olyan táblát hoz létre, amely elferdített adateloszlást tartalmaz. Az optimális lekérdezési terv attól függ, hogy melyik paraméter van kiválasztva. Sajnos a séma gyorsítótárazási viselkedése nem mindig újrafordítja a lekérdezést a leggyakoribb paraméter értéke alapján. Így előfordulhat, hogy az optimálisnál nagyobb méretet kell gyorsítótárazni, és sok értékhez használni, még akkor is, ha egy másik terv az átlagosnál jobb megoldás. Ezután a lekérdezési terv két, azonos módon létrehozott tárolt eljárást hoz létre, azzal a kivétellel, hogy egy speciális lekérdezési mutatóval rendelkezik.

```sql
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
```

Javasoljuk, hogy várjon legalább 10 percet, mielőtt megkezdené a példa 2. részét, hogy az eredmények különbözőek legyenek az eredményül kapott telemetria-adatban.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

A példa egyes részei a 1 000-as paraméteres INSERT utasítás futtatását kísérlik meg (egy megfelelő terhelés létrehozásához, amelyet tesztelési adatkészletként kell használni). Ha tárolt eljárásokat hajt végre, a lekérdezési processzor megvizsgálja az első fordítás során az eljárásnak átadott paraméterérték értékét (a "szippantás" paraméter). A processzor gyorsítótárazza az eredményül kapott tervet, és a későbbi meghívásokhoz használja, még akkor is, ha a paraméter értéke eltér. Lehetséges, hogy az optimális csomag nem használható minden esetben. Előfordulhat, hogy a-optimalizáló irányításával olyan tervet szeretne kiválasztani, amely a lekérdezés első lefordításakor nem a megadott esetnél jobb. Ebben a példában a kezdeti terv egy "vizsgálat" tervet hoz létre, amely az összes sort beolvassa az összes olyan érték megtalálásához, amely megfelel a paraméternek:

![Lekérdezés finomhangolása ellenőrzési terv használatával](./media/sql-database-performance-guidance/query_tuning_1.png)

Mivel az eljárást az 1. érték használatával hajtottuk végre, az eredményül kapott terv az 1. értékhez optimális volt, de a tábla összes többi értékének is optimális volt. Az eredmény valószínűleg nem az, amit szeretne, ha minden csomagot véletlenszerűen választ ki, mert a terv lassabban működik, és több erőforrást használ.

Ha a tesztet `SET STATISTICS IO` beállítással `ON`ra futtatja, akkor ebben a példában a logikai vizsgálat a színfalak mögött történik. Láthatja, hogy a csomag 1 148 olvasást végez (ami nem hatékony, ha az átlagos esetben csak egy sort kell visszaadnia):

![Lekérdezés finomhangolása logikai vizsgálat használatával](./media/sql-database-performance-guidance/query_tuning_2.png)

A példa második része egy lekérdezési mutató használatával közli, hogy az optimalizáló egy adott értéket használjon a fordítási folyamat során. Ebben az esetben kényszeríti a lekérdezési processzort, hogy figyelmen kívül hagyja a paraméterként átadott értéket, és ehelyett `UNKNOWN`. Ez olyan értékre hivatkozik, amely az átlagos gyakorisággal rendelkezik a táblában (a ferdeség figyelmen kívül hagyásával). Az eredményül kapott terv egy olyan Seek-alapú csomag, amely gyorsabban működik, és átlagosan kevesebb erőforrást használ, mint a terv 1. része:

![Lekérdezés finomhangolása lekérdezési javaslat használatával](./media/sql-database-performance-guidance/query_tuning_3.png)

Megtekintheti a **sys. resource_stats** tábla hatásait (a teszt végrehajtásának idejétől, valamint az adatoknak a táblával való feltöltésének időpontjában). Ebben a példában az 1. rész a 22:25:00-es időintervallumban, a 2. rész pedig a 22:35:00-es időpontban fut. A korábbi időablak több erőforrást használt az adott időtartományban, mint a későbbi (a tervezés hatékonyságának javítása miatt).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Lekérdezés-hangolási példa eredményei](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Bár az ebben a példában szereplő kötet szándékosan kicsi, az optimálisnál kisebb paraméterek hatása jelentős lehet, különösen nagyobb adatbázisok esetén. A szélsőséges esetekben a gyors esetek és a lassú esetek esetében is másodpercek alatt lehet a különbség.

A **sys. resource_stats** vizsgálatával megállapíthatja, hogy egy teszt erőforrása több vagy kevesebb erőforrást használ, mint egy másik teszt. Amikor összehasonlítja az adatmennyiséget, elkülöníti a tesztek időzítését, hogy ne legyenek ugyanabban az 5 perces ablakban a **sys. resource_stats** nézetben. A gyakorlat célja, hogy minimálisra csökkentse a felhasznált erőforrások teljes mennyiségét, és ne csökkentse a maximális erőforrásokat. Általában egy kódrészlet optimalizálása a késéshez is csökkenti az erőforrások felhasználását. Győződjön meg arról, hogy az alkalmazáson végzett módosítások szükségesek, és hogy a módosítások nem érintik negatívan az alkalmazásban a lekérdezési mutatókat használó személy felhasználói élményét.

Ha a munkaterhelés ismétlődő lekérdezéseket tartalmaz, gyakran érdemes rögzíteni és érvényesíteni a csomag választási lehetőségeit, mivel az az adatbázis üzemeltetéséhez szükséges minimális erőforrás-méretet vezérli. Az érvényesítése után időnként újra megvizsgálja a csomagokat, hogy azok ne legyenek lecsökkentve. További információ a [lekérdezési mutatókkal (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Adatbázisok közti horizontális felskálázás

Mivel a Azure SQL Database a termék hardverén fut, az egyes adatbázisok kapacitásának korlátai alacsonyabbak, mint a hagyományos helyszíni SQL Server telepítésekor. Egyes ügyfelek horizontális Felskálázási technikák használatával terjesztik az adatbázis-műveleteket több adatbázisra, ha a műveletek nem illeszkednek a Azure SQL Database egyes adatbázisainak korlátaihoz. A legtöbb horizontális Felskálázási technikát használó ügyfelek több adatbázisra osztották fel az adatAzure SQL Databaset egyetlen dimenzióban. Ehhez a megközelítéshez meg kell ismernie, hogy a OLTP-alkalmazások gyakran olyan tranzakciókat hajtanak végre, amelyek csak egy sorra vagy a séma sorainak egy kis csoportjára vonatkoznak.

> [!NOTE]
> A SQL Database mostantól biztosít egy könyvtárat, amely segít a horizontális felskálázásban. További információ: [Elastic Database ügyféloldali kódtár – áttekintés](sql-database-elastic-database-client-library.md).

Ha például egy adatbázis az ügyfél nevét, sorrendjét és a rendelés részleteit (például a SQL Servert tartalmazó hagyományos Northwind-adatbázist) használja, akkor az adatokat több adatbázisra is feloszthatja, ha csoportosítja az ügyfelet a kapcsolódó sorrendtel és a rendelés részleteivel. információk. Garantálhatja, hogy az ügyfél adatvédelme egy különálló adatbázisban maradjon. Az alkalmazás különböző ügyfeleket oszt szét az adatbázisok között, így hatékonyan terjeszti a terhelést több adatbázis között. A horizontális felskálázással az ügyfelek nem csak a maximális adatbázis-méretet tudják elkerülni, de Azure SQL Database a különböző számítási méretek korlátainál lényegesen nagyobb munkaterheléseket is feldolgozhatnak, feltéve, hogy minden egyes adatbázis illeszkedik a DTU.

Bár az adatbázisok horizontális felskálázása nem csökkenti a megoldás összesített erőforrás-kapacitását, rendkívül hatékony a több adatbázison keresztül elosztott, nagyon nagy megoldások támogatásához. Az egyes adatbázisok más számítási méretekben futhatnak, hogy a nagyon nagy, "hatékony" és magas erőforrás-igényű adatbázisokra is felhasználhatók legyenek.

### <a name="functional-partitioning"></a>Funkcionális particionálás

SQL Server a felhasználók gyakran egyesítenek számos függvényt egy adott adatbázisban. Ha például egy alkalmazás logikával kezeli az áruház leltárát, akkor az adatbázishoz a leltárhoz, a beszerzési rendelésekhez, a tárolt eljárásokhoz, valamint a hónap végét kezelő, indexelt vagy jelentős nézetekhez tartozó logika tartozhat. Ezzel a technikával könnyebben felügyelheti az adatbázist olyan műveletekhez, mint a biztonsági mentés, de a hardver méretét is megköveteli, hogy a maximális terhelést az alkalmazás összes funkcióján kezeljék.

Ha kibővíthető architektúrát használ a Azure SQL Databaseban, érdemes lehet egy alkalmazás különböző funkcióit különböző adatbázisokra bontani. Ezzel a technikával az egyes alkalmazások egymástól függetlenül méretezhetők. Mivel az alkalmazás válik elérhetővé (és az adatbázis terhelése növekszik), a rendszergazda az alkalmazás egyes funkcióinak független számítási méreteit is kiválaszthatja. Ebben az architektúrában a korlátnál egy alkalmazás nagyobb lehet, mint egy árucikk-gép, mert a terhelés több gépen is eloszlik.

### <a name="batch-queries"></a>Batch-lekérdezések

Az olyan alkalmazások esetében, amelyek nagy mennyiségű, gyakori, ad hoc lekérdezéssel érik el az adatelérést, jelentős mennyiségű válaszidő kerül elköltésre az alkalmazási réteg és a Azure SQL Databasei réteg közötti hálózati kommunikáció során. Még ha az alkalmazás és a Azure SQL Database is ugyanabban az adatközpontban található, akkor a kettő közötti hálózati késést nagy számú adatelérési művelettel lehet kinagyítani. Ha csökkenteni szeretné az adatelérési műveletek hálózati menetét, érdemes lehet az ad hoc lekérdezéseket batch használatával vagy tárolt eljárásként lefordítani. Ha az ad hoc lekérdezéseket kötegbe helyezi, több lekérdezés is elküldhető egyetlen útvonalon a Azure SQL Databaseba. Ha egy tárolt eljárásban ad hoc lekérdezéseket fordít, akkor ugyanazt az eredményt érheti el, mint ha batch-t használ. A tárolt eljárások használatával a Azure SQL Database a lekérdezési csomagok gyorsítótárazásának esélyét is, így újra használhatja a tárolt eljárást.

Egyes alkalmazások írási Igényűek. Előfordulhat, hogy az adatbázis teljes i/o-terhelését úgy csökkenti, hogy a Batch-írásokat együtt szeretné megfontolni. Ez gyakran olyan egyszerű, mint a Explicit tranzakciók használata a tárolt eljárásokban és ad hoc kötegekben lévő tranzakciók automatikus véglegesítés helyett. A különböző használható technikák kiértékelését lásd: az [Azure-beli SQL Database alkalmazások kötegelt feldolgozásának módszerei](sql-database-use-batching-to-improve-performance.md). Kísérletezzen a saját számítási feladataival, és keresse meg a megfelelő modellt a kötegelt feldolgozáshoz. Ügyeljen arra, hogy a modell némileg eltérő tranzakciós konzisztencia-garanciával rendelkezzen. Az erőforrás-használatot lecsökkentő megfelelő számítási feladatok megtalálásához a konzisztencia és a teljesítmény-kompromisszum megfelelő kombinációját kell megkeresni.

### <a name="application-tier-caching"></a>Alkalmazás-réteg gyorsítótárazása

Egyes adatbázis-alkalmazások olvasási és nagy terheléssel rendelkeznek. A gyorsítótárazási rétegek csökkenthetik az adatbázis terhelését, és esetleg csökkenthetik az adatbázisok támogatásához szükséges számítási méretet Azure SQL Database használatával. Ha a [Redis-hez Azure cache](https://azure.microsoft.com/services/cache/)-t használ, akkor az adatok beolvasása nagy terheléssel elvégezhető egyszer (vagy akár egyszer az alkalmazási rétegbeli gépen, attól függően, hogy hogyan van konfigurálva), majd az SQL-adatbázison kívül tárolja azokat. Ez az adatbázis terhelésének (CPU és olvasási IO) csökkentése, de hatással van a tranzakciós konzisztenciare, mert előfordulhat, hogy a gyorsítótárból beolvasott adatok nem szinkronizálhatók az adatbázisban lévő adatokkal. Bár számos alkalmazásban elfogadható bizonyos fokú inkonzisztencia, ez nem igaz az összes számítási feladathoz. Az alkalmazásokra vonatkozó követelmények teljes mértékben tisztában kell lennie az alkalmazási szintű gyorsítótárazási stratégia megvalósítása előtt.

## <a name="next-steps"></a>Következő lépések

- A DTU-alapú szolgáltatási rétegekkel kapcsolatos további információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
- A virtuális mag-alapú szolgáltatási rétegekkel kapcsolatos további információkért lásd: [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
- További információ a rugalmas készletekről: [Mi az az Azure rugalmas készlet?](sql-database-elastic-pool.md)
- További információ a teljesítményről és a rugalmas készletekről: [Mikor érdemes rugalmas készletet fontolóra venni](sql-database-elastic-pool-guidance.md)
