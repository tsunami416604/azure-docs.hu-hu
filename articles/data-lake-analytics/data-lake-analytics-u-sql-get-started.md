---
title: Ismerkedés a U-SQL nyelv |} Microsoft Docs
description: A U-SQL nyelvű alapismeretei.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: e7efdac8b032055b5344f34f5f9ad2679bddb107
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-u-sql"></a>Ismerkedés a U-SQL
U-SQL nyelven, hogy feltétlenül szükséges C#, így deklaratív SQL bármilyen léptékben adatok feldolgozása. U-SQL skálázható, elosztott lekérdezés funkció segítségével hatékonyan elemezheti adatok relációs áruházak, például az Azure SQL-adatbázis között. U-SQL, a strukturálatlan adatok feldolgozását olvassa el a séma alkalmazása és egyéni logika és a felhasználó által megadott függvények beszúrni. Ezenkívül a U-SQL, amely lehetővé teszi az részletes szabályozhatják, hogyan hajthat végre léptékű bővítési is. 

## <a name="learning-resources"></a>Képzési erőforrást

* A [U-SQL-oktatóanyag](http://aka.ms/usqltutorial) egy részletes útmutató a U-SQL nyelv legtöbb biztosít. Ez a dokumentum olvasása az összes, aki a U-SQL további ajánlott.
* További információk a **U-SQL nyelvi szintaxisát**, tekintse meg a [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* Megérteni a **U-SQL-Tervező alapvetően**, a Visual Studio blogbejegyzésből [bevezetéséről U-SQL – A nyelv, amely megkönnyíti, nagy adatfeldolgozási](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne ebben a dokumentumban a U-SQL minták keresztül, olvassa el, és végezze el [oktatóanyag: Data Lake Tools for Visual Studio használatával fejlesztése U-SQL-parancsfájlok](data-lake-analytics-data-lake-tools-get-started.md). Hogy az oktatóanyag azt ismerteti, beállítás esetén az Azure Data Lake Tools for Visual Studio U-SQL használatával.

## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

Az alábbi U-SQL parancsfájl egyszerű, és sok szempontból nyelve a U-SQL vizsgálatát teszi lehetővé.

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

Ez a parancsfájl nem rendelkezik olyan átalakítása lépéseket. Emellett beolvassa a forrásfájl neve is `SearchLog.tsv`, schematizes, és kiírja a sorhalmaz vissza SearchLog-first-u-sql.csv nevű fájlba.

Figyelje meg a kérdéses az adatok mellett írja be a `Duration` mező. Ez azt jelenti, hogy a `Duration` mezője null értékű lehet.

### <a name="key-concepts"></a>Fő fogalmak
* **A sorhalmaz változók**: minden lekérdezési kifejezés, amely létrehozza a sorhalmaz változó lehet hozzárendelni. U-SQL-T-SQL változó elnevezési mintát követi (`@searchlog`, például) a parancsfájlban.
* A **KIBONTÁSA** kulcsszó fájlból olvassa be az adatokat, és határozza meg, olvassa el a séma. `Extractors.Tsv` a beépített U-SQL kivonatoló lapon tagolt fájlok van. Egyéni vagyis fejleszthet.
* A **kimeneti** fájlba írja az adatokat a sorkészlet. `Outputters.Csv()` az egy beépített U-SQL outputter vesszővel tagolt fájl létrehozásához. Egyéni outputters fejleszthet.

### <a name="file-paths"></a>Fájlok elérési útja

A KIVONATOT, és a kimeneti utasítás elérési utat használja. Abszolút vagy relatív elérési útvonalat lehet:

A következő fájl abszolút elérési út egy-egy Data Lake Store nevű fájlra hivatkozik `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

A következő fájl elérési útját kezdődik-e `"/"`. Az alapértelmezett Data Lake Store-fiók egy fájlra vonatkozik:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Skaláris változót használja.

Skaláris változót is segítségével egyszerűbbé teszik a parancsfájl karbantartási. Az előző U-SQL-parancsfájlt is írhatók mint:

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

## <a name="transform-rowsets"></a>Átalakítás sorkészletek

Használjon **válasszon** sorkészletek átalakítására:

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

A WHERE záradékban használ egy [C# logikai kifejezés](https://msdn.microsoft.com/library/6a71f45d.aspx). A C# kifejezés nyelv segítségével a saját kifejezések és a funkciók. Összetettebb szűrés alkalmazásával logikai kötőszavak (jelölőnégyzetből) és disjunctions (ORs) még akkor is végrehajtható.

Az alábbi parancsfájl a DateTime.Parse() metódus és a együtt használja.

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
 >A második lekérdezés eredménye az első sorhalmaz, és létrehozza a két szűrő összetett működik. Is felhasználhatja a változó nevét, és a nevek lexically hatóköre.

## <a name="aggregate-rowsets"></a>Összesített sorkészletek
U-SQL lehetővé teszi az ismerős ORDER BY, GROUP BY és összesítések.

A következő lekérdezés a teljes időtartam talál régiónként, és ezután jeleníti meg az első öt időtartamok sorrendben.

U-SQL sorkészletek nem őrzi meg a következő lekérdezés sorrendjét. Így egy output sorrendben kell ORDER BY hozzáadása a kimeneti kivonat:

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

A U-SQL ORDER BY záradék van szükség a FETCH záradék használatával válassza ki a kifejezésben.

A U-SQL rendelkező záradékot a kimeneti korlátozása csoportokat, amelyek a HAVING feltételnek megfelelő használható:

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

Speciális összesítő forgatókönyvek esetén dokumentációjában az U-SQL referencia [összesíteni, elemzési, és hivatkozzon funkciók](https://msdn.microsoft.com/library/azure/mt621335.aspx)

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
