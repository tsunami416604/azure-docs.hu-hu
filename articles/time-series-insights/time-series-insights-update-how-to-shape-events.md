---
title: Alakzat eseményei a Azure Time Series Insights Preview-val | Microsoft Docs
description: Megtudhatja, hogyan formázhatja az eseményeket Azure Time Series Insights előzetes verzióval.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: f8a50e062d2dac1f30f8b745f351570262daac53
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990897"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Események Azure Time Series Insights előzetes verzióval

Ebből a cikkből megtudhatja, hogyan alakíthatja át a JSON-fájlt, hogy maximalizálja Azure Time Series Insights előnézeti lekérdezések hatékonyságát.

## <a name="best-practices"></a>Ajánlott eljárások

Gondolja át, hogyan küldi el az eseményeket Time Series Insights előzetes verzióra. Tehát mindig a következőket kell tennie:

* Az adatküldés a hálózaton keresztül a lehető leghatékonyabban.
* Az adatokat olyan módon tárolhatja, amely segít az adott forgatókönyvnek megfelelően összesíteni.

A legjobb lekérdezési teljesítmény érdekében tegye a következőket:

* Ne küldjön felesleges tulajdonságokat. Time Series Insights előzetes verzióra vált a használat során. A legjobb megoldás a lekérdezni kívánt adatfeldolgozások tárolása és feldolgozása.
* A statikus adathoz használjon példány mezőket. Ez a gyakorlat segít elkerülni a statikus adatok hálózaton keresztüli küldését. A példány mezői, az idősorozat-modell egyik összetevője, például a Time Series Insights szolgáltatásban általánosan elérhető hivatkozási adat. Ha többet szeretne megtudni a példány mezőiről, tekintse meg a [Time Series Model](./time-series-insights-update-tsm.md)című témakört.
* Dimenzió tulajdonságainak megosztása két vagy több esemény között. Ezzel a gyakorlattal hatékonyabban küldhet adatküldést a hálózaton keresztül.
* Ne használjon mély tömbös beágyazást. Time Series Insights az előzetes verzió legfeljebb két olyan beágyazott tömböt támogat, amelyek objektumokat tartalmaznak. Time Series Insights az előnézet az üzenetekben található tömböket több, tulajdonság érték párokkal rendelkező eseménybe.
* Ha csak néhány mérték létezik az összes vagy a legtöbb eseménynél, érdemes elküldenie ezeket a mértékeket különálló tulajdonságokként ugyanazon az objektumon belül. A küldésük külön csökkenti az események számát, és növelheti a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni.

## <a name="example"></a>Példa

A következő példa egy olyan forgatókönyvön alapul, amelyben a két vagy több eszköz méréseket vagy jeleket küld. A mérések vagy jelek *áramlási sebessége*, a *motor*olajnyomás, a *hőmérséklet*és a *páratartalom*lehet.

A példában egyetlen Azure IoT Hub üzenet jelenik meg, amelyben a külső tömb a közös dimenzió értékeinek közös szakaszát tartalmazza. A külső tömb a Time Series-példányok adatmennyiségét használja az üzenet hatékonyságának növeléséhez. 

Az idősorozat-példány eszköz-metaadatokat tartalmaz. Ez a metaadatok nem változnak minden eseménnyel, de hasznos tulajdonságokat biztosít az adatok elemzéséhez. Ha menteni szeretné a huzalon küldött bájtokat, és hatékonyabbá teszi az üzenetet, érdemes lehet a közös dimenzió értékeit és az idősorozat-példányok metaadatait használni.

### <a name="example-json-payload"></a>Példa JSON-adattartalomra

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Idősorozat-példány 
> [!NOTE]
> Az idősorozat-azonosító a *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Insights az előnézet Összekapcsol egy táblát (az összeolvasztás után) a lekérdezés ideje alatt. A tábla további oszlopokat, például **típust**tartalmaz. Az alábbi példa bemutatja, hogyan [alakíthatja](./time-series-insights-send-events.md#supported-json-shapes) át a telemetria adatait.

| deviceId  | Type (Típus) | L1 | L2 | időbélyeg | sorozat. Áramlási sebesség FT3/s | sorozat. Motor olajnyomás PSI |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SZIMULÁTOR | Akkumulátorrendszer | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SZIMULÁTOR |   Akkumulátorrendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA KÖZÖS | SZIMULÁTOR |    Akkumulátorrendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

Az előző példában vegye figyelembe a következő szempontokat:

* A statikus tulajdonságok Time Series Insights előzetes verzióban tárolódnak, hogy optimalizálják a hálózaton keresztül továbbított adatátvitelt.
* Time Series Insights az előnézeti idő a példányban definiált idősorozat-AZONOSÍTÓn keresztül csatlakozik a lekérdezési időponthoz.
* Két beágyazási réteg van használatban. Ez a szám a legtöbbet Time Series Insights előzetes verzió támogatja. Fontos, hogy elkerülje a mélyen beágyazott tömböket.
* Mivel a rendszer néhány mértéket használ, ezeket külön tulajdonságokként küldik el ugyanazon az objektumon belül. A példában a **sorozat. Áramlási sebesség PSI**, **sorozat. A motor olajnyomás PSI**és **adatsorozatok. A flow Rate FT3/s** egyedi oszlopok.

>[!IMPORTANT]
> A példány mezői nem tárolódnak a telemetria. A rendszer metaadatokat tárol a Time Series-modellben.
> Az előző táblázat a lekérdezés nézetet jelöli.

## <a name="next-steps"></a>Következő lépések

- Az irányelvek gyakorlatba való helyezéséhez lásd: [Azure Time Series Insights előnézeti lekérdezés szintaxisa](./time-series-insights-query-data-csharp.md). A lekérdezési szintaxissal kapcsolatos további információkért tekintse meg az adatelérés Time Series Insights előzetes verzió REST APIét.
- A támogatott JSON-alakzatokkal kapcsolatos további tudnivalókért lásd a [támogatott JSON-alakzatokat](./time-series-insights-send-events.md#supported-json-shapes).
