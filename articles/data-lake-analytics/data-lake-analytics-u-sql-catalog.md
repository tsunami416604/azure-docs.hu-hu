---
title: Az Azure Data Lake Analytics U-SQL katalógus az első lépései
description: Útmutató a U-SQL-katalógus használatáról az kódot és az adatok megosztását.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: saveenr
editor: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: 35a39733987eba7060049db7005c1f6bc0058e63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624352"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics U-SQL katalógus az első lépései

## <a name="create-a-tvf"></a>Hozzon létre egy TVF

Az előző U-SQL parancsfájl megismételni azonos a forrás-fájlból olvassa a KIVONATOT használatát. A U-SQL táblázat értékű függvény (TVF) az adatokat jövőbeni használatra foglalják magukban.  

Az alábbi parancsfájlt hoz létre egy TVF nevű `Searchlog()` az alapértelmezett adatbázis és a sémában:

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

Az alábbi parancsfájl bemutatja, hogyan használja a TVF, amely az előző parancsfájl lett definiálva:

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

Ha egy lekérdezési kifejezésben, a TVF helyett segítségével egy U-SQL-NÉZET foglalják magukban az adott kifejezés.

A következő parancsfájl egy nevű nézetet hoz létre `SearchlogView` az alapértelmezett adatbázis és a sémában:

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

Az alábbi parancsfájl bemutatja, hogy a meghatározott nézet:

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
Relációs adatbázis táblákkal, az U-SQL is hozzon létre egy táblát egy előre meghatározott séma vagy hozzon létre egy táblát, amely kikövetkezteti a séma a lekérdezésből, amely megadja a tábla (más néven CREATE TABLE AS SELECT vagy CTAS).

Az adatbázis és a két tábla létrehozása az alábbi parancsfájl használatával:

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

## <a name="query-tables"></a>Lekérdezés táblák
Táblák, például az előző parancsfájl kérdezze le az adatforrás adatfájljainak azonos módon létrehozott kérdezheti le. KIVONAT használatával hoz létre egy sorhalmaz, helyett most hivatkozhat a tábla neve.

Olvassa el a táblát, módosítsa az átalakítás parancsfájlt, amely korábban használt:

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
 >Jelenleg nem futtatható a válassza ki ugyanazt a parancsfájlt a táblához hozta létre a tábla.

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
