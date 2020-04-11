---
title: Felhasználó által definiált függvények az Azure Stream Analytics szolgáltatásban
description: Ez a cikk az Azure Stream Analytics felhasználó által definiált függvényeinek áttekintése.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115585"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Felhasználó által definiált függvények az Azure Stream Analytics szolgáltatásban

Az Azure Stream Analytics SQL-szerű lekérdezési nyelve megkönnyíti a valós idejű elemzési logika megvalósítását a streamelési adatokon. A Stream Analytics további rugalmasságot biztosít a lekérdezésben meghívott egyéni függvények révén. A következő kód példa egy `sampleFunction` UDF nevű, amely elfogadja egy paraméter, minden bemeneti rekord `sampleResult`a feladat kap, és az eredmény van írva a kimeneti .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>A függvények típusai

Az Azure Stream Analytics a következő négy függvénytípust támogatja: 

* Felhasználói JavaScript-függvények 
* JavaScript-felhasználó által definiált összesítések 
* C# felhasználó által definiált függvények (a Visual Studio használatával) 
* Azure Machine Learning 

Ezeket a függvényeket olyan forgatókönyvekhez használhatja, mint a gépi tanulási modellek, a karakterlánc-műveletek, az összetett matematikai számítások, a kódolás és az adatok dekódolása. 

## <a name="limitations"></a>Korlátozások

A felhasználó által definiált függvények állapot nélküliek, és a visszatérési érték csak skaláris érték lehet. Ezekből a felhasználó által definiált függvényekből nem hívhat ki külső REST-végpontokat, mert valószínűleg hatással lesz a feladat teljesítményére. 

Az Azure Stream Analytics nem vezet nyilvántartást az összes függvény meghívásáról és a visszaadott eredményekről. Az ismételhetőség biztosítása érdekében – például a feladat régebbi időbélyegről történő `Date.GetData()` ismételt `Math.random()`futtatása újra ugyanazt az eredményt hozza létre – ne használjon olyan függvényeket, mint például a vagy a, mivel ezek a függvények nem adják vissza ugyanazt az eredményt az egyes meghívások esetén.  

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Minden futásidejű hiba végzetesnek minősül, és tevékenység- és diagnosztikai naplókon keresztül kerül felszínre. Javasoljuk, hogy a függvény kezelje az összes kivételt és hibát, és érvényes eredményt ad vissza a lekérdezésnek. Ez megakadályozza, hogy a feladat [sikertelen állapotba kerüljön.](job-states.md)  


## <a name="next-steps"></a>További lépések

* [JavaScript-felhasználó által definiált függvények az Azure Stream Analytics szolgáltatásban](stream-analytics-javascript-user-defined-functions.md)
* [Az Azure Stream Analytics JavaScript által definiált összesítések](stream-analytics-javascript-user-defined-aggregates.md)
* [A .NET Standard szolgáltatásban megadott, az Azure Stream Analytics-feladatokhoz definiált függvények fejlesztése](stream-analytics-edge-csharp-udf-methods.md)
* [Az Azure Stream Analytics integrálása az Azure Machine Learningszolgáltatással](machine-learning-udf.md)

