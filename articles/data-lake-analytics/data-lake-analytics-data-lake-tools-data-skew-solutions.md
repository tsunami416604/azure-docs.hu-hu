---
title: Adatdöntés megoldása – Azure Data Lake Tools for Visual Studio
description: Az Azure Data Lake Tools for Visual Studio használatával az adatdöntési problémák lehetséges megoldásainak elhárítása.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71802553"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Egyenetlen adateloszlással kapcsolatos hibák megoldása az Azure Data Lake Tools for Visual Studióval

## <a name="what-is-data-skew"></a>Mi az adatdöntés?

Röviden kijelentve, az adatdöntés túlreprezentált érték. Képzeld el, hogy 50 adóvizsgáztatot rendelt élaz adóbevallások ellenőrzéséhez, minden egyes amerikai államhoz egy elbírálót. A wyomingi vizsgáztatónak, mivel az ottani lakosság nak kicsi, kevés a tennivalója. Kaliforniában azonban, a vizsgáztató tartják nagyon elfoglalt, mert az állam nagy népessége.
    ![Példa adatdöntési problémára](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

A mi forgatókönyvünkben az adatok egyenlőtlenül oszlanak meg az összes adóvizsgáztató között, ami azt jelenti, hogy egyes vizsgáztatóknak többet kell dolgozniuk, mint másoknak. A saját munkahelyén gyakran tapasztal olyan helyzeteket, mint az adóvizsgáztató példa. Több technikai szempontból, egy csúcspont kap sokkal több adatot, mint a társaik, a helyzet, ami a csúcspont működik több, mint a többiek, és hogy végül lassítja a teljes munkát. Mi a rosszabb, a feladat sikertelen lehet, mert csúcsok lehet, például egy 5 órás futásidejű korlátozás és egy 6 GB-os memória korlátozása.

## <a name="resolving-data-skew-problems"></a>Adatdöntési problémák megoldása

Az Azure Data Lake Tools for Visual Studio segítségével megállapíthatja, hogy a feladat nak van-e adatdöntési problémája. Ha probléma áll fenn, az ebben a szakaszban található megoldásokkal oldhatja meg.

## <a name="solution-1-improve-table-partitioning"></a>1. megoldás: A táblaparticionálás javítása

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>1. lehetőség: A ferde kulcs értékének előzetes szűrése

Ha ez nem befolyásolja az üzleti logikát, előre szűrheti a magasabb frekvenciájú értékeket. Ha például a GUID oszlopban sok 000-000-000 van, előfordulhat, hogy nem szeretné összesíteni ezt az értéket. Az összesítés előtt a "WHERE GUID != "000-000-000" (HELY GUID != "000-000-000" értéket írhatja be a magas frekvenciájú érték szűréséhez.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>2. lehetőség: Válasszon másik partíció- vagy terjesztési kulcsot

Az előző példában, ha csak ellenőrizni szeretné az adó-naplózási munkaterhelés az egész országban/régióban, javíthatja az adatok terjesztését az azonosítószám kulcsként kiválasztásával. Egy másik partíció vagy terjesztési kulcs kiválasztása néha egyenletesebben terjesztheti az adatokat, de meg kell győződnie arról, hogy ez a választás nem befolyásolja az üzleti logikát. Például az egyes államok adóösszegének kiszámításához érdemes lehet _az Államot_ partíciókulcsként kijelölni. Ha továbbra is tapasztalja ezt a problémát, próbálja meg a 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>3. lehetőség: További partíció- vagy terjesztési kulcsok hozzáadása

Ahelyett, hogy csak _az államot_ használná partíciókulcsként, több kulcsot is használhat a particionáláshoz. Fontolja meg például _az irányítószám_ hozzáadása további partíciókulcsként az adatpartíciók méretének csökkentése és az adatok egyenletesebb elosztása érdekében.

### <a name="option-4-use-round-robin-distribution"></a>4. lehetőség: Ciklikus multiplexelés használata

Ha nem talál megfelelő kulcsot a partícióhoz és a terjesztéshez, megpróbálhatja használni a ciklikus multiplexelést. A ciklikus multiplexelés minden sort egyformán kezel, és véletlenszerűen a megfelelő gyűjtőkbe helyezi őket. Az adatok egyenletesen oszlanak el, de elveszítik a helyadatokat, ami olyan hátrány, amely egyes műveletek esetében is csökkentheti a feladat teljesítményét. Továbbá, ha a ferde kulcs összesítését végzi, az adatdöntési probléma továbbra is fennáll. A ciklikus multiplexelésről a CREATE TABLE (U-SQL) U-SQL table distributions (U-SQL) című témakörben olvashat [bővebben: Tábla létrehozása sémával.](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch)

## <a name="solution-2-improve-the-query-plan"></a>2. megoldás: A lekérdezési terv javítása

### <a name="option-1-use-the-create-statistics-statement"></a>1. lehetőség: A STATISZTIKA LÉTREHOZÁSA utasítás használata

Az U-SQL a TÁBLÁKHOZTA STATISZTIKA LÉTREHOZÁSA utasítást biztosítja. Ez az utasítás további információt ad a lekérdezés-optimalizálónak a táblában tárolt adatjellemzőkről, például az értékelosztásról. A legtöbb lekérdezés esetében a lekérdezésoptimalizáló már létrehozza a szükséges statisztikákat egy jó minőségű lekérdezési tervhez. Esetenként szükség lehet a lekérdezésteljesítmény javítására további statisztikák létrehozásával a STATISZTIKA LÉTREHOZÁSA segítségével, vagy a lekérdezés tervének módosításával. További információt a [STATISZTIKA (U-SQL) létrehozása](/u-sql/ddl/statistics/create-statistics) lapon talál.

Példa kód:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>A statisztikai adatok nem frissülnek automatikusan. Ha a statisztikai adatok újbóli létrehozása nélkül frissíti a tábla adatait, a lekérdezés teljesítménye csökkenhet.

### <a name="option-2-use-skewfactor"></a>2. lehetőség: Ferdetorzító használata

Ha az egyes államok adóját szeretné összegezni, akkor csoportonkénti állapotot kell használnia, amely nem kerüli el az adatdöntési problémát. A lekérdezésben azonban adattippet is megadhat a kulcsokban lévő adatdöntés azonosításához, hogy az optimalizáló végrehajtási tervet készíthessen elő Önnek.

Általában a paramétert 0,5 és 1-re állíthatja be, 0,5 jelentése nem sok döntés, 1 pedig nehéz döntés. Mivel a tipp hatással van az aktuális utasítás és az összes alsóbb rétegbeli utasítás végrehajtási tervoptimalizálására, mindenképpen adja hozzá a tippet a potenciális ferde kulcs-bölcs összesítés előtt.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Példa kód:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>3. lehetőség: ROWCOUNT használata  
A SKEWFACTOR mellett, adott ferde kulcsegyesítési esetekesetén, ha tudja, hogy a másik illesztett sorkészlet kicsi, az optimalizáló nak megmondhatja, ha a JOIN előtt egy ROWCOUNT emlékeztetőt ad hozzá az U-SQL utasításhoz. Ily módon az optimalizáló kiválaszthatja a közvetítési illesztési stratégiát a teljesítmény javítása érdekében. Ne feledje, hogy a ROWCOUNT nem oldja meg az adatdöntési problémát, de további segítséget nyújthat.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Példa kód:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>3. megoldás: A felhasználó által definiált szűkítő és összevonás javítása

Néha írhat egy felhasználó által definiált operátort a bonyolult folyamatlogikával való kapcsolatkezelésére, és egy jól megírt szűkítő és összevonás bizonyos esetekben enyhítheti az adatdöntési problémát.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>1. lehetőség: Ha lehetséges, használjon rekurzív szűkítőt

Alapértelmezés szerint a felhasználó által definiált szűkítő nem rekurzív módban fut, ami azt jelenti, hogy a kulcs munkamennyiségének csökkentése egyetlen csúcspontba kerül. De ha az adatok ferde, a hatalmas adatkészletek előfordulhat, hogy egyetlen csúcspont, és hosszú ideig fut.

A teljesítmény javítása érdekében hozzáadhat egy attribútumot a kódhoz, hogy definiálja a szűkítőt, hogy rekurzív módban fusson. Ezután a hatalmas adatkészletek több csúcsra is terjeszthetők, és párhuzamosan futtathatók, ami felgyorsítja a feladatot.

Ha egy nem rekurzív szűkítőt rekurzívra szeretne módosítani, győződjön meg arról, hogy az algoritmus asszociatív. Például az összeg asszociatív, a medián pedig nem. Azt is meg kell győződnie arról, hogy a bemeneti és kimeneti csökkentő tartani ugyanazt a sémát.

A rekurzív szűkítő attribútuma:

    [SqlUserDefinedReducer(IsRecursive = true)]

Példa kód:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>2. lehetőség: Ha lehetséges, használjon sorszintű combiner módot

Az egyes ferde kulcsillesztési esetekHEZ tartozó ROWCOUNT-tipphez hasonlóan a combiner mód hatalmas ferde kulcsérték-készleteket próbál elosztani több csúcsra, hogy a munka egyidejűleg végrehajtható legyen. A Combiner mód nem oldja meg az adatdöntési problémákat, de további segítséget nyújthat a hatalmas ferde kulcsérték-készletekhez.

Alapértelmezés szerint a combiner mód Teljes, ami azt jelenti, hogy a bal oldali és a jobb oldali sorkészlet nem választható el egymástól. Ha a módot balra/jobbra/belsőre állítja be, az lehetővé teszi a sorszintű illesztést. A rendszer elválasztja a megfelelő sorkészleteket, és több, párhuzamosan futó csúcsokra osztja el őket. A kombináló mód konfigurálása előtt azonban ügyeljen arra, hogy a megfelelő sorkészletek külön legyenek.

Az alábbi példa egy különálló bal oldali sorkészletet jelenít meg. Minden kimeneti sor balról egyetlen bemeneti sortól függ, és potenciálisan a jobb oldali összes sortól függ, azonos kulcsértékkel. Ha a combiner módot balra állítja be, a rendszer a hatalmas bal oldali sort kicsikre választja, és több csúcshoz rendeli őket.

![Combiner mód illusztrációja](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Ha rossz combiner módot állít be, a kombináció kevésbé hatékony, és az eredmények hibásak lehetnek.

A kombájnok üzemmód jellemzői:

- SqlUserDefinedCombiner(Mode=CombinerMode.Full): Minden kimeneti sor potenciálisan függ az összes bemeneti sor balról és jobbról ugyanazzal a kulcsértékkel.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Minden kimeneti sor balról egyetlen bemeneti sortól függ (és potenciálisan a jobb oldali összes sortól, ugyanazzal a kulcsértékkel).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Minden kimeneti sor egy jobb oldali bemeneti sortól függ (és potenciálisan a bal ról származó összes sortól ugyanazzal a kulcsértékkel).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Minden kimeneti sor egyetlen bemeneti sortól függ a bal és a jobb oldali azonos értékkel.

Példa kód:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
