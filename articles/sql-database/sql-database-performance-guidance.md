---
title: Teljesítményhangolási útmutató alkalmazásokhoz és adatbázisokhoz
description: Ismerje meg az azure SQL Database teljesítményéhez használható adatbázis-alkalmazások és adatbázisok finomhangolását.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab; jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 4f30ebe39d86db7076baa8c29b2a5cf060b07bf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255950"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database"></a>Alkalmazások és adatbázisok finomhangolása az Azure SQL Database teljesítményéhez

Miután azonosított egy teljesítményproblémát, amellyel az SQL Database-nek szembe kell néznie, ez a cikk a következőket segíti:

- Hangolja be az alkalmazást, és alkalmazzon néhány bevált gyakorlatot, amelyek javíthatják a teljesítményt.
- Az adatbázis finomhangolásával az indexek és lekérdezések módosításával hatékonyabban dolgozhat az adatokkal.

Ez a cikk feltételezi, hogy már dolgozott az Azure SQL Database [adatbázis-tanácsadó ajánlásain](sql-database-advisor.md) és az Azure SQL Database [automatikus hangolási javaslatain.](sql-database-automatic-tuning.md) Azt is feltételezi, hogy áttekintette [A figyelés és a hangolás áttekintése,](sql-database-monitor-tune-overview.md) valamint a teljesítményproblémák elhárításával kapcsolatos kapcsolódó cikkek. Emellett ez a cikk feltételezi, hogy nem rendelkezik a CPU-erőforrások, futással kapcsolatos teljesítmény probléma, amely megoldható a számítási méret vagy a szolgáltatási szint növelésével, hogy több erőforrást az adatbázishoz.

## <a name="tune-your-application"></a>Az alkalmazás finomhangolása

A hagyományos helyszíni SQL Server, a folyamat a kezdeti kapacitástervezés gyakran elkülönül a folyamat egy alkalmazás éles környezetben futó folyamat. Először a hardver- és terméklicenceket vásárolják meg, majd a teljesítményhangolást később végzik el. Az Azure SQL Database használataesetén célszerű egy alkalmazás futtatásának és finomhangolásának folyamatát összeegyeztet. A modell a kapacitás igény szerinti fizetés, beállíthatja az alkalmazást, hogy a minimális szükséges erőforrásokat most, ahelyett, hogy a hardver túlkiépítése alapján találgatások a jövőbeli növekedési tervek egy alkalmazás, amely gyakran helytelen. Egyes ügyfelek dönthetnek úgy, hogy nem hangolják be az alkalmazást, hanem a hardvererőforrások túlterhelését választják. Ez a megközelítés lehet, hogy jó ötlet, ha nem szeretné módosítani a kulcsfontosságú alkalmazás egy forgalmas időszakban. Az alkalmazások finomhangolása azonban minimálisra csökkentheti az erőforrás-igényeket és csökkentheti a havi számlákat, ha az Azure SQL Database szolgáltatásrétegeit használja.

### <a name="application-characteristics"></a>Alkalmazási jellemzők

Bár az Azure SQL Database szolgáltatási szintek célja, hogy javítsa a teljesítmény stabilitását és kiszámíthatóságát egy alkalmazás, néhány ajánlott eljárások segítségével az alkalmazás finomhangolása, hogy jobban kihasználják az erőforrások at számítási méret. Bár számos alkalmazás jelentős teljesítménynövekedéssel rendelkezik egyszerűen egy nagyobb számítási méretre vagy szolgáltatási szintre való váltással, egyes alkalmazásoknak további finomhangolásra van szükségük a magasabb szintű szolgáltatás előnyeinek kihasználása érdekében. A nagyobb teljesítmény érdekében fontolja meg az alkalmazások további hangolását az alábbi jellemzőkkel rendelkező alkalmazásokhoz:

