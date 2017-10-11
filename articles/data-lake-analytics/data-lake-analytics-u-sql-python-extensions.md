---
title: "Python az Azure Data Lake Analytics U-SQL-parancsfájlok kiterjesztése |} Microsoft Docs"
description: "Megtudhatja, hogyan Python kódját a U-SQL-parancsfájlok futtatása"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: d18ef1f747aee2fa01cef9891432d0461031ee4c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-python"></a>Oktatóanyag: Ismerkedés a U-SQL Python kiterjesztése

U-SQL Python-bővítmények a fejlesztők hajtsa végre a Python kód nagymértékben párhuzamos végrehajtása. A következő példa bemutatja az alapvető lépéseket:

* Használja a `REFERENCE ASSEMBLY` utasítás a U-SQL parancsfájl Python bővítmények engedélyezése
* Használja a `REDUCE` művelet, a kulcs a bemeneti adatok particionálása
* A U-SQL Python-bővítmények közé tartozik egy beépített nyomáscsökkentő (`Extension.Python.Reducer`) futó Python kódját a nyomáscsökkentő rendelt minden csomópont
* A U-SQL parancsfájl tartalmazza a beágyazott Python-kódot, amely rendelkezik a hívott függvény `usqlml_main` , amely fogad egy pandas DataFrame bemeneti adatként, és egy pandas DataFrame kimeneteként adja vissza.

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

## <a name="how-python-integrates-with-u-sql"></a>Hogyan integrálható a Python U-SQL

### <a name="datatypes"></a>Adattípusok

* Konvertálja a karakterláncot és a numerikus oszlopot a U-SQL-Pandas és a U-SQL között
* U-SQL nullák alakítja a Pandas érkező vagy oda irányuló `NA` értékek

### <a name="schemas"></a>Sémák

* A Pandas index vektorok nem támogatottak a U-SQL. A Python függvény összes bemeneti adatkeretek mindig rendelkezik egy 64 bites numerikus index 0 és mínusz 1 sorok száma. 
* U-SQL adatkészletek nem lehet ismétlődő oszlopneveket tartalmaz
* U-SQL adatkészletek oszlopnevek, amelyek nem karakterlánc. 

### <a name="python-versions"></a>Python-verziók
Csak a Python 3.5.1 (Windows fordított) használata támogatott. 

### <a name="standard-python-modules"></a>Python-modulok
Összes a Python modulban találhatók.

### <a name="additional-python-modules"></a>További Python-modulok.
A szabványos Python-függvénytárak mellett számos általánosan használt python-könyvtárak jelennek meg:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Kivétel üzenetek
Jelenleg a Python kódban kivétel jeleníti meg hibaként általános csúcspont. A jövőben a U-SQL-feladatot hibaüzenetek a Python kivétel üzenetet jelenít meg.

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretkorlátai
Minden csomópont csak korlátozott mennyiségű memória rendelve van. Ezt a határértéket jelenleg egy AU 6 GB-ot. A bemeneti és kimeneti DataFrames már léteznie kell a Python kódját a memória, mert a bemeneti és kimeneti teljes mérete nem haladhatja meg a 6 GB.

## <a name="see-also"></a>Lásd még:
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL ablak függvények használata az Azure Data Lake Analytics-feladatok](data-lake-analytics-use-window-functions.md)

