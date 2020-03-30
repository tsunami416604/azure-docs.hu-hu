---
title: JavaScript-felhasználó által definiált összesítések az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan hajthatja végre a speciális lekérdezési mechanika javascript-felhasználó által definiált összesítések az Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: c509d174787a58abeee33e039eb7bbbcbcb43f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531734"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Az Azure Stream Analytics JavaScript által definiált összesítések
 
Az Azure Stream Analytics támogatja a felhasználó által definiált aggregátumok (UDA) JavaScript nyelven írt, lehetővé teszi, hogy összetett állapotalapú üzleti logika megvalósítását. Az UDA-n belül teljes hozzáféréssel rendelkezik az állapotadat-szerkezet, az állapotfelhalmozódás, az állapotdekumuláció és az összesített eredményszámítás felett. A cikk bemutatja a két különböző JavaScript UDA-felületeket, az UDA létrehozásának lépéseit, valamint az UDA használatának módját ablakalapú műveletekkel a Stream Analytics-lekérdezésben.

## <a name="javascript-user-defined-aggregates"></a>JavaScript-felhasználó által definiált összesítések

A felhasználó által definiált összesítés takarásként az ablakspecifikáción alapul, és az adott ablakban lévő eseményeket összesíti, és egyetlen eredményértéket hoz létre. A Stream Analytics által ma támogatott UDA-felületek nek két típusa van: AccumulateOnly és AccumulateDeaccumulate. Mindkét típusú UDA használható Tumbling, Hopping, Sliding és Session Window. Az AaccumulateDeaccumulate UDA jobban teljesít, mint az AkkumOnly UDA, ha az Ugráló, Csúszó és Munkamenet ablakkal együtt használja. A két típus közül választhat a használt algoritmus alapján.

### <a name="accumulateonly-aggregates"></a>AkkumulációCsak aggregátumok

AccumulateCsak aggregátumok csak felhalmozódnak az új események az állam, az algoritmus nem teszi lehetővé az értékek deaccumulation. Akkor válassza ezt az összesített típust, ha az állapotértékből származó eseményadatok dekamezenstal való felhalmozódása nem valósítható meg. Az alábbiakban a JavaScript sablon AccumulatOnly összesítések:

```JavaScript
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
```

### <a name="accumulatedeaccumulate-aggregates"></a>Akkumulált aggregátumok

Az akkumulált aggregátumok lehetővé teszik egy korábbi halmozott érték deaktakulását az állapotból, például eltávolítanak egy kulcs-érték párt az eseményértékek listájáról, vagy kivonnak egy értéket az összegösszesítés állapotából. A következőkben a JavaScript sablon accumulatedeaccumulate összesítések:

```JavaScript
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
```

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript függvény deklaráció

Minden JavaScript UDA-t egy függvényobjektum-deklaráció határoz meg. Az UDA-definíció főbb elemei a következőkben találhatók.

### <a name="function-alias"></a>Függvény aliasa

A függvényalias az UDA-azonosító. A Stream Analytics-lekérdezésben történő behíváskor mindig uda aliast használjon egy "uda"-val együtt. előtagot.

### <a name="function-type"></a>Függvény típusa

UDA esetén a függvénytípusnak **Javascript UDA-nak**kell lennie.

### <a name="output-type"></a>Kimenet típusa

Egy adott típus, amelyet a Stream Analytics-feladat támogatott, vagy "Bármilyen", ha kezelni szeretné a típust a lekérdezésben.

### <a name="function-name"></a>Függvény neve

A függvényobjektum neve. A függvény nevének meg kell egyeznie az UDA aliasévával.

### <a name="method---init"></a>Módszer - init()

Az init() metódus inicializálja az aggregátum állapotát. Ez a metódus az ablak indításakor jelenik meg.

### <a name="method--accumulate"></a>Módszer – akkumuláció()

A accumulate() metódus az előző állapot és az aktuális eseményértékek alapján számítja ki az UDA-állapotot. Ez a metódus akkor van meghívva, amikor egy esemény időablakba lép (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW vagy SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Módszer – deaccumulate()

A deaccumulate() metódus újraszámítja az állapotot az előző állapot és az aktuális eseményértékek alapján. Ez a metódus akkor lesz meghívva, ha egy esemény ből csúszóablak vagy sessionwindow lép.

### <a name="method--deaccumulatestate"></a>Módszer – deaccumulateState()

A deaccumulateState() metódus újraszámítja az állapotot az előző állapot és az ugrás állapota alapján. Ez a módszer akkor van meghívva, ha egy eseménykészlet hoppingwindow-t hagy.

### <a name="method--computeresult"></a>Módszer – computeResult()

A computeResult() metódus az aktuális állapot alapján adja vissza az összesített eredményt. Ezt a módszert egy időablak végén (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW vagy SESSIONWINDOW) nevezzük meg.

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA támogatott bemeneti és kimeneti adattípusok
JavaScript UDA-adattípusok esetén olvassa el a [JavaScript UDF-ek integrálása](stream-analytics-javascript-user-defined-functions.md) **Stream Analytics és JavaScript típusú átalakítás** című szakaszt.

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>JavaScript UDA hozzáadása az Azure Portalról

Az alábbiakban végigsétálunk az UDA-nak a Portal-ból történő létrehozásának folyamatán. Az itt használt példa az idősúlyozott átlagok kiszámítása.

Most hozzon létre egy JavaScript UDA egy meglévő ASA-feladat a következő lépéseket.

1. Jelentkezzen be az Azure Portalra, és keresse meg meglévő Stream Analytics-feladatát.
1. Ezután kattintson a funkciók linkre **a FELADAT TOPOLÓGIA**csoportban.
1. Új funkció hozzáadásához kattintson a **Hozzáadás** ikonra.
1. Az Új függvény nézetben válassza a **JavaScript UDA** függvénytípust, majd megjelenik egy alapértelmezett UDA-sablon a szerkesztőben.
1. Töltse ki a "TWA" uda aliasként, és módosítsa a függvény implementációját a következőképpen:

    ```JavaScript
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
    ```

1. Miután a "Mentés" gombra kattintott, az UDA megjelenik a funkciólistában.

1. Kattintson az új funkció "TWA", akkor ellenőrizze a funkció meghatározása.

## <a name="calling-javascript-uda-in-asa-query"></a>JavaScript UDA hívása az ASA-lekérdezésben

Az Azure Portalon, és nyissa meg a feladatot, szerkesztheti a lekérdezést, és hívja meg a TWA() függvényt az "uda" megbízáselőtaggal. Példa:

```SQL
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
```

## <a name="testing-query-with-uda"></a>Lekérdezés tesztelése UDA-val

Hozzon létre egy helyi JSON-fájlt az alábbi tartalommal, töltse fel a fájlt a Stream Analytics-feladatba, és tesztelje a fenti lekérdezést.

```JSON
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
```

## <a name="get-help"></a>Segítségkérés

További segítségért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Az Azure Stream Analytics lekérdezési nyelvének hivatkozása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API-hivatkozása](https://msdn.microsoft.com/library/azure/dn835031.aspx)
