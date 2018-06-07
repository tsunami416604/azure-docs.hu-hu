---
title: Python az Azure Data Lake Analytics U-SQL-parancsfájlok kiterjesztése
description: Megtudhatja, hogyan futtassa a Python-kódot használó Azure Data Lake Analytics U-SQL-parancsfájlok
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 3e895a6ea9bccc0d210f43748edb3eea80ddc6ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625080"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Python kód az Azure Data Lake Analytics U-SQL-parancsfájlok kiterjesztése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, a Python-bővítmények vannak telepítve az Azure Data Lake Analytics-fiók.

* Keresse meg azt a Data Lake Analytics-fiók az Azure-portálon
* A bal oldali menü alatti **bevezetés** kattintson a **mintaparancsfájlok**
* Kattintson a **U-SQL-bővítmények telepítése** majd **OK**

## <a name="overview"></a>Áttekintés 

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

## <a name="see-also"></a>Lásd még
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL ablak függvények használata az Azure Data Lake Analytics-feladatok](data-lake-analytics-use-window-functions.md)
* [Az Azure Data Lake Tools használja a Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
