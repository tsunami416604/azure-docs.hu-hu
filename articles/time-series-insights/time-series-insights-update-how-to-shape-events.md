---
title: Alakzat eseményei – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg az ajánlott eljárásokat, valamint az Azure Time betekintő előzetes verziójában végzett lekérdezési események alakját.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: fd744e6283b00b0dfdd50805cb628f5bc40ab8d6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846144"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Események Azure Time Series Insights előzetes verzióval

Ebből a cikkből megtudhatja, hogyan alakíthatja ki a JSON-fájlt a betöltéshez, és hogy maximalizálja Azure Time Series Insights előnézeti lekérdezések hatékonyságát.

## <a name="best-practices"></a>Ajánlott eljárások

Gondolja át, hogyan küldi el az eseményeket Time Series Insights előzetes verzióra. Tehát mindig a következőket kell tennie:

* Az adatküldés a hálózaton keresztül a lehető leghatékonyabban.
* Az adatokat olyan módon tárolhatja, amely segít az adott forgatókönyvnek megfelelően összesíteni.

A legjobb lekérdezési teljesítmény érdekében tegye a következőket:

* Ne küldjön felesleges tulajdonságokat. Time Series Insights előzetes verzióra vált a használat során. A legjobb megoldás a lekérdezni kívánt adatfeldolgozások tárolása és feldolgozása.
* A statikus adathoz használjon példány mezőket. Ez a gyakorlat segít elkerülni a statikus adatok hálózaton keresztüli küldését. A példány mezői, az idősorozat-modell egyik összetevője, például a Time Series Insights szolgáltatásban általánosan elérhető hivatkozási adat. Ha többet szeretne megtudni a példány mezőiről, olvassa el a [Time Series-modell](./time-series-insights-update-tsm.md)című témakört.
* Dimenzió tulajdonságainak megosztása két vagy több esemény között. Ezzel a gyakorlattal hatékonyabban küldhet adatküldést a hálózaton keresztül.
* Ne használjon mély tömbös beágyazást. Time Series Insights az előzetes verzió legfeljebb két olyan beágyazott tömböt támogat, amelyek objektumokat tartalmaznak. Time Series Insights az előnézet az üzenetekben található tömböket több, tulajdonság érték párokkal rendelkező eseménybe.
* Ha csak néhány mérték létezik az összes vagy a legtöbb eseménynél, érdemes elküldenie ezeket a mértékeket különálló tulajdonságokként ugyanazon az objektumon belül. A küldésük külön csökkenti az események számát, és növelheti a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni.

A betöltés során a beágyazást tartalmazó hasznos adatok összeolvasztása is megtörténik, így az oszlop neve egyetlen érték egy határolóval. Time Series Insights az előnézet aláhúzást használ a Körvonalazás során. Fontos megjegyezni, hogy ez a használt termék GA-verziójának változása. Az előzetes verzió használata során a rendszer kikötést mutat az összeolvasztással kapcsolatban, amely az alábbi második példában látható.

## <a name="examples"></a>Példák

A következő példa egy olyan forgatókönyvön alapul, amelyben a két vagy több eszköz méréseket vagy jeleket küld. A mérések vagy jelek *áramlási sebessége*, a *motor*olajnyomás, a *hőmérséklet*és a *páratartalom*lehet.

A példában egyetlen Azure IoT Hub üzenet jelenik meg, amelyben a külső tömb a közös dimenzió értékeinek közös szakaszát tartalmazza. A külső tömb a Time Series-példányok adatmennyiségét használja az üzenet hatékonyságának növeléséhez. 

Az idősorozat-példány eszköz-metaadatokat tartalmaz. Ez a metaadatok nem változnak minden eseménnyel, de hasznos tulajdonságokat biztosít az adatok elemzéséhez. Ha menteni szeretné a huzalon küldött bájtokat, és hatékonyabbá teszi az üzenetet, érdemes lehet a közös dimenzió értékeit és az idősorozat-példányok metaadatait használni.

### <a name="example-1"></a>1\. példa:

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

### <a name="time-series-instance"></a>Idősorozat-példány 

> [!NOTE]
> Az idősorozat-azonosító a *deviceId*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights az előnézet Összekapcsol egy táblát (az összeolvasztás után) a lekérdezés ideje alatt. A tábla további oszlopokat, például **típust**tartalmaz. Az alábbi példa bemutatja, hogyan [alakíthatja](./time-series-insights-send-events.md#supported-json-shapes) át a telemetria adatait.

| deviceId  | Type (Típus) | L1 | L2 | időbélyeg | series_Flow sebesség FT3/s | series_Engine olajnyomás PSI |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SZIMULÁTOR | Akkumulátorrendszer | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SZIMULÁTOR |   Akkumulátorrendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA KÖZÖS | SZIMULÁTOR |    Akkumulátorrendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

Az előző példában vegye figyelembe a következő szempontokat:

* A statikus tulajdonságok Time Series Insights előzetes verzióban tárolódnak, hogy optimalizálják a hálózaton keresztül továbbított adatátvitelt.
* Time Series Insights az előnézeti idő a példányban definiált idősorozat-AZONOSÍTÓn keresztül csatlakozik a lekérdezési időponthoz.
* Két beágyazási réteg van használatban. Ez a szám a legtöbbet Time Series Insights előzetes verzió támogatja. Fontos, hogy elkerülje a mélyen beágyazott tömböket.
* Mivel a rendszer néhány mértéket használ, ezeket külön tulajdonságokként küldik el ugyanazon az objektumon belül. A példában **Series_Flow ráta PSI**, **series_Engine olajnyomás psi**, és **series_Flow Rate FT3/s** egyedi oszlopok.

>[!IMPORTANT]
> A példány mezői nem tárolódnak a telemetria. A rendszer metaadatokat tárol a Time Series-modellben.
> Az előző táblázat a lekérdezés nézetet jelöli.

### <a name="example-2"></a>2\. példa:

Vegye figyelembe a következő JSON-t:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```
A fenti példában az összeolvasztott `data_flow` tulajdonság elnevezési ütközést jelent a `data_flow` tulajdonsággal. Ebben az esetben a *legújabb* tulajdonság értéke felülírja a korábbiat. Ha ez a viselkedés kihívást jelent az üzleti forgatókönyvek számára, forduljon az ÁME csapatához.

> [!WARNING] 
> Azokban az esetekben, ahol a duplikált tulajdonságok ugyanabban az esemény-adattartalomban találhatók, mint az összeolvasztás vagy más mechanizmus miatt, a rendszer a legújabb overwritting tárolja, és minden korábbi értéket megadhat.


## <a name="next-steps"></a>Következő lépések

Az irányelvek gyakorlatba való beírásához olvassa el [Azure Time Series Insights előnézeti lekérdezés szintaxisát](./time-series-insights-query-data-csharp.md). A lekérdezési szintaxissal kapcsolatos további információkért tekintse meg az adatelérés Time Series Insights előzetes verzió REST APIét.
