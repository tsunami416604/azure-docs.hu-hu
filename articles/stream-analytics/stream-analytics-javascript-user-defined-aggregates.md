---
title: "Az Azure Stream Analytics JavaScript felhasználó által definiált összesítések |} Microsoft Docs"
description: "Hajtsa végre a felhasználó által definiált összesítések JavaScript speciális lekérdezési mechanika"
keywords: "JavaScript, felhasználó által definiált összesítések, uda-értékét"
services: stream-analytics
author: minhe-msft
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/28/2017
ms.author: minhe
ms.openlocfilehash: b3863a34ed146e54c6d60e035957b942a1976ff9
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Az Azure Stream Analytics JavaScript felhasználó által definiált összesítések (előzetes verzió)

Az Azure Stream Analytics támogatja a felhasználó által definiált összesítések (UDA) JavaScript nyelven írt, lehetővé teszi összetett állapot-nyilvántartó üzleti logika végrehajtásához. Teljes körű hozzáférést engedélyezzenek a állapot adatszerkezet, állapot felhalmozódása, állapot összesítésének alkalmasságára és összesített eredmény számítási rendelkezik belül uda-Értékeket. A cikk bemutatja a két különböző felületeihez JavaScript uda-Értékeket, egy uda-Értékeket, és uda-Értékeket használata a Windows-alapú műveletek a Stream Analytics-lekérdezés létrehozásához szükséges lépéseket.

## <a name="javascript-user-defined-aggregates"></a>Felhasználó által definiált összesítések JavaScript

A felhasználó által definiált összesítő fölött egy ablak a megadott időpont ezt az ablakot az események összesíteni, és egyetlen eredmény érték létrehozására szolgál. Uda-Értékeket felületek, hogy a Stream Analytics jelenleg támogatja AccumulateOnly és AccumulateDeaccumulate két típusa van. Mindkét típusú uda-Értékeket Átfedésmentes ablak, Hopping, és a késleltetett ablakban használhatják. AccumulateDeaccumulate uda-Értékeket AccumulateOnly UDA használt Hopping és a késleltetett ablak jobb hajt végre. A két típusú használata algoritmus alapján választja.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly összesítések

AccumulateOnly összesítések csak felhalmozhat új események állapotát, az algoritmus nem engedélyezi a deaccumulation értékek. Válassza ki az összesítési típus amikor deaccumulate esemény a állapotérték adatait lehetetlen végrehajtásához. Az alábbiakban olvashat a JavaScript sablon AccumulatOnly aggregátumok:

````JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate összesítések

AccumulateDeaccumulate összesítések állapotból, a korábbi halmozott értékét deaccumulation például engedélyezése, távolítsa el a kulcs-érték pár esemény értékből álló lista vagy értéket összesítő Sum állapotból kivonandó időnél. Az alábbiakban olvashat a JavaScript sablon AccumulateDeaccumulate aggregátumok:

````JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript függvény deklarációjában

Minden JavaScript uda-Értékeket egy függvény objektum deklarációnak van definiálva. Az alábbiakban a fő elemet UDA-definícióban.

### <a name="function-alias"></a>Függvény aliasa

Függvény alias az adott uda-Értékeket azonosítója. A Stream Analytics query meghívásakor mindig használjon uda-Értékeket alias együtt a "uda-értékeket." előtag.

### <a name="function-type"></a>Függvénytípus

Az uda-Értékeket, a függvény típusnak kell lennie **Javascript uda-Értékeket**.

### <a name="output-type"></a>Kimeneti típust

Egy adott, írja be a Stream Analytics-feladat támogatott, vagy a "Bármely" if szeretné kezelni a lekérdezés a típus.

### <a name="function-name"></a>Függvény neve

A függvény objektum neve. A függvény nevét szó meg kell felelnie a uda-Értékeket alias (előzetes viselkedését, azt is fontolóra veheti, hogy támogatási névtelen függvény amikor GA).

### <a name="method---init"></a>Módszer - init()

A init() metódus inicializálja összesített állapotát. Ezt a módszert nevezik ablak indításakor.

### <a name="method--accumulate"></a>Módszer – accumulate()

A accumulate() metódus az uda-Értékeket állapot, a korábbi állapotába, és az aktuális esemény értékek alapján számítja ki. Ezt a módszert nevezik, amikor eseménnyel ér egy olyan időkeretet (TUMBLINGWINDOW, HOPPINGWINDOW vagy SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Módszer – deaccumulate()

A deaccumulate() metódus újraszámítja a korábbi állapotába, és az aktuális esemény értékek alapján. Ezt a módszert nevezik, amikor esemény elhagyja a SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Módszer – deaccumulateState()

A deaccumulateState() metódus újraszámítja állapotát annak megfelelően hop állapotát és a korábbi állapotába. Ez a módszer van meghívva, amikor az események hagyja a HOPPINGWINDOW készlete.

### <a name="method--computeresult"></a>Módszer – computeResult()

A computeResult() módszer az aktuális állapotától függően összesített eredményt adja vissza. Ez a módszer egy olyan időkeretet (TUMBLINGWINDOW HOPPINGWINDOW és SLIDINGWINDOW) végén nevezik.

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript uda-Értékeket támogatott bemeneti és kimeneti adattípusok
A JavaScript uda-Értékeket adattípusok, tekintse át a részt **Stream Analytics és a JavaScript adattípus átalakítása** a [integrálni JavaScript felhasználó által megadott függvények](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>A JavaScript uda-Értékeket hozzáadása az Azure-portálon

Az alábbiakban azt ismerteti a folyamatot, amely egy uda-Értékeket létrehozása a portálon. A példában használjuk itt van számítástechnikai átlagok.

Most hozzuk létre hajtsa végre a JavaScript uda-Értékeket egy meglévő ASA feladat alatt.

1. Jelentkezzen be Azure-portálon, és keresse meg a meglévő Stream Analytics-feladat.
1. Kattintson a hivatkozásra kattintva funkciók **feladat TOPOLÓGIA**.
1. Kattintson a **Hozzáadás** ikonra egy új funkció hozzáadásához.
1. Válassza ki az új függvény nézet **JavaScript uda-Értékeket** függvény típusként, majd megjelenik egy alapértelmezett uda-Értékeket sablon jelenik meg a szerkesztőben.
1. Töltse ki a uda-Értékeket aliasként "TWA", és módosítsa a függvény végrehajtása a következők:

    ````JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ````

1. A "Mentés" gombra, ha a uda-Értékeket a függvény lista megjelenik.

1. Kattintson az új függvény "TWA" ellenőrizheti, a függvény definícióját.

## <a name="calling-javascript-uda-in-asa-query"></a>JavaScript uda-Értékeket hívása ASA lekérdezés

Azure-portálon, és nyissa meg a feladat, a lekérdezés szerkesztése és TWA() függvény alapján "uda-értékeket." előtaggal kezdődik. Példa:

````SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
````

## <a name="testing-query-with-uda"></a>Lekérdezés uda-Értékeket a tesztelés

Hozzon létre egy helyi JSON-fájlt az alábbi tartalmat feltölteni a fájlt az Stream Analytics-feladat és fent lekérdezés teszteléséhez.

````JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
````

## <a name="get-help"></a>Segítségkérés

Segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
