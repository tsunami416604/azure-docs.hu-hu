---
title: JavaScript nyelvű felhasználó által definiált összesítések az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan hajthat végre az összetett lekérdezési műveleteket a JavaScript nyelvű felhasználó által definiált összesítések az Azure Stream Analytics szolgáltatásban.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: eb433a322f8077c947fd6db1aaa0e2266a109938
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187054"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Az Azure Stream Analytics JavaScript nyelvű felhasználó által definiált összesítések (előzetes verzió)
 
Az Azure Stream Analytics támogatja a felhasználó által definiált összesítések (UDA) javascriptben írt, lehetővé teszi az állapotalapú összetett üzleti logikát. UDA belül, az állapot adatszerkezet, állam felhalmozódása, állapot összesítésének alkalmasságára és összesített eredmény számítási teljes hozzáféréssel rendelkeznek. A cikk a két különböző felületek JavaScript UDA, UDA, és UDA használata Windows-alapú műveleteket a Stream Analytics-lekérdezés létrehozásának lépéseit mutatja be.

## <a name="javascript-user-defined-aggregates"></a>JavaScript nyelvű felhasználó által definiált összesítések

Egy felhasználó által meghatározott összesítés felett egy ablakot a megadott időpont a ezt az ablakot az események összesítése és egyszeri eredmény érték létrehozására szolgál. UDA-adapterek, hogy Stream Analytics támogatja a mai AccumulateOnly és AccumulateDeaccumulate két típusa van. Mindkét típusú UDA Átfedésmentes ablak, segítségével tehetjük meg, és késleltetett ablakban használhatják. AccumulateDeaccumulate UDA jobban, mint a AccumulateOnly UDA segítségével tehetjük meg és késleltetett ablakban együtt használva hajt végre. Az algoritmus használata alapján a két típus egyikének kiválasztása.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly összesítések

AccumulateOnly összesítések csak felhalmozhat annyi új események az állapotba, az algoritmus nem engedélyezi a deaccumulation értékek. Válassza ki az összesített mikor deaccumulate egy eseményt az állapotérték adatait nem lehet megvalósítani. Következő az AccumulatOnly összesítések a JavaScript-sablon:

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

AccumulateDeaccumulate összesítések például lehetővé teszi egy előző összesített érték a állapotból deaccumulation, távolítsa el a kulcs-érték pár esemény értékekből álló listát, vagy kivonása egy értéket egy sum összesített állapotát. Következő az AccumulateDeaccumulate összesítések a JavaScript-sablon:

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

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript-függvény deklarációjában

Minden egyes JavaScript UDA-objektum deklarace Funkce határozza meg. Az alábbiakban a fő elemeiről UDA-definícióban.

### <a name="function-alias"></a>Függvényalias

Függvény aliasa a UDA azonosítója. Neve a Stream Analytics-lekérdezés, mindig használjon UDA alias együtt egy "uda." előtag.

### <a name="function-type"></a>Függvény típusa

Az UDA, a függvény típusúnak kell lennie **Javascript UDA**.

### <a name="output-type"></a>Kimenet típusa

Egy adott írja be a Stream Analytics-feladat támogatott, vagy a "Bármely" if szeretné kezelni a típusát a lekérdezésben.

### <a name="function-name"></a>Függvény neve

Ez a funkció az objektum neve. Szó szerint a függvény nevét meg kell egyeznie az UDA-alias (előzetes verzió viselkedését, azt fontolgatja támogatási névtelen függvény amikor általánosan elérhető).

### <a name="method---init"></a>Módszer - init()

Az init() metódust inicializálja az összesített állapotát. Ezt a módszert nevezik ablak indításakor.

### <a name="method--accumulate"></a>Módszer – accumulate()

A accumulate() metódus az UDA-állapot az előző állapotra és az aktuális esemény értékek alapján számítja ki. Ezt a módszert nevezik, amikor egy esemény egy olyan időkeretet (TUMBLINGWINDOW, HOPPINGWINDOW vagy SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Módszer – deaccumulate()

A deaccumulate() metódus újraszámítja állapota az előző állapotra és az aktuális esemény értékek alapján. Ez a módszer egy esemény távozik a SLIDINGWINDOW nevezzük.

### <a name="method--deaccumulatestate"></a>Módszer – deaccumulateState()

A deaccumulateState() metódus újraszámítja állapota alapján az előző állapotra és a egy Ugrás állapotát. Ezt a módszert nevezik, amikor események hagyja HOPPINGWINDOW egy készletét.

### <a name="method--computeresult"></a>Módszer – computeResult()

A computeResult() metódus az aktuális állapotától függően összesített eredményt adja vissza. Ez a metódus végén található egy olyan időkeretet (TUMBLINGWINDOW HOPPINGWINDOW és SLIDINGWINDOW) nevezzük.

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA támogatott bemeneti és kimeneti adatok típusa
JavaScript UDA-adatok esetében, tekintse meg a szakasz **Stream Analytics és a JavaScript típusátalakítás** , [integrálása a JavaScript UDF-EK](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>JavaScript UDA hozzáadása az Azure Portalról

Az alábbiakban azt végig az UDA-portálról történő létrehozásának folyamatán. Az itt használt példa a számítástechnikai átlagok.

Most hozzunk létre egy meglévő ASA-feladat a JavaScript UDA hajtsa végre.

1. Jelentkezzen be az Azure Portalra, és keresse meg a meglévő Stream Analytics-feladatot.
1. Kattintson a functions alatti hivatkozásra **FELADATTOPOLÓGIA**.
1. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá egy új függvényt.
1. Válassza ki az új függvény nézeten **JavaScript UDA** függvény típusaként, majd megjelenik egy alapértelmezett UDA sablon jelenik meg a szerkesztőben.
1. Az UDA-alias "TWA" adja meg, és módosítsa a függvény implementálását az alábbiak szerint:

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

1. Ha a "Mentés" gombra kattint, az UDA jelenik meg a függvény listán.

1. Kattintson az új függvény "TWA" ellenőrizheti a függvény definícióját.

## <a name="calling-javascript-uda-in-asa-query"></a>JavaScript UDA hívása az ASA-lekérdezés

Az Azure Portalon, és nyissa meg a feladatot, szerkessze a lekérdezést és TWA() függvény a megbízás "uda." előtaggal. Példa:

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

## <a name="testing-query-with-uda"></a>Tesztelési UDA-lekérdezés

Hozzon létre egy helyi JSON-fájlt az alábbi tartalmat, és feltöltheti a fájlt a Stream Analytics-feladat lekérdezést újabb teszteléséhez.

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

További segítségért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Az Azure Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
