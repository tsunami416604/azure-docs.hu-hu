---
title: A U-SQL-katalógus használata Azure Data Lake Analytics
description: Megtudhatja, hogyan oszthatja meg a kódot és az információkat a U-SQL-katalógus használatával. Táblázat értékű függvények létrehozása, nézetek létrehozása, táblák létrehozása és lekérdezése.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672846"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Ismerkedés az U-SQL-katalógussal Azure Data Lake Analytics

## <a name="create-a-tvf"></a>TVF létrehozása

Az előző U-SQL-parancsfájlban megismétli, hogy a kinyerés ugyanazzal a forrásfájl használatával olvasson. Ha a U-SQL tábla értékű függvényt (TVF) használja, akkor a későbbiekben újra beágyazhatja az adategységeket.  

A következő szkript létrehoz egy nevű `Searchlog()` TVF az alapértelmezett adatbázisban és sémában:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
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
RETURN;
END;
```

Az alábbi szkript bemutatja, hogyan használhatja az előző parancsfájlban definiált TVF:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Nézetek létrehozása

Ha egyetlen lekérdezési kifejezéssel rendelkezik, a TVF helyett egy U-SQL-nézetet használhat a kifejezés beágyazásához.

A következő szkript létrehoz egy nevű `SearchlogView` nézetet az alapértelmezett adatbázisban és sémában:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

A következő parancsfájl a definiált nézet használatát mutatja be:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Táblák létrehozása
Csakúgy, mint a kapcsolódó adatbázistáblák esetében, az U-SQL használatával létrehozhat egy előre definiált sémával rendelkező táblázatot, vagy létrehozhat egy táblázatot, amely kikövetkezteti a sémát a táblát feltöltő lekérdezésből (más néven a SELECT vagy a CTAS CREATE TABLE).

Hozzon létre egy adatbázist és két táblázatot a következő parancsfájl használatával:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Táblák lekérdezése
Az adatfájlok lekérdezéséhez hasonló módon, például az előző szkriptben létrehozott táblákat is lekérdezheti. A sorhalmaz a kinyerés használatával történő létrehozása helyett a tábla nevére is hivatkozhat.

A táblákból való olvasáshoz módosítsa a korábban használt átalakítási parancsfájlt:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Jelenleg nem futtathat olyan táblát, amely ugyanabban a parancsfájlban van, mint ahol a táblázatot létrehozta.

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
