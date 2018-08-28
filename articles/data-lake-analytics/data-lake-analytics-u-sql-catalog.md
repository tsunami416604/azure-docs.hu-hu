---
title: A U-SQL katalógus az Azure Data Lake Analytics használatának első lépései
description: Ismerje meg a U-SQL katalógus használata a kód és az adatok megosztásához.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: 62f43fc082969bf04b7177725478585ce41aa347
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045938"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>A U-SQL katalógus az Azure Data Lake Analytics használatának első lépései

## <a name="create-a-tvf"></a>Hozzon létre egy TVF

A korábbi U-SQL parancsfájl ismétlődő azonos a forrás-fájlból olvassa a KIVONATOT használatát. A U-SQL táblázat értékű függvény (TVF) magába foglalja az adatok későbbi felhasználásra.  

A következő szkript létrehoz egy nevű TVF `Searchlog()` az alapértelmezett adatbázis és séma:

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

A következő parancsfájl bemutatja, hogyan használható a TVF az előző parancsfájlban megadott:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SerachLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Nézetek létrehozása

Ha a lekérdezés egyetlen, helyett egy TVF segítségével egy U-SQL-NÉZET a kifejezés magába foglalja.

A következő szkriptet hoz létre nevű nézete használható `SearchlogView` az alapértelmezett adatbázis és séma:

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

Az alábbi parancsfájl bemutatja, hogy az előre definiált nézet:

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
Relációs adatbázis-táblákkal, U-SQL-lel is hozzon létre egy táblát egy előre definiált sémával, vagy hozzon létre egy táblát, amely kikövetkezteti a használandó sémát a lekérdezésből, amelyek a tábla (más néven CREATE TABLE AS SELECT vagy a CTAS) tölti fel.

Hozzon létre egy adatbázis és a két táblázatot a következő parancsfájl használatával:

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
Lekérdezheti a táblázatok, például azokkal azonos módon, hogy lekérdezésfájlok az adatokat az előző parancsfájlban létrehozott. Sorhalmaz létrehozása a KINYERÉSI, helyett most már hivatkozhat a tábla neve.

Olvassa el a táblákból, módosítsa a átalakító szkript, amelyet korábban használt:

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
 >Jelenleg nem futtatható egy válassza ki ugyanazt a parancsprogramot, mint a táblán, ahol létrehozta a táblában.

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
