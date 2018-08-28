---
title: Azure Data Lake Tools for Visual Studio használatával – Adateltérés problémák megoldásához
description: A lehetséges megoldások Adateltérés problémák hibaelhárítása Azure Data Lake Tools for Visual Studio használatával.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: b3079a7f2e71e26164d96cf167b67f1a60f7a23b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046473"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Azure Data Lake Tools for Visual Studio használatával – Adateltérés problémák megoldásához

## <a name="what-is-data-skew"></a>Mi az adatok tevékenységdiagramon?

Röviden hangsúlyoztuk, Adateltérés érték túlterhelt kezeli őket. Tegyük fel, hogy rendelt-e az 50 adó elbírálók adó értéket ad vissza, az egyes amerikai állami egy vizsgálónak naplózását. A Wyoming vizsgálónak az ott a lakosság kicsi, mert rendelkezik ehhez kevés. California azonban az elbírálók marad túlzottan nagy államnépesség miatt.
    ![Probléma Adateltérés – példa](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

Ebben az esetben az adatok egyenetlenül oszlanak el között minden adózási elbírálók, ami azt jelenti, hogy néhány elbírálók több, mint a többi kell-e működni. Saját feladat Itt a adó-vizsgálónak példához hasonló helyzeteket gyakran előfordul. További műszaki feltételeket egy-egy csúcsának lekérdezi a társaknak sokkal több adatot, és olyan helyzet, amely lehetővé teszi több, mint a többi és a munka, végül a csúcspont lelassítja a teljes feladat. Mi az rosszabb, a feladat lehet, hogy sikertelen lesz, mert csúcspontok lehet, például egy 5 órás futásidejű korlátozás és a egy 6 GB memória korlátozás.

## <a name="resolving-data-skew-problems"></a>Adateltérés problémák megoldásához

Az Azure Data Lake Tools for Visual Studio segítségével, hogy a feladat rendelkezik-e Adateltérés probléma észlelése. Ha a probléma fennáll, feloldhatja úgy, ebben a szakaszban a megoldások.

## <a name="solution-1-improve-table-partitioning"></a>1. megoldás: A tábla particionálásával javítása

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>1. lehetőség: Előre szűrni az egyenetlen kulcs értéke

Nincs hatással az üzleti logikát, ha előre szűrheti a nagyobb gyakoriságot értékeket. Például ha nagy mennyiségű 000 000-000 GUID azonosító oszlop, nem érdemes ezt az értéket összesíteni. Mielőtt összesített, írhat "WHERE GUID! ="000 000-000"" szűrése a nagy gyakoriságú érték.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>2. lehetőség: Válasszon egy másik partíció vagy terjesztési kulcs

Az előző példában ha azt szeretné, csak a adó-naplózási munkaterhelés bármely országban, ellenőrizze, növelheti az adatok terjesztési a kulcsként a azonosítószámát kiválasztásával. Egy másik partíció vagy terjesztési kulcs kiadási is egyes esetekben az adatokat egyenletesebben osszák, de győződjön meg arról, hogy ez a választás nem érinti az üzleti logikát kell. Például az adó összege az egyes állapotokhoz kiszámításához, érdemes kijelölni _állapot_ partíciókulcsként. Ha a probléma továbbra is, próbálja meg a beállítást 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>3. lehetőség: A több partícióra vagy terjesztési kulcsainak hozzáadása

Csak helyett _állapot_ partíciókulcsként, particionálási egynél több kulcs is használja. Például érdemes lehet hozzáadni _irányítószám_ , egy további partíciókulcsot az adatok partícióméretei csökkentéséhez, és az adatokat egyenletesebben osszák.

### <a name="option-4-use-round-robin-distribution"></a>4. lehetőség: Ciklikus multiplexelés használata

Ha nem találja a megfelelő kulcs partíciót és-terjesztési, megpróbálhatja ciklikus multiplexelés használja. Ciklikus multiplexelés egyaránt kezeli az összes sort, és véletlenszerűen visszaállítja azokat megfelelő gyűjtőkbe. Az adatok egyenletesen lekérdezi, de helye információ egy hátránya, hogy bizonyos műveletek teljesítményének feladat is csökkentheti az elveszíti. Emellett ha mégis végez összesítést az egyenetlen kulcs, a Adateltérés probléma addig megmarad. Ciklikus multiplexelés kapcsolatos további információkért lásd: a U-SQL-tábla Disztribúciók szakaszában [CREATE TABLE (U-SQL): egy tábla létrehozása sémával](https://msdn.microsoft.com/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>2. megoldás: A lekérdezésterv javítása

### <a name="option-1-use-the-create-statistics-statement"></a>1. lehetőség: A CREATE STATISTICS utasítás használható.

U-SQL-táblák a CREATE STATISTICS utasítás nyújt. A jelen nyilatkozat a lekérdezésoptimalizáló az adatjellemzők, értékeloszlásának, például egy táblában tárolt kapcsolatos további információkat biztosít. A legtöbb lekérdezések esetén a lekérdezésoptimalizáló már magas színvonalú lekérdezésterv szükséges statisztikái állít elő. Bizonyos esetekben szüksége lehet javíthatja a lekérdezések teljesítményét, a CREATE STATISTICS statisztikák létrehozása vagy a lekérdezés tervezési módosításával. További információkért lásd: a [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/library/azure/mt771898.aspx) lapot.

Példa:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statisztikai adatok nem frissül automatikusan. Ha frissíti a táblában lévő adatokat a statisztikai adatok újbóli létrehozása nélkül, a lekérdezési teljesítmény előfordulhat, hogy elutasítja.

### <a name="option-2-use-skewfactor"></a>2. lehetőség: SKEWFACTOR használata

Ha szeretné az egyes állapotokhoz adó sum, GROUP BY állapotban, amely nem a Adateltérés probléma elkerülése érdekében kell használnia. Azonban a lekérdezési kulcsok Adateltérés azonosításához, hogy az optimalizáló készítheti elő az Ön számára egy végrehajtási terv adatok tippként is biztosít.

A paraméter állítja általában 0,5-1, azaz nem sok torzulása és 1 jelentése nagy döntés 0,5. A mutató végrehajtási terv optimalizálása az aktuális utasítás és az összes alárendelt utasítást érinti, mert mindenképpen adja hozzá a mutatót a lehetséges torzítja key-wise összesítés előtt.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Példa:

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

### <a name="option-3-use-rowcount"></a>3. lehetőség: Használja a sorok száma  
Mellett SKEWFACTOR olyan esetekben, adott torzítja kulcs való csatlakozás, ha tudja, hogy a többi csatlakoztatott sor rendkívül kicsi, beállíthatja, hogy az optimalizáló hozzáadásával egy sorszám érhető el a U-SQL-utasítással való csatlakozás előtt. Ezzel a módszerrel optimalizáló választhat egy szórási illesztési stratégiát a teljesítmény javítása érdekében. Vegye figyelembe, hogy a sorok száma nem oldja meg a Adateltérés probléma, de néhány további segítségért kínál.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Példa:

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>3. megoldás: A felhasználó által definiált nyomáscsökkentő és egyesítő javítása

Néha írhat egy felhasználó által definiált operátor összetett logikát foglalkozni, és a egy jól megírt nyomáscsökkentő és egyesítő Adateltérés probléma bizonyos esetekben előfordulhat, hogy csökkentése.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>1. lehetőség: Egy rekurzív nyomáscsökkentő lehetőség használata

Alapértelmezés szerint a felhasználó által definiált nyomáscsökkentő futtatja, ez azt jelenti, csökkentheti a munkahelyi kulcs egyetlen csúcs van elosztva a nem rekurzív módban. Azonban ha az adatok torzítja van, a hatalmas adatkészletek feldolgozása egyetlen csúcs előfordulhat, hogy, és hosszú ideig fussanak.

A teljesítmény javítása érdekében hozzáadhat egy attribútum meghatározásához nyomáscsökkentő rekurzív módban fusson a kódban. Ezután a hatalmas adatkészletek ossza el több csúcspontok is, és felgyorsítja a feladatok párhuzamosan futtathatók.

A nem rekurzív nyomáscsökkentő rekurzív módosításához győződjön meg arról, hogy az algoritmus társuló kell. Például a sum társuló, pedig a középérték nem. Szükség is győződjön meg arról, hogy a bemeneti és kimeneti nyomáscsökkentő a tartsa ugyanazzal a sémával.

A rekurzív nyomáscsökkentő attribútuma:

    [SqlUserDefinedReducer(IsRecursive = true)]

Példa:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>2. lehetőség: Használja a sorszintű egyesítő módot, ha lehetséges

A ROWCOUNT mutató adott torzítja kulcs illesztési esetek hasonlóan egyesítő mód megpróbálja terjeszteni több csúcspontok hatalmas torzítja-kulcs-érték beállítása, hogy a munka párhuzamosan futtatható. Egyesítő mód nem oldható fel Adateltérés problémák, de hatalmas torzítja-kulcs-érték csoportokkal kapcsolatos további segítség kínál.

Alapértelmezés szerint a egyesítő mód a teljes, ami azt jelenti, hogy a sor bal és jobb oldali sor nem kell elválasztani. Sorszintű illesztés bal és jobb/belső, az mód beállítása lehetővé teszi. A rendszer osztja szét a megfelelő sor készletek, és be több, párhuzamosan futó csúcspontok osztja el azokat. Azonban mielőtt konfigurálná a egyesítő módot, ügyeljen arra, hogy győződjön meg arról, hogy a megfelelő sor készletek választhatók el egymástól.

A következő példában mutat be elválasztott bal oldali sor. Minden kimeneti sor bal egyetlen bemeneti sor függ, és potenciálisan függ a közvetlenül az egyazon értékkel rendelkező összes sorát. Balra lehet beállítani a egyesítő mód, ha a rendszer történő kis a eszközök hatalmas bal sor osztja szét, és hozzárendeli azokat több csúcsokat.

![Egyesítő mód ábra](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Ha a nem megfelelő egyesítő módot, együttes használata kevésbé hatékony, és az eredmények hiba lehet.

Attribútumok egyesítő mód:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Minden kimeneti sor a bal oldalon (és ugyanazt a kulcsérték-jobb oldalról potenciálisan minden sort) egyetlen bemeneti sor függ.

- qlUserDefinedCombiner(Mode=CombinerMode.Right): minden kimeneti sor függ a jobb oldalon (és ugyanazt a kulcsérték a bal oldali potenciálisan minden sort) egyetlen bemeneti sor.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Minden kimeneti sor bal és jobb ugyanazzal az értékkel egyetlen bemeneti sor függ.

Példa:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
