---
title: A Python, az Azure Data Lake Analytics U-SQL szkriptek kiterjesztése
description: Ismerje meg, hogyan használata Azure Data Lake Analytics U-SQL-parancsfájlok a Python-kód futtatása
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: b2179f9d81a2dad877c8ae58471f7440eb9edbe7
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050991"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>A Python-kód az Azure Data Lake Analytics U-SQL-szkriptek kiterjesztése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, a Python-bővítmények telepítve az Azure Data Lake Analytics-fiókban.

* Keresse meg azt a Data Lake Analytics-fiók az Azure Portalon
* A bal oldali menü alatt **bevezetés** kattintson a **Mintaszkriptek**
* Kattintson a **U-SQL-bővítmények telepítése** majd **OK**

## <a name="overview"></a>Áttekintés 

U-SQL Python-bővítmények lehetővé teszi a fejlesztőknek, a Python-kód nagymértékben párhuzamos végrehajtása végrehajtásához. Az alábbi példa az alapvető lépéseit mutatja be:

* Használja a `REFERENCE ASSEMBLY` utasítás Python-bővítmények engedélyezéséhez a U-SQL parancsfájl
* Használatával a `REDUCE` művelet a bemeneti adatokat a kulcs
* A U-SQL Python-bővítmények közé tartozik egy beépített nyomáscsökkentő (`Extension.Python.Reducer`) futó Python-kód a nyomáscsökkentő rendelt csúcspontok
* A U-SQL parancsfájl tartalmazza a beágyazott Python-kód, amely rendelkezik a hívott függvény `usqlml_main` , amely fogad egy pandas DataFrame bemenetként és a egy pandas DataFrame kimeneteként adja vissza.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Python együttműködéséről az U-SQL

### <a name="datatypes"></a>Adattípusok

* Konvertálja a karakterlánc és a numerikus oszlopok a U-SQL-közötti Pandas és a U-SQL
* U-SQL Nullértékeit lesznek konvertálva, és onnan Pandas `NA` értékek

### <a name="schemas"></a>Sémák

* Index útvonalairól Pandas nem támogatottak a U-SQL-ben. A Python-függvény az összes bemeneti adatkeretek mindig van egy 64 bites numerikus indexet 0 és 1 mínusz sorok számát. 
* U-SQL-adatkészletek nem lehet ismétlődő oszlopneveket tartalmaz
* U-SQL adatkészletek oszlopnevek, amelyek nem karakterlánc. 

### <a name="python-versions"></a>Python-verzió
Csak a Python 3.5.1 (Windows vagy későbbi verzióra) használata támogatott. 

### <a name="standard-python-modules"></a>Python-modulok
A standard szintű Python modulok is.

### <a name="additional-python-modules"></a>További Python-modulok
A standard szintű Python-kódtárakat mellett számos gyakran használt python-kódtárakat foglalja magában:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Kivétel üzenetek
Jelenleg kivételt a Python-kód jelenik meg, általános csúcspont hiba. A U-SQL-feladat hibaüzenetek a későbbiekben, a Python-kivétel üzenetet jelenít meg.

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti fájlméretre vonatkozó korlátozások
Minden csúcspont korlátozott mennyiségű memória rendelve van. Ezt a korlátot jelenleg az AU 6 GB. A bemeneti és kimeneti DataFrames léteznie kell a Python-kódban a memóriában, mert a bemeneti és kimeneti teljes mérete legfeljebb 6 GB.

## <a name="see-also"></a>Lásd még
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-ablakfunkciók használata Azure Data Lake Analytics-feladatok](data-lake-analytics-use-window-functions.md)
* [Használja az Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