- **A "beszédes" viselkedés miatt lassú teljesítményt nyújtó alkalmazások**

  A csevegőalkalmazások a hálózati késésre érzékeny adatelérési műveleteket tesznek le. Előfordulhat, hogy módosítania kell az ilyen típusú alkalmazásokat az SQL-adatbázis adatelérési műveleteiszámának csökkentése érdekében. Például javíthatja az alkalmazások teljesítményét olyan technikák használatával, mint az ad hoc lekérdezések kötegelése vagy a lekérdezések tárolt eljárásokba való áthelyezése. További információt a [Batch-lekérdezések](#batch-queries)című témakörben talál.

- **Olyan intenzív munkaterheléssel rendelkező adatbázisok, amelyeket egy teljes egyetlen gép nem támogathat**

   Adatbázisok, amelyek meghaladják a legnagyobb prémium szintű számítási méretű erőforrásokat, előnyös lehet a számítási feladatok horizontális felskálázása. További információ: [Cross-database sharding](#cross-database-sharding) and [Functional partitioning](#functional-partitioning).

- **Az optimálistól elmaradó lekérdezésekkel rendelkező alkalmazások**

  Előfordulhat, hogy a rosszul hangolt lekérdezésekkel rendelkező alkalmazások, különösen az adatelérési rétegben lévők nem részesülhetnek nagyobb számítási mérettel. Ez magában foglalja azokat a lekérdezéseket, amelyek nem rendelkeznek WHERE záradékkal, hiányzó indexekkel rendelkeznek, vagy elavult statisztikákkal rendelkeznek. Ezek az alkalmazások a szabványos lekérdezési teljesítményhangolási technikák előnyeit élvezik. További információ: [Hiányzó indexek](#identifying-and-adding-missing-indexes) és [Lekérdezéshangolás és célzás.](#query-tuning-and-hinting)

- **Az optimálistól elmaradó adatelérési kialakítással rendelkező alkalmazások**

   Alkalmazások, amelyek rejlő adat-hozzáférési egyidejűségi problémák, például holtponti, előfordulhat, hogy nem részesülnek a nagyobb számítási méret. Fontolja meg az azure-beli SQL-adatbázissal szembeni adatváltások csökkentését az ügyféloldali adatok ügyféloldali gyorsítótárazásával az Azure-gyorsítótárazási szolgáltatással vagy egy másik gyorsítótárazási technológiával. Lásd: [Alkalmazásszintű gyorsítótárazás](#application-tier-caching).

## <a name="tune-your-database"></a>Az adatbázis finomhangolása

Ebben a szakaszban megvizsgálunk néhány technikát, amelyek segítségével az Azure SQL Database-t az alkalmazás legjobb teljesítményének elérése érdekében, és futtathatja a lehető legalacsonyabb számítási méretben. Ezek közül a technikák közül néhány megfelel a hagyományos SQL Server finomhangolási gyakorlati tanácsok, de mások az Azure SQL Database-re jellemző. Bizonyos esetekben megvizsgálhatja az adatbázis felhasznált erőforrásait, hogy olyan területeket találjon, amelyek tovább hangolhatják és kiterjeszthetik a hagyományos SQL Server-technikákat az Azure SQL Database-ben való munkára.

### <a name="identifying-and-adding-missing-indexes"></a>Hiányzó indexek azonosítása és hozzáadása

Az OLTP-adatbázis teljesítményének gyakori problémája a fizikai adatbázis-tervezéssel kapcsolatos. Az adatbázissémákat gyakran nagyméretű (terhelési vagy adatmennyiségű) tesztelés nélkül tervezik és szállítják. Sajnos a lekérdezési terv teljesítménye kis léptékben elfogadható, de jelentősen csökken a termelési szintű adatmennyiségek alatt. A probléma leggyakoribb forrása a megfelelő indexek hiánya a lekérdezésben a szűrők vagy más korlátozások teljesítéséhez. Gyakran előfordul, hogy a hiányzó indexek táblavizsgálatként jelennek meg, amikor egy indexkeresés elegendő lehet.

Ebben a példában a kiválasztott lekérdezési terv egy vizsgálat, ha a keresés elegendő lenne:

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

Az Azure SQL Database segítségével megkeresheti és javíthatja a gyakori hiányzó indexfeltételeket. Az Azure SQL Database-be beépített DMV-k olyan lekérdezési fordításokat vizsgálnak meg, amelyekben az index jelentősen csökkentené a lekérdezés futtatásának becsült költségét. A lekérdezés végrehajtása során az SQL Database nyomon követi az egyes lekérdezési tervek végrehajtásának gyakran, és nyomon követi a becsült rést a végrehajtó lekérdezési terv és az elképzelt között, ahol az index létezett. Ezekkel a DMV-k segítségével gyorsan kitalálni, hogy a fizikai adatbázis-tervezés módosításai javíthatják az adatbázis teljes munkaterhelési költségét és a tényleges munkaterhelést.

Ezzel a lekérdezéssel kiértékelheti a lehetséges hiányzó indexeket:

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

Létrehozása után ugyanaz a SELECT utasítás egy másik tervet választ, amely vizsgálat helyett keresést használ, majd hatékonyabban hajtja végre a tervet:

![Javított indexekkel rendelkező lekérdezési terv](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A legfontosabb betekintést, hogy az IO kapacitása egy megosztott, árurendszer korlátozottabb, mint egy dedikált kiszolgálógép. A szükségtelen I/O minimalizálása a rendszer előnyeit az Azure SQL Database szolgáltatási szintek minden egyes számítási méretének maximális kihasználásához. A megfelelő fizikai adatbázis-tervezési lehetőségek jelentősen javíthatják az egyes lekérdezések késését, javíthatják a méretezési egységenként kezelt egyidejű kérelmek átviteli képességét, és minimalizálhatják a lekérdezés kielégítéséhez szükséges költségeket. A hiányzó indexdv-ekről a [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)című témakörben talál további információt.

### <a name="query-tuning-and-hinting"></a>Lekérdezéshangolás és célzás

Az Azure SQL Database lekérdezésoptimalizálója hasonló a hagyományos SQL Server-lekérdezésoptimalizálóhoz. A lekérdezések finomításával és a lekérdezésoptimalizáló érvelési modell korlátainak ismertetésével kapcsolatos gyakorlati tanácsok többsége az Azure SQL Database-re is vonatkozik. Ha az Azure SQL Database-ben hangolja be a lekérdezéseket, további előnyökkel járhat az összesített erőforrás-igények csökkentése. Előfordulhat, hogy az alkalmazás alacsonyabb költséggel futhat, mint egy nem hangolt megfelelő, mert alacsonyabb számítási méretben is futtatható.

Egy példa, amely gyakori az SQL Server, és amely az Azure SQL Database is vonatkozik, hogy a lekérdezés-optimalizáló "szippantás" paramétereket. A fordítás során a lekérdezésoptimalizáló kiértékeli egy paraméter aktuális értékét, és megállapítja, hogy képes-e optimálisabb lekérdezési tervet létrehozni. Bár ez a stratégia gyakran vezethet a lekérdezési terv, amely jelentősen gyorsabb, mint egy előre ismert paraméterértékek nélkül összeállított terv, jelenleg nem működik tökéletesen mind az SQL Server és az Azure SQL Database. Néha a paraméter nem szippantott, és néha a paraméter szippantás, de a generált terv nem optimális a teljes paraméterértékek egy számítási feladatban. A Microsoft lekérdezési tippeket (direktívákat) is tartalmaz, így a szándékot tudatosabban adhatja meg, és felülbírálhatja a paraméterszippantés alapértelmezett viselkedését. Gyakran, ha tippeket használ, kijavíthatja azokat az eseteket, amelyekben az alapértelmezett SQL Server vagy az Azure SQL Database viselkedése nem tökéletes egy adott ügyfél-munkaterheléshez.

A következő példa bemutatja, hogy a lekérdezésfeldolgozó hogyan hozhat létre olyan tervet, amely a teljesítmény és az erőforrás-követelmények számára egyaránt nem optimális. Ez a példa azt is mutatja, hogy ha lekérdezési emlékeztetőt használ, csökkentheti az SQL-adatbázis lekérdezési futási idejét és erőforrás-szükségletét:

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

A beállítási kód olyan táblát hoz létre, amely ferde adateloszlással rendelkezik. Az optimális lekérdezési terv attól függően változik, hogy melyik paraméter van kiválasztva. Sajnos a terv gyorsítótárazási viselkedése nem mindig fordítja újra a lekérdezést a leggyakoribb paraméterérték alapján. Így lehetséges, hogy egy szuboptimális terv gyorsítótárazott és használt sok érték, még akkor is, ha egy másik terv lehet egy jobb terv választás átlagosan. Ezután a lekérdezési terv két azonos tárolt eljárást hoz létre, azzal a különbséggel, hogy az egyikrendelkezik egy speciális lekérdezési tippel.

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

Azt javasoljuk, hogy várjon legalább 10 percet, mielőtt elkezdi a példa 2.

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

Ebben a példában minden egyes része 1000-szer próbál paraméterezett beszúrási utasítást futtatni (elegendő terhelést generálni a tesztadatkészletként való használatra). A tárolt eljárások végrehajtásakor a lekérdezésfeldolgozó megvizsgálja az eljárásnak az első összeállítássorán átadott paraméterértéket ("szippantás paraméter"). A processzor gyorsítótárazza az eredményül kapott tervet, és későbbi meghívásokra használja, még akkor is, ha a paraméter értéke eltérő. Előfordulhat, hogy az optimális terv nem minden esetben használható. Néha meg kell irányítani az optimalizáló, hogy válasszon egy tervet, amely jobb az átlagos esetben, nem pedig az adott esetben, amikor a lekérdezés először állították össze. Ebben a példában a kezdeti terv létrehoz egy "beolvasási" tervet, amely beolvassa az összes sort, hogy megtalálja a paraméternek megfelelő értékeket:

![Lekérdezéshangolás bekéselési terv használatával](./media/sql-database-performance-guidance/query_tuning_1.png)

Mivel az eljárást az 1 érték használatával hajtottuk végre, az eredményül kapott terv optimális volt az 1-es értékhez, de a táblázatban szereplő összes többi érték esetében nem volt optimális. Az eredmény valószínűleg nem az, amit szeretne, ha véletlenszerűen választaná ki az egyes terveket, mert a terv lassabban teljesít, és több erőforrást használ.

Ha a tesztet `SET STATISTICS IO` a `ON`beállítással futtatja, a példában szereplő logikai befuttatási munka a színfalak mögött történik. Láthatjuk, hogy van 1148 olvasás végzett a terv (ami nem hatékony, ha az átlagos eset, hogy vissza csak egy sor):

![Lekérdezés hangolása logikai vizsgálat tal](./media/sql-database-performance-guidance/query_tuning_2.png)

A példa második része egy lekérdezési emlékeztetőt használ, hogy megmondja az optimalizálónak, hogy egy adott értéket használjon a fordítási folyamat során. Ebben az esetben arra kényszeríti a lekérdezésfeldolgozót, hogy figyelmen kívül `UNKNOWN`hagyja a paraméterként átadott értéket, hanem a . Ez olyan értékre vonatkozik, amelynek átlagos gyakorisága van a táblázatban (figyelmen kívül hagyva a ferdeség). Az eredményül kapott terv egy gyorsabb és átlagosan kevesebb erőforrást használó keresési alapú terv, mint a példa 1.

![Lekérdezés hangolása lekérdezési emlékeztető használatával](./media/sql-database-performance-guidance/query_tuning_3.png)

A hatás a **sys.resource_stats** táblában látható (a teszt végrehajtásának és az adatok feltöltésének időpontjától késés van). Ebben a példában a 22:25:00 időablak alatt végrehajtott 1. A korábbi időablak több erőforrást használt ebben az időszakban, mint a későbbi (a terv hatékonyságának javulása miatt).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Lekérdezéshangolási példa eredményei](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Bár a kötet ebben a példában szándékosan kicsi, az optimálisalatti paraméterek hatása jelentős lehet, különösen a nagyobb adatbázisok. A különbség, szélsőséges esetekben, lehet másodpercek között a gyors esetek és óra lassú esetekben.

Megvizsgálhatja **a sys.resource_stats-t** annak megállapítására, hogy a teszthez használt erőforrás több vagy kevesebb erőforrást használ-e egy másik tesztnél. Az adatok összehasonlításakor válassza el egymástól a tesztek időzítését, hogy ne ugyanabban az 5 perces ablakban jelenjenek meg a **sys.resource_stats** nézetben. A gyakorlat célja, hogy minimalizálja a felhasznált erőforrások teljes mennyiségét, és ne minimalizálja a csúcserőforrásokat. Általában egy kóddarab késésre optimalizálása is csökkenti az erőforrás-felhasználást. Győződjön meg arról, hogy az alkalmazáson végzett módosítások szükségesek, és hogy a módosítások nem befolyásolják negatívan az alkalmazás felhasználói élményét, aki esetleg lekérdezési tippeket használ az alkalmazásban.

Ha egy számítási feladat ismétlődő lekérdezésekkel rendelkezik, gyakran érdemes rögzíteni és érvényesíteni a tervválasztási beállítások optimálisságát, mivel az adatbázis üzemeltetéséhez szükséges minimális erőforrásméret-egységet hajtja. Miután érvényesítette, időnként vizsgálja felül a terveket, hogy megbizonyosodjon arról, hogy azok nem romlottak. A [lekérdezési tippeket (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)ismertetni lehet.

### <a name="very-large-database-architectures"></a>Nagyon nagy adatbázis-architektúrák

Az Azure SQL Database-ben az egyes adatbázisokhoz tartozó [nagy kapacitású](sql-database-service-tier-hyperscale.md) szolgáltatási szint kiadása előtt az ügyfelek az egyes adatbázisok kapacitáskorlátait használják. Ezek a kapacitáskorlátok továbbra is léteznek a rugalmas készletekben és a felügyelt példányokban lévő példány-adatbázis készletezésű adatbázisokhoz. A következő két szakasz két lehetőséget vitat meg az Azure SQL Database nagyon nagy adatbázisaival kapcsolatos problémák megoldásához, ha nem használhatja a nagy kapacitású szolgáltatási szintet.

### <a name="cross-database-sharding"></a>Adatbázisközi szilánkok

Mivel az Azure SQL Database árualapú hardveren fut, az egyes adatbázisok kapacitáskorlátai alacsonyabbak, mint a hagyományos helyszíni SQL Server-telepítés. Egyes ügyfelek a szilánkolási technikák at az adatbázis-műveletek több adatbázisra, ha a műveletek nem férnek el az Azure SQL Database-ben egy adott adatbázis korlátain belül. A legtöbb ügyfél, aki az Azure SQL Database-ben a szilánkolási technikákat használja, egyetlen dimenzióra osztja fel az adatokat több adatbázis között. Ehhez a megközelítéshez meg kell értenie, hogy az OLTP-alkalmazások gyakran olyan tranzakciókat hajtanak végre, amelyek csak egy sorra vagy a séma sorok egy kis csoportjára vonatkoznak.

> [!NOTE]
> Az SQL Database most egy könyvtárat biztosít a szilánkok leküzdéséhez. További információt a [Rugalmas adatbázis-ügyféltár – áttekintés című témakörben talál.](sql-database-elastic-database-client-library.md)

Ha például egy adatbázis ügyfélneve, rendelési és rendelési adatai (például az SQL Serverrel szállított hagyományos northwind-i adatbázis), ezeket az adatokat több adatbázisra is feloszthatja, ha csoportosítja az ügyfelet a kapcsolódó rendelési és rendelési adatokkal. Információ. Garantálhatja, hogy az ügyfél adatai egy adott adatbázisban maradnak. Az alkalmazás a különböző ügyfeleket adatbázisok között osztja fel, és hatékonyan elosztja a terhelést több adatbázis között. A szilánkok használatával az ügyfelek nem csak a maximális adatbázisméret-korlátot kerülhetik el, hanem az Azure SQL Database is feltudja dolgozni a különböző számítási méretek korlátainál jelentősen nagyobb számítási feladatokat, feltéve, hogy minden egyes adatbázis belefér a DTU-ba.

Bár az adatbázis-kicsinyítés nem csökkenti a megoldás összesített erőforrás-kapacitását, rendkívül hatékony a több adatbázisra elosztott nagyon nagy megoldások támogatásában. Minden adatbázis futtatható különböző számítási méret, hogy támogassa a nagyon nagy, "hatékony" adatbázisok magas erőforrás-igényű.

#### <a name="functional-partitioning"></a>Funkcionális particionálás

Az SQL Server felhasználói gyakran sok funkciót kombinálnak egy adott adatbázisban. Ha például egy alkalmazás nak van logikája egy üzlet készletének kezeléséhez, akkor az adatbázisnak lehetnek logikája a készlethez, a beszerzési rendelések nyomon követéséhez, a tárolt eljárásokhoz és az indexelt vagy materializált nézetekhez, amelyek a hónap végi jelentéseket kezelik. Ez a módszer megkönnyíti az adatbázis felügyeletét az olyan műveletekhez, mint a biztonsági mentés, de azt is megköveteli, hogy méretezze a hardvert a maximális terhelés kezeléséhez az alkalmazás összes függvényében.

Ha egy kibővített architektúrát használ az Azure SQL Database-ben, célszerű egy alkalmazás különböző funkcióit különböző adatbázisokra osztani. Ezzel a technikával minden alkalmazás egymástól függetlenül méretezhető. Ahogy egy alkalmazás forgalmasabbá válik (és az adatbázis terhelése nő), a rendszergazda az alkalmazás minden függvényéhez független számítási méreteket választhat. A korlát, ezzel az architektúrával egy alkalmazás nagyobb lehet, mint egy árucikk gép képes kezelni, mert a terhelés több gép között oszlik meg.

### <a name="batch-queries"></a>Kötegelt lekérdezések

Az alkalmazások, amelyek nagy mennyiségű, gyakori, alkalmi lekérdezéshasználatával férnek hozzá az adatokhoz, jelentős mennyiségű válaszidőt fordítanak az alkalmazásszint és az Azure SQL Database-szint közötti hálózati kommunikációra. Még akkor is, ha mind az alkalmazás, mind az Azure SQL Database ugyanabban az adatközpontban van, a kettő közötti hálózati késés nagyszámú adatelérési művelet et nagyítható. Az adatelérési műveletek hálózati adatváltási műveleteinek csökkentése érdekében fontolja meg az ad hoc lekérdezések kötegelésének vagy tárolt eljárásokként történő összeállításának lehetőségét. Ha kötegeli az ad hoc lekérdezéseket, küldhet több lekérdezést egy nagy köteg egy utazás az Azure SQL Database.If you batch the ad hoc queries, you can send multiple queries as one large batch in a one trip to Azure SQL Database. Ha ad hoc lekérdezéseket állít le egy tárolt eljárásban, ugyanazt az eredményt érheti el, mintha kötegeli őket. A tárolt eljárás használatával is ad az előnye, hogy növeli az esélyét a lekérdezési tervek az Azure SQL Database- ben, így a tárolt eljárás újra használhatja.

Egyes alkalmazások írásigényesek. Néha csökkentheti az adatbázis teljes I/O-terhelését, ha figyelembe veszi az írások együtt kötegelésének megfontolását. Ez gyakran olyan egyszerű, mint explicit tranzakciók használata a tárolt eljárásokban és ad hoc kötegekben lévő automatikus véglegesítési tranzakciók helyett. A különböző használható technikák kiértékeléséért [lásd: Kötegelési technikák SQL Database-alkalmazások az Azure-ban.](sql-database-use-batching-to-improve-performance.md) Kísérletezzen a saját munkaterhelésével, hogy megtalálja a megfelelő modellt a kötegeléshez. Győződjön meg arról, hogy egy modell előfordulhat, hogy kissé eltérő tranzakciós konzisztencia garanciákat. Az erőforrás-felhasználást minimalizáló megfelelő munkaterhelés megtalálásához meg kell találni a konzisztencia és a teljesítmény kompromisszumok megfelelő kombinációját.

### <a name="application-tier-caching"></a>Alkalmazásszintű gyorsítótárazás

Egyes adatbázis-alkalmazások olvasási nehéz számítási feladatokat. A gyorsítótárazási rétegek csökkenthetik az adatbázis terhelését, és potenciálisan csökkenthetik az azure SQL Database használatával az adatbázis támogatásához szükséges számítási méretet. Az [Azure Cache for Redis](https://azure.microsoft.com/services/cache/), ha egy olvasási nagy számítási feladatok, az adatok olvasása egyszer (vagy talán egyszer egy alkalmazásszintű gépen, attól függően, hogy hogyan van konfigurálva), és majd tárolja az adatokat az SQL-adatbázison kívül. Ez egy módja annak, hogy csökkentse az adatbázis terhelését (CPU és az I/O olvasása), de hatással van a tranzakciós konzisztenciára, mivel előfordulhat, hogy a gyorsítótárból beolvasott adatok nincsenek szinkronban az adatbázisban lévő adatokkal. Bár sok alkalmazásban bizonyos szintű inkonzisztencia elfogadható, ez nem minden számítási feladatra igaz. Az alkalmazásszintű gyorsítótárazási stratégia megvalósítása előtt teljes mértékben tisztában kell lennie az alkalmazáskövetelményekkel.

## <a name="next-steps"></a>További lépések

- A DTU-alapú szolgáltatási szintekről további információt a [DTU-alapú vásárlási modell ben](sql-database-service-tiers-dtu.md)talál.
- A virtuálismag-alapú szolgáltatási szintekről további információt a [virtuálismag-alapú vásárlási modell ben](sql-database-service-tiers-vcore.md)talál.
- A rugalmas készletekről a [Mi az Azure rugalmas készlete.](sql-database-elastic-pool.md)
- A teljesítményről és a rugalmas készletekről a [Mikor érdemes egy rugalmas készletet figyelembe venni?](sql-database-elastic-pool-guidance.md)
