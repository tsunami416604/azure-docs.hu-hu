---
title: Ismerkedés az U-SQL nyelvvel az Azure Data Lake Analytics-ben
description: Ismerje meg az U-SQL nyelv alapjait az Azure Data Lake Analytics ben. Írja meg az első lekérdezést változókkal a fájlokból származó további adatokhoz, alakítsa át a sorhalmazt és összesítse az adatokat.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672825"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Ismerkedés az U-SQL-rel az Azure Data Lake Analytics szolgáltatásban
Az U-SQL egy olyan nyelv, amely egyesíti a deklaratív SQL-t a c# elengedhetetlennel, hogy bármilyen méretű adatokat feldolgozhasson. Az U-SQL méretezhető, elosztott lekérdezési képességén keresztül hatékonyan elemezheti az adatokat a relációs tárolók, például az Azure SQL Database között. Az U-SQL segítségével strukturálatlan adatokat dolgozhat fel séma alkalmazásával az olvasásra, és egyéni logika és UDF-ek beszúrásával. Emellett az U-SQL bővíthetőséget is tartalmaz, amely részletesen szabályozhatja, hogyan hajthatja végre nagy méretekben. 

## <a name="learning-resources"></a>Tanulási források

* Az [U-SQL oktatóanyag](https://aka.ms/usqltutorial) az U-SQL nyelv nagy részének részletes áttekintését biztosítja. Ez a dokumentum ajánlott olvasás minden fejlesztő szeretnének tanulni U-SQL.
* Az **U-SQL nyelv szintaxisáról**az [U-SQL nyelvi útmutatóban](https://docs.microsoft.com/u-sql/)talál részletes információt.
* Az **U-SQL tervezési filozófiájának**megértéséhez tekintse meg a Visual Studio ["Introducing U-SQL – A Language that makes Big Data Processing Easy .](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt végigmenne a jelen dokumentumban található U-SQL-mintákon, olvassa el és fejezze be [az Oktatóanyagot: U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával.](data-lake-analytics-data-lake-tools-get-started.md) Ez az oktatóanyag bemutatja az U-SQL használatának mechanikáját az Azure Data Lake Tools for Visual Studio alkalmazással.

## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

A következő U-SQL szkript egyszerű, és lehetővé teszi számunkra, hogy vizsgálja meg számos szempontból az U-SQL nyelvet.

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

Ez a parancsfájl nem rendelkezik átalakítási lépéseket. A forrásfájlból `SearchLog.tsv`olvas, schematizes, és visszaírja a sorhalmazt egy SearchLog-first-u-sql.csv nevű fájlba.

Figyelje meg a `Duration` mezőben lévő adattípus melletti kérdőjelet. Ez azt `Duration` jelenti, hogy a mező null lehet.

### <a name="key-concepts"></a>Fő fogalmak
* **Sorhalmaz-változók**: Minden sorhalmazt eredményező lekérdezési kifejezés hozzárendelhető egy változóhoz. Az U-SQL a T-SQL változóelnevezési mintát (például)`@searchlog`követi a parancsfájlban.
* A **KIVONÁS** kulcsszó adatokat olvas be egy fájlból, és meghatározza a sémát olvasáskor. `Extractors.Tsv`egy beépített U-SQL elszívó a tabulátorral tagolt méretű fájlokhoz. Egyedi elszívók is kifejleszthetők.
* A **OUTPUT** adatokat ír egy sorhalmazból egy fájlba. `Outputters.Csv()`egy beépített U-SQL outputter, amely vesszővel tagolt értékkel bíró fájlt hoz létre. Egyéni outputtereket fejleszthet.

### <a name="file-paths"></a>Fájl elérési útjai

A KIVONÁS és a OUTPUT utasítások fájlelérési utakat használnak. A fájlelérési utak lehetnek abszolútak vagy relatívek:

Ez a következő abszolút fájlelérési út egy Data `mystore`Lake Store nevű fájlra hivatkozik:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

A következő fájlelérési `"/"`út a következővel kezdődik. Az alapértelmezett Data Lake Store-fiókban lévő fájlra hivatkozik:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Skaláris változók használata

Skaláris változók at is használhat a parancsfájl karbantartásának megkönnyítése érdekében. Az előző U-SQL szkript is írható, mint:

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

Sorkészletek átalakítása a **SELECT** segítségével:

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

A WHERE záradék [C# logikai kifejezést](/dotnet/csharp/language-reference/operators/index)használ. Használhatja a C# kifejezés nyelve, hogy a saját kifejezések és függvények. Akár összetettebb szűrést is végrehajthat, ha logikai kötőszavakkal (AND- ek) és letiltásokkal (Legkülső régiókkal) kombinálja őket.

A következő parancsfájl a DateTime.Parse() metódust és egy kötőegységet használ.

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
 >A második lekérdezés az első sorhalmaz eredménye alapján működik, amely létrehozza a két szűrő összetettét. A változónevét újra felhasználhatja, és a nevek hatóköre lexikálisan történik.

## <a name="aggregate-rowsets"></a>Összesítési sorkészletek
U-SQL ad a jól ismert ORDER BY, GROUP BY, és összesítések.

A következő lekérdezés megkeresi a régiónkénti teljes időtartamot, majd sorrendben jeleníti meg az első öt időtartamot.

Az U-SQL sorkészletek nem őrzik meg a sorrendjüket a következő lekérdezéshez. Így a kimenet megrendeléséhez hozzá kell adnia az ORDER BY értéket a OUTPUT utasításhoz:

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

Az U-SQL ORDER BY záradék hoz a FETCH záradékot a SELECT kifejezésben.

Az U-SQL HAVING záradék a kimenetet olyan csoportokra korlátozhatja, amelyek megfelelnek a HAVING feltételnek:

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

A speciális aggregációs forgatókönyveket az U-SQL referenciadokumentációban talál az [összesítő, analitikus és referenciafüggvényekhez.](/u-sql/built-in-functions)

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-szkriptek fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
