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
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: fd2c58b07f3be5d5fa6d99d0c8c64906b81e7de4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036984"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Események Azure Time Series Insights előzetes verzióval

Ez a cikk az ajánlott eljárásokat ismerteti a JSON-adattartalom Azure Time Series Insights való betöltéséhez, és az előnézeti lekérdezések hatékonyságának maximalizálása érdekében.

## <a name="best-practices"></a>Ajánlott eljárások

A legjobb, ha alaposan gondolja át, hogyan küldi el az eseményeket a Time Series Insights előzetes verziójának környezetében. 

Az általános ajánlott eljárások a következők:

* Az adatküldés a hálózaton keresztül a lehető leghatékonyabban.
* Az adatokat olyan módon tárolhatja, amely segít az adott forgatókönyvnek megfelelően összesíteni.

A legjobb lekérdezési teljesítmény érdekében tartsa be a következő szabályokat a hüvelykujjhoz:

* Ne küldjön felesleges tulajdonságokat. Time Series Insights előnézeti számlákat használat szerint. A legjobb, ha csak a lekérdezni kívánt adatfeldolgozást szeretné tárolni és feldolgozni.
* A statikus adathoz használjon példány mezőket. Ez a módszer segít elkerülni a statikus adatok hálózaton keresztüli küldését. A példány mezői, az idősorozat-modell egyik összetevője, például a Time Series Insights szolgáltatásban általánosan elérhető hivatkozási adat. Ha többet szeretne megtudni a példány mezőiről, olvassa el a [Time Series-modell](./concepts-model-overview.md)című témakört.
* Dimenzió tulajdonságainak megosztása két vagy több esemény között. Ezzel a gyakorlattal hatékonyabban küldhet adatküldést a hálózaton keresztül.
* Ne használjon mély tömbös beágyazást. Time Series Insights az előzetes verzió legfeljebb két olyan beágyazott tömböt támogat, amelyek objektumokat tartalmaznak. Time Series Insights az előnézet az üzenetekben található tömböket több, tulajdonság érték párokkal rendelkező eseménybe.
* Ha csak néhány mérték létezik az összes vagy a legtöbb eseménynél, érdemes elküldenie ezeket a mértékeket különálló tulajdonságokként ugyanazon az objektumon belül. A küldésük külön csökkenti az események számát, és növelheti a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni.

## <a name="column-flattening"></a>Oszlop összeolvasztása

A betöltés során a beágyazott objektumokat tartalmazó adattárolók összeolvasztása megtörténik, így az oszlopnév egyetlen érték egy határolóval.

* Például a következő beágyazott JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   A (z) lesz: `data_flow` összeolvasztáskor.

> [!IMPORTANT]
> * Azure Time Series Insights az előnézet aláhúzásokat ( `_` ) használ az oszlopok megjelenítéséhez.
> * Figyelje meg, hogy az általános elérhetőségtől eltérnek a használati időszakok ( `.` ).

Az összetettebb forgatókönyvek az alábbi ábrán láthatók.

#### <a name="example-1"></a>1. példa:

A következő forgatókönyv két (vagy több) eszközt tartalmaz, amelyek elküldik a méréseket (jeleket): *áramlási sebesség*, a *motor*olajnyomás, a *hőmérséklet*és a *páratartalom*.

Egyetlen Azure IoT Hub üzenet van elküldve, ahol a külső tömb a közös dimenzió értékek közös szakaszát tartalmazza (jegyezze fel az üzenetben található két eszköz bejegyzését).

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

**Elvihető ételek**

