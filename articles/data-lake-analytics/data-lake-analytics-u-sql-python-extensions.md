---
title: Az U-SQL-parancsfájlok kiterjesztése a Pythonnal az Azure Data Lake Analytics szolgáltatásban
description: Megtudhatja, hogyan futtathat Python-kódot U-SQL-parancsfájlokban az Azure Data Lake Analytics használatával
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813402"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Az U-SQL-parancsfájlok kiterjesztése Python-kóddal az Azure Data Lake Analytics szolgáltatásban

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a Python-bővítmények telepítve vannak az Azure Data Lake Analytics-fiókban.

* Navigálás a Data Lake Analytics-fiókra az Azure Portalon
* A bal oldali menüben az **ELSŐ LÉPÉSEK** csoportban kattintson a **Mintaparancsfájlokra**
* Kattintson **az U-SQL extensions telepítése gombra,** majd **az OK gombra**

## <a name="overview"></a>Áttekintés 

Az U-SQL python-bővítmények lehetővé teszik a fejlesztők számára, hogy a Python-kód masszívan párhuzamos végrehajtását hajtsák végre. A következő példa az alapvető lépéseket mutatja be:

* Az `REFERENCE ASSEMBLY` utasítás használata python-bővítmények engedélyezéséhez az U-SQL Scripthez
* A `REDUCE` művelet használata a kulcs bemeneti adatainak particionálására
* Az U-SQL Python-bővítményei tartalmaznak egy`Extension.Python.Reducer`beépített szűkítőt ( ), amely python-kódot futtat a szűkítőhöz rendelt minden csúcson
* Az U-SQL parancsfájl tartalmazza a beágyazott Python-kódot, amely nek van egy nevű `usqlml_main` függvénye, amely elfogadja a pandák DataFrame bemenetként, és egy pandaDataFrame-et ad vissza kimenetként.

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

## <a name="how-python-integrates-with-u-sql"></a>Hogyan integrálható a Python az U-SQL-rel?

### <a name="datatypes"></a>Adattípusok

* Az U-SQL karakterlánc- és numerikus oszlopai a Pandák és az U-SQL közötti
* Az U-SQL Null-ok panda `NA` értékekké és azokból konvertálódnak

### <a name="schemas"></a>Sémák

* A Pandák indexvektok nem támogatottak az U-SQL-ben. A Python függvényben lévő összes bemeneti adatkeret mindig rendelkezik egy 64 bites numerikus indexsel 0-tól a sorok mínusz 1 számáig. 
* Az U-SQL adatkészletek nem rendelkezhetnek ismétlődő oszlopnevekkel
* U-SQL adatkészletek oszlopnevei, amelyek nem karakterláncok. 

### <a name="python-versions"></a>Python-verziók
Csak a Python 3.5.1 (Windows rendszerre lefordított) támogatott. 

### <a name="standard-python-modules"></a>Szabványos Python-modulok
Az összes szabványos Python-modul tartalmazza.

### <a name="additional-python-modules"></a>További Python-modulok
A szabványos Python-könyvtárak mellett számos általánosan használt python-könyvtár is megtalálható:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Kivételüzenetek
Jelenleg egy kivétel a Python-kódban általános csúcspont-hibaként jelenik meg. A jövőben az U-SQL feladat hibaüzenetek jelennek meg a Python kivétel üzenetet.

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretkorlátozások
Minden csúcsponthoz korlátozott mennyiségű memória van hozzárendelve. Jelenleg ez a korlát 6 GB egy AU esetében. Mivel a bemeneti és kimeneti DataFrames léteznie kell a memóriában a Python-kód, a teljes mérete a bemeneti és kimeneti nem haladhatja meg a 6 GB-ot.

## <a name="see-also"></a>Lásd még
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL ablakfüggvények használata az Azure Data Lake Analytics-feladatokhoz](data-lake-analytics-use-window-functions.md)
* [A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata](data-lake-analytics-data-lake-tools-for-vscode.md)
