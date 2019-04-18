---
title: Az Azure Data Lake Analytics U-SQL nyelv – első lépések
description: Ismerje meg az Azure Data Lake Analytics U-SQL nyelv az alapokat.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 9de5c7228944bd0448d9dfa833ef223140ccf0e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59488711"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>U-SQL-lel az Azure Data Lake Analytics használatának első lépései
U-SQL nyelv, amely egyesíti az SQL deklaratív imperatív C# lehetővé teszi, hogy minden olyan nagy mennyiségű adat feldolgozását. U-SQL skálázható, elosztott lekérdezési képességek segítségével hatékonyan elemezheti adatok például az Azure SQL Database relációs adattárak között. Az U-SQL strukturált adatok feldolgozására olvasási séma alkalmazásának, és egyéni logikát és felhasználói függvények szúr be. Továbbá a U-SQL tartalmazza, amely felkínálja részletesen szabályozhatja, hogyan hajtható végre, ipari méretekben bővíthetőséget. 

## <a name="learning-resources"></a>Tanulási források

* A [U-SQL-oktatóanyag](https://aka.ms/usqltutorial) nyelve a U-SQL legtöbb részletes bemutatóját tartalmazza. Ebben a dokumentumban ajánlott minden fejlesztő, aki a U-SQL további olvasása.
* További információk a **U-SQL nyelv szintaxisát**, tekintse meg a [U-SQL nyelvi referencia](https://go.microsoft.com/fwlink/p/?LinkId=691348).
* Megérteni a **U-SQL-Tervező filozófia**, blogbejegyzésből a Visual Studio [bemutatása U-SQL – A nyelvet, amely a big Data jellegű adatok feldolgozása egyszerűen](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Előfeltételek

Előtt ebben a dokumentumban a U-SQL-mintákat, olvassa el, és végezze el [oktatóanyag: A Data Lake Tools for Visual Studio használatával U-SQL-parancsfájlok fejlesztése a](data-lake-analytics-data-lake-tools-get-started.md). Az oktatóanyag azt ismerteti, hogy beállítás esetén a U-SQL az Azure Data Lake Tools for Visual Studio használatával.

## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

Az alábbi U-SQL parancsfájl egyszerű, és lehetővé teszi, hogy bizonyos szempontokból az U-SQL nyelv vizsgálatát.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Ez a szkript nem rendelkezik olyan Adatátalakítási lépéseket. A forrásfájl nevű olvas `SearchLog.tsv`schematizes, és a sorkészlet visszaírja SearchLog-first-u-sql.csv nevű fájlba.

Figyelje meg, hogy a kérdéses adatok mellett írja be a `Duration` mező. Azt jelenti, hogy a `Duration` mező lehet null értékű.

### <a name="key-concepts"></a>Fő fogalmak
* **Sorhalmaz változók**: Minden egyes lekérdezési kifejezés, amely egy sorhalmaz változóhoz rendelhető. A T-SQL változó elnevezési mintát követi, U-SQL (`@searchlog`, például) a szkriptben.
* A **KINYERÉSE** kulcsszó adatokat olvas be egy fájlt, és határozza meg, a séma olvasása. `Extractors.Tsv` van egy beépített U-SQL-információkinyerő lapon tagolt fájlok. Egyéni információkinyerők fejleszthet.
* A **kimeneti** sorhalmaz adatokat ír egy fájlba. `Outputters.Csv()` van egy beépített U-SQL outputter, hozzon létre egy vesszővel tagolt fájlt. Egyéni kiírókra fejleszthet.

### <a name="file-paths"></a>Fájlok elérési útja

A KINYERÉSI, és a kimeneti utasítást használja a Fájlelérési utak. Fájlok elérési útja lehet abszolút vagy relatív:

A következő fájl abszolút elérési út hivatkozik egy fájlt egy Data Lake Store nevű `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

A következő fájl elérési útja kezdődik `"/"`. Az alapértelmezett Data Lake Store-fiók egy fájl hivatkozik:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Skaláris változót használja.

Skaláris változókat is használhatja a parancsfájl karbantartási megkönnyítése. Az előző U-SQL parancsfájlt is írható mint:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Sorkészletek átalakítása

Használat **kiválasztása** sorkészletek átalakításához:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A WHERE záradékban használt egy [C# logikai kifejezés](/dotnet/csharp/language-reference/operators/index). A C# kifejezés nyelve segítségével hajtsa végre a saját kifejezések és függvények. A logikai kötőszavak (Equal) és disjunctions (ORs) kombinálásával összetettebb szűrés még akkor is elvégezheti.

A következő parancsfájl DateTime.Parse() módszer és a egy együtt használja.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >A második lekérdezés eredménye az első sorhalmaz, amely létrehozza a két szűrő összetett működik. Is felhasználhatja a változó neve, és a nevek betűrendbe is meghatározhat.

## <a name="aggregate-rowsets"></a>Összesített sorkészletek
U-SQL biztosítja a jól ismert ORDER BY, GROUP BY, és összesítést.

Az alábbi lekérdezés a teljes időtartam talál régiónként, és ezután megjeleníti az első öt időtartamok sorrendben.

U-SQL-sorkészletek nem őrzi meg a következő lekérdezés sorrendjét. Így egy output sorrendben szüksége ORDER BY záradék hozzáadása a kimeneti kivonat:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

A U-SQL ORDER BY záradék szükséges, jelölje be a kifejezésben a FETCH záradék használatával.

A U-SQL KELLENE záradékot a kimeneti korlátozhatja a csoportokat, amelyek megfelelnek a HAVING feltétel használható:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Speciális összesítési forgatókönyvek esetén tekintse meg a U-SQL dokumentációja [összesítéséhez, elemzési és hivatkozási függvények](/u-sql/built-in-functions)

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
