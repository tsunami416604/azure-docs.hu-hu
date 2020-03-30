---
title: Az U-SQL katalógus használata az Azure Data Lake Analytics ben
description: Ismerje meg, hogyan használhatja az U-SQL katalógust a kód és az adatok megosztására. Táblaértékű függvényeket hozhat létre, nézeteket hozhat létre, táblákat hozhat létre és kérdezheti le őket.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672846"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Ismerkedés az U-SQL katalógussal az Azure Data Lake Analytics szolgáltatásban

## <a name="create-a-tvf"></a>TVF létrehozása

Az előző U-SQL parancsfájlban megismételte az EXTRACT használatát, hogy ugyanabból a forrásfájlból olvashassa. Az U-SQL tábla értékű funkcióval (TVF) beágyazhatja az adatokat a későbbi újrafelhasználáshoz.  

A következő parancsfájl létrehoz `Searchlog()` egy TVF-et, amelyet az alapértelmezett adatbázisban és sémában hívnak meg:

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

A következő parancsfájl bemutatja, hogyan használhatja az előző parancsfájlban definiált TVF-et:

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

Ha egyetlen lekérdezési kifejezéssel rendelkezik, tvf helyett u-SQL NÉZETet használhat a kifejezés beágyazására.

A következő parancsfájl létrehoz `SearchlogView` egy nézetet az alapértelmezett adatbázisban és sémában:

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

## <a name="create-tables"></a>Táblázatok létrehozása
A relációs adatbázistáblákhoz ugyanúgy létrehozhat egy előre definiált sémát tartalmazó táblát, vagy létrehozhat egy olyan táblát, amely a táblát feltöltő lekérdezésből (más néven CREATE TABLE AS SELECT vagy CTAS) következtet.

Hozzon létre egy adatbázist és két táblát a következő parancsfájl használatával:

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
A táblákat ugyanúgy lekérdezheti, például az előző parancsfájlban létrehozottakat, mint az adatfájlokat. Ahelyett, hogy a KIVONÁS használatával hozna létre sorhalmazt, most már hivatkozhat a tábla nevére.

Ha a táblákból szeretne olvasni, módosítsa a korábban használt átalakító parancsfájlt:

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
 >Jelenleg nem futtathat SELECT-et ugyanabban a parancsfájlban, amelyben a táblát létrehozta.

## <a name="next-steps"></a>Következő lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
