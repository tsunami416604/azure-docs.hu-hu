---
title: "Adatok-döntés teljesítése Azure Data Lake Tools for Visual Studio használatával |} Microsoft Docs"
description: "Hibaelhárítás az adatok-döntés problémák lehetséges megoldások Azure Data Lake Tools for Visual Studio használatával."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
ms.openlocfilehash: 9b284ef33be4b935569fc368d81ddf040b2c2b7d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Adatok-döntés teljesítése Azure Data Lake Tools for Visual Studio használatával

## <a name="what-is-data-skew"></a>Mi az az adatok döntés?

Rövid időre is hangsúlyoztuk, adatok döntés érték túlzott kezeli őket. Tegyük fel, hogy 50 adó elméleti adó értéket ad vissza, az egyes USA állapotához egy vizsgáztatónak naplózandó rendelt-e. A Wyoming vizsgáztatónak az nincs a feltöltési kicsi, mert rendelkezik kevés elvégzéséhez. Kaliforniai azonban a vizsgáztatónak tartják túlzottan megnő a szerinti nagy feltöltési miatt.
    ![Adatok-döntés probléma – példa](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

A mi esetünkben az adatok nem egyenlően oszlik el minden adó elméleti, ami azt jelenti, hogy néhány elméleti több, mint a többire kell-e működni. Saját feladat olyan helyzetekben, például a adó-vizsgáztatónak példa itt gyakran előfordul. További technikai értelemben egy-egy csúcsának lekérdezi a társaknak mint sokkal több adatot, olyan helyzet, amely lehetővé teszi a több, mint a többi és, hogy végül működik csúcspont lelassítja az egy teljes feladat. Mi az rosszabb, a feladat lehet, hogy sikertelen lesz, mert csúcsban lehet, például egy 5 órás futásidejű korlátozás és a memória 6 GB-os korlátozását.

## <a name="resolving-data-skew-problems"></a>Adatok-döntés problémák megoldásához

Az Azure Data Lake Tools for Visual Studio segítségével észleli, hogy a feladat rendelkezik-e az adatok-döntés probléma. Ha a probléma fennáll, hogyan oldható meg úgy a megoldások ebben a szakaszban.

## <a name="solution-1-improve-table-partitioning"></a>1. megoldás: A táblaparticionálást javítása

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>1. lehetőség: A kihasználtságot kulcsérték előre szűrése

Nincs hatással az üzleti logikát, a nagyobb gyakoriságot értékek előre végezhet. Például ha nagy mennyiségű 000 000-000 oszlopban GUID, előfordulhat, hogy nem kívánt ezt az értéket összesítő. Mielőtt összesített, írhat "WHERE GUID! ="000 000-000"" szűrése nagyon gyakori értékét.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>2. lehetőség: Válasszon egy másik partíció vagy terjesztési kulcs

Az előző példában ha csak ellenőrizni kívánja az ország, amelynek a adó-naplózási munkaterhelés javíthatja az adatok terjesztési azonosítója számára kattintva a kulcsként. Egy másik partíció vagy a terjesztési kulcs kiadási is néha az adatok több egyenletes elosztása, de meg kell győződnie arról, hogy ez a beállítás nincs hatással az üzleti logikát. Például az egyes adó összeg kiszámításához, érdemes kijelölni _állapot_ partíciókulcsnak. Ha továbbra is ebbe a problémába ütközik, próbálja meg a beállítás 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>3. lehetőség: Több partíció vagy terjesztési kulcsainak hozzáadása

Csak helyett _állapot_ partíciókulcsként, használhat több kulcs particionálást. Például, vegyen fel _irányítószám_ csökkentése adatok-partíció méretét, és az adatok több egyenletes elosztása további partíció kulcsként.

### <a name="option-4-use-round-robin-distribution"></a>4. lehetőség: Ciklikus multiplexelés használata

Ha nem találja a megfelelő kulcs partíció és terjesztési, megpróbálhatja ciklikus multiplexelés használandó. Ciklikus multiplexelés egyaránt kezeli az összes sort, és véletlenszerűen megfelelő gyűjtők elhelyezi azokat. Az adatok beolvasása egyenletesen, de helység információ, amely az egyes műveletek esetében a feladat teljesítmény is csökkentheti visszatérítési elveszíti. Emellett akkor használatos, ha a kihasználtságot kulcs összesítési ennek ellenére is, az adatok-döntés probléma áll fenn. Ciklikus multiplexelés kapcsolatos további tudnivalókért tekintse meg a U-SQL táblázat azokat a Terjesztéseket részt [CREATE TABLE (U-SQL): a táblázatok létrehozásáról az a séma](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>2. megoldás: A lekérdezésterv javítása

### <a name="option-1-use-the-create-statistics-statement"></a>1. lehetőség: A CREATE statistics UTASÍTÁSHOZ utasítás használható.

U-SQL-táblák a CREATE statistics UTASÍTÁSHOZ utasítás biztosít. A jelen nyilatkozat ad a lekérdezésoptimalizáló a adatjellemzők, többek között a terjesztési érték, a táblában tárolt kapcsolatos további információt. A legtöbb lekérdezésnél a lekérdezésoptimalizáló már jó minőségű lekérdezéstervet szükséges statisztikai adatait állít elő. Alkalmanként szükség lehet további statisztikák létrehozása a CREATE statistics UTASÍTÁSHOZ vagy a lekérdezés tervezési módosításával javíthatja a lekérdezések teljesítményét. További információkért lásd: a [CREATE statistics UTASÍTÁSHOZ (U-SQL)](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx) lap.

Példa:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statisztikai adatok nem frissülnek automatikusan. Ha frissíti a táblákban tárolt adatokat nélkül hozza létre újra a statisztikai adatokat, a lekérdezési teljesítmény előfordulhat, hogy elutasítja.

### <a name="option-2-use-skewfactor"></a>2. lehetőség: SKEWFACTOR használata

Ha szeretné az egyes adó sum, a GROUP BY állapot, egy módszer, amely nem az adat-döntés probléma elkerülése érdekében kell használnia. Adatok javaslat azonban biztosíthat adatok döntés kulcsok azonosítására, hogy a optimalizáló előkészíti végrehajtási tervének meg a lekérdezésben.

A paraméter állítja általában 0,5-1, tehát nem kap nagy eltérésére és 1 jelentése nagy döntés 0,5. Mivel a mutató hatással van az aktuális utasítás és az összes alárendelt utasítást végrehajtási terv optimalizálást, ügyeljen arra, hogy a mutató hozzáadása előtt a lehetséges válik egyenetlenné key-wise összesítési.

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

### <a name="option-3-use-rowcount"></a>3. lehetőség: ROWCOUNT használata  
Mellett SKEWFACTOR az adott válik egyenetlenné kulcs illesztési esetben, hogy a többi összekapcsolt sorkészlet kicsi, ha megadható, hogy a optimalizáló ROWCOUNT mutató hozzáadásával a ILLESZTÉS előtt U-SQL-utasításban. Ezzel a módszerrel optimalizáló választható szórási illesztés stratégiáról a teljesítmény növelése érdekében. Ne feledje, hogy sorszám nem oldja meg az adatok-döntés problémát, de néhány további segítséget kínál.

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

Néha írhat egy operátor felhasználó által definiált összetett folyamat logika kezelésére, és egy jól megírt nyomáscsökkentő és egyesítő adatok-döntés probléma bizonyos esetekben előfordulhat, hogy mérsékelni.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>1. lehetőség: Egy rekurzív nyomáscsökkentő, ha lehetséges használata

Alapértelmezés szerint a felhasználó által definiált nyomáscsökkentő fut nem rekurzív módban, ami azt jelenti, amelyekkel csökkenthető a kulcsok munkahelyi egyetlen csúcspont van elosztva. Azonban az adatok válik egyenetlenné, ha nagyon nagy adatkészletek egyetlen csúcspont feldolgozása előfordulhat, hogy, és hosszú ideig fussanak.

A teljesítmény javítása érdekében a kódot, amely meghatározza a rekurzív módban való futásra nyomáscsökkentő attribútum adhat hozzá. Ezt követően a nagyon nagy adatkészletek több csúcsban terjesztése is, és felgyorsítja a feladat párhuzamosan futnak.

Módosítsa a nem rekurzív nyomáscsökkentő rekurzív, győződjön meg arról, hogy a algoritmus társuló kell. Például a sum társuló, pedig a középérték nem. Szükség győződjön meg arról, hogy a bemeneti és kimeneti nyomáscsökkentő megtartja-e az azonos sémából.

A rekurzív nyomáscsökkentő attribútum:

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

A sorszám mutatót adott válik egyenetlenné kulcs illesztési adódó hasonló, egyesítő mód megpróbálja terjesztheti a több csúcsban hatalmas válik egyenetlenné kulcs-érték beállítása, hogy a munka párhuzamosan futtatható. Egyesítő mód nem oldható fel az adatok-döntés problémákat, de felajánlhat hatalmas válik egyenetlenné kulcs-érték beállítása további segítséget.

Alapértelmezés szerint a egyesítő módja teljes, ami azt jelenti, hogy a sor bal és jobb oldali sor készlet nem kell elválasztani. A mód beállítása balra vagy jobbra vagy belső, lehetővé teszi, hogy a sorszintű illesztési. A rendszer a megfelelő sor készletek elválasztja, és továbbítja őket a párhuzamosan futó több csúcsban. Azonban a egyesítő mód konfigurálása előtt ügyeljen arra, hogy győződjön meg arról, hogy a megfelelő sor készletek választhatók el egymástól.

Az alábbi példában elkülönített bal sorkészlet. Minden kimeneti sor bal oldali bemeneti egyetlen sor függ, és potenciálisan attól függ, a jobb oldali azonos kulcsértékkel rendelkező összes sorát. Ha balra egyesítő módja annak beállítása, a rendszer elválasztja a hatalmas balra soron kívüli kis megfelelően beállítani, és hozzárendeli több csúcsban.

![Egyesítő mód ábra](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Ha rossz egyesítő módja annak beállítása, a kombinált kevésbé hatékony, és lehet, hogy az eredmények még.

A egyesítő mód attribútumok:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Minden kimeneti sor egyetlen bemeneti sor balra (és a jobb oldali kulcs azonos értékű potenciálisan összes sorát) függ.

- qlUserDefinedCombiner(Mode=CombinerMode.Right): minden kimeneti sor egyetlen bemeneti sor jobb (és a bal oldali kulcs azonos értékű potenciálisan összes sorát) függ.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Minden kimeneti sor a bal és jobb azonos értékű egyetlen bemeneti sor függ.

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