* A JSON-példa olyan külső tömböt tartalmaz, amely [idősorozat-példányok](./concepts-model-overview.md#time-series-model-instances) mennyiségét használja az üzenet hatékonyságának növelése érdekében. Bár a Time Series-példányok eszközének metaadatai valószínűleg nem változnak, gyakran hasznos tulajdonságokat biztosítanak az adatok elemzéséhez.

* A JSON két vagy több üzenetet egyesít (egy-egy-egy eszközről) egyetlen hasznos adatba, amely a sávszélességet az idő múlásával menti.

* Az egyes eszközök egyes adatsorozat-adatpontjai egyetlen **adatsor** -attribútumba vannak összevonva, ami csökkenti az egyes eszközök frissítéseinek folyamatos továbbításának szükségességét.

> [!TIP]
> Az adatok elküldéséhez és a telemetria hatékonyabbá tételéhez szükséges üzenetek számának csökkentése érdekében érdemes lehet a közös dimenzió értékeket és az idősorozat-példányok metaadatait egyetlen JSON-adattartalomba alakítani.

#### <a name="time-series-instance"></a>Idősorozat-példány 

Ismerkedjen meg közelebbről, hogy miként alakíthatja át a JSON-t optimálisan a [Time Series-példány](./concepts-model-overview.md#time-series-model-instances) használatával. 

> [!NOTE]
> Az [Idősorozat-azonosítók](./time-series-insights-update-how-to-id.md) alább találhatók: *DeviceID*.

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

Time Series Insights az előnézet Összekapcsol egy táblát (az összeolvasztás után) a lekérdezés ideje alatt. A tábla további oszlopokat, például **típust**tartalmaz.

| deviceId  | Típus | L1 | L2 | időbélyeg | series_Flow sebesség FT3/s | series_Engine olajnyomás PSI |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SZIMULÁTOR | Akkumulátorrendszer | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SZIMULÁTOR |   Akkumulátorrendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA KÖZÖS | SZIMULÁTOR |    Akkumulátorrendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

> [!NOTE]
>  Az előző táblázat a lekérdezés nézetet jelöli a [Time Series Preview Explorerben](./time-series-insights-update-explorer.md).

**Elvihető ételek**

* Az előző példában a statikus tulajdonságok Time Series Insights előzetes verzióban tárolódnak a hálózaton továbbított adatátvitelek optimalizálása érdekében.
* Time Series Insights az előnézeti idő a példányban definiált idősorozat-AZONOSÍTÓn keresztül csatlakozik a lekérdezési időponthoz.
* Két beágyazási réteg van használatban. Ez a szám a legtöbbet Time Series Insights előzetes verzió támogatja. Fontos, hogy elkerülje a mélyen beágyazott tömböket.
* Mivel a rendszer néhány mértéket használ, ezeket külön tulajdonságokként küldik el ugyanazon az objektumon belül. A példában **Series_Flow ráta PSI**, **series_Engine olajnyomás psi**, és **series_Flow Rate FT3/s** egyedi oszlopok.

>[!IMPORTANT]
> A példány mezői nem tárolódnak a telemetria. A rendszer metaadatokat tárol a Time Series-modellben.

#### <a name="example-2"></a>2. példa

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

A fenti példában az összeolvasztott tulajdonság a `data["flow"]` tulajdonsággal való elnevezési ütközést jelent `data_flow` .

Ebben az esetben a *legújabb* tulajdonság értéke felülírja a korábbiat. 

> [!TIP]
> További segítségért forduljon a Time Series Insights csapatához.

> [!WARNING] 
> * Azokban az esetekben, amikor az ismétlődő tulajdonságok egy összeolvasztás vagy egy másik mechanizmus miatt azonos (számú) esemény-adattartalomban találhatók, a rendszer a legújabb > tulajdonság értékét tárolja, a korábbi értékek felülírásával.
> * A kombinált események sorozata nem bírálja felül egymást.

## <a name="next-steps"></a>További lépések

* Az irányelvek gyakorlatba való beírásához olvassa el [Azure Time Series Insights előnézeti lekérdezés szintaxisát](./time-series-insights-query-data-csharp.md). A lekérdezési szintaxissal kapcsolatos további információkért tekintse meg az adatelérés Time Series Insights [előzetes verzió Rest APIét](https://docs.microsoft.com/rest/api/time-series-insights/preview) .

* A JSON ajánlott eljárásait kombinálhatja a [Time Series modellel](./time-series-insights-update-how-to-tsm.md).
