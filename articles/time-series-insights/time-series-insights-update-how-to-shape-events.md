---
title: Alakzatesemények – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg az ajánlott eljárásokat, és hogyan alakíthatja ki az eseményeket az Azure Time Insights előzetes verzióban lekérdezéshez.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650923"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Események alakítása az Azure Time Series Insights előzetes verzióval

Ez a cikk gyakorlati tanácsokat határoz meg a JSON-adatok Azure Time Series Insights betöltéséhez való terhelésének alakításához és az előzetes verziójú lekérdezések hatékonyságának maximalizálásához.

## <a name="best-practices"></a>Ajánlott eljárások

A legjobb, ha alaposan átgondolja, hogyan küld eseményeket a Time Series Insights előzetes környezetbe. 

Az általános ajánlott eljárások a következők:

* Adatok küldése a hálózaton keresztül a lehető leghatékonyabban.
* Tárolja az adatokat úgy, hogy könnyebben összesítheti azadatokat a forgatókönyvnek megfelelően.

A legjobb lekérdezési teljesítmény érdekében tartsa be az alábbi hüvelykujj-szabályokat:

* Ne küldjön felesleges tulajdonságokat. Time Series Insights előzetes számlák használat szerint. A legjobb, ha csak a lekérdezni kívánt adatokat tárolja és dolgozza fel.
* Használjon példánymezőket statikus adatokhoz. Ez a gyakorlat segít elkerülni a statikus adatok küldését a hálózaton keresztül. A Time Series Model összetevőpéldány-mezői az általánosan elérhető Time Series Insights szolgáltatás ban referenciaadatokként működnek. Ha többet szeretne megtudni a példánymezőkről, olvassa el a [Time Series Model](./time-series-insights-update-tsm.md)című.
* Dimenziótulajdonságok megosztása két vagy több esemény között. Ez a gyakorlat segít az adatok hatékonyabb küldésében a hálózaton keresztül.
* Ne használjon mélytömbökbeágyat. A Time Series Insights Előzetes verzió az objektumokat tartalmazó beágyazott tömbök legfeljebb két szintjét támogatja. A Time Series Insights Preview az üzenetektömbjeit több eseménybe olvasztja tulajdonságérték-párokkal.
* Ha csak néhány mérték létezik az összes vagy a legtöbb esemény, akkor jobb, ha ezeket a mértékeket külön tulajdonságokként ugyanazon az objektumon belül. Külön küldés csökkenti az események számát, és javíthatja a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni.

## <a name="column-flattening"></a>Oszlop összeolvasztása

A betöltés során a beágyazott objektumokat tartalmazó hasznos rakományok összeolvasztásra kerülnek, így az oszlopnév egyetlen érték egy határelhatárolóval.

* Például a következő beágyazott JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Válik: `data_flow` ha lapított.

> [!IMPORTANT]
> * Az Azure Time Series Insights`_`előzetes verzió aláhúzást ( ) használ az oszlop-határvonalzáshoz.
> * Figyelje meg az általános rendelkezésre`.`állástól való eltérést, amely inkább időszakokat ( ) használ.

Az alábbiakban részletesebb forgatókönyveket mutatunk be.

#### <a name="example-1"></a>1. példa:

A következő forgatókönyv két (vagy több) eszközzel rendelkezik, amelyek a méréseket (jeleket) küldik: *Áramlási sebesség*, *Motorolaj nyomása*, *Hőmérséklet*és *Páratartalom*.

Egyetlen Azure IoT Hub-üzenet van elküldve, ahol a külső tömb közös dimenzióértékek megosztott szakaszát tartalmazza (vegye figyelembe az üzenetben található két eszközbejegyzést).

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

**Elvihető ételek:**

* A JSON példa egy külső tömb, amely [a Time Series Instance](./time-series-insights-update-tsm.md#time-series-model-instances) adatokat az üzenet hatékonyságának növelése érdekében. Annak ellenére, hogy a Time Series Instances eszköz metaadatai valószínűleg nem változnak, gyakran hasznos tulajdonságokat biztosít az adatok elemzéséhez.

* A JSON két vagy több üzenetet egyesít (mindegyik eszközről egyet) egyetlen hasznos adatmegtakarításba a sávszélesség idővel.

* Az egyes eszközök egyedi adatsor-pontjai egyetlen **adatsor-attribútumba** vannak egyesítve, így szükség van az egyes eszközök frissítéseinek folyamatos streamelésére.

> [!TIP]
> Az adatok küldéséhez és a telemetriai adatok hatékonyabbá tételéhez szükséges üzenetek számának csökkentése érdekében fontolja meg a közös dimenzióértékek és az idősorozat-példány metaadatainak egyetlen JSON-hasznos ba kötegelését.

#### <a name="time-series-instance"></a>Idősorozat-példány 

Vessünk egy közelebbi pillantást, hogyan használhatja a [Time Series instance-ot](./time-series-insights-update-tsm.md#time-series-model-instances) a JSON optimálisabb alakításához. 

> [!NOTE]
> Az alábbi [idősorozat-azonosítók](./time-series-insights-update-how-to-id.md) *az eszközazonosítók.*

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

A Time Series Insights előzetes verziója egy táblát (összeolvasztás után) illeszt össze a lekérdezési idő alatt. A táblázat további oszlopokat tartalmaz, például **a Típus**.

| deviceId  | Típus | 1. | 2. | időbélyeg | series_Flow Ráta ft3/s | series_Engine olajnyomás psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | Szimulátor | Akkumulátor rendszer | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | Szimulátor |   Akkumulátor rendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA GYAKORI | Szimulátor |    Akkumulátor rendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Az előző tábla a Time [Series Preview Explorer](./time-series-insights-update-explorer.md)lekérdezési nézetét jelöli.

**Elvihető ételek:**

* Az előző példában a statikus tulajdonságok a Time Series Insights előzetes verzióban tárolódnak a hálózaton keresztül küldött adatok optimalizálása érdekében.
* A Time Series Insights előzetes adatai lekérdezési időben a példányban definiált Idősorozat-azonosítón keresztül kapcsolódnak egymáshoz.
* A beágyazás két rétegét használjuk. Ez a szám a time series insights előzetes verzió ban leginkább támogatott. Fontos, hogy elkerülje a mélyen beágyazott tömböket.
* Mivel kevés mérték van, a rendszer külön tulajdonságként küldi el őket ugyanazon az objektumon belül. A példában **series_Flow Rate psi**, **series_Engine Oil Pressure psi**és **series_Flow Rate ft3/s** egyedi oszlopok.

>[!IMPORTANT]
> A példánymezők nem tárolódnak telemetriai adatokkal. Metaadatokkal vannak tárolva a Time Series modellben.

#### <a name="example-2"></a>2. példa

Fontolja meg a következő JSON:

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

A fenti példában a `data["flow"]` lapított tulajdonság elnevezési `data_flow` ütközést okozna a tulajdonsággal.

Ebben az esetben a *legutóbbi* tulajdonságérték felülírja a korábbit. 

> [!TIP]
> További segítségért forduljon a Time Series Insights csapatához!

> [!WARNING] 
> * Azokban az esetekben, ahol a duplikált tulajdonságok azonos (egyes számú) esemény hasznos terhelésben vannak jelen az összeolvasztás vagy más mechanizmus miatt, a rendszer a legújabb > tulajdonságértékét tárolja, felülírva a korábbi értékeket.
> * A kombinált események sorozata nem bírálja felül egymást.

## <a name="next-steps"></a>További lépések

* Az irányelvek gyakorlatba való átlépéséhez olvassa el az [Azure Time Series Insights előzetes lekérdezési szintaxisát.](./time-series-insights-query-data-csharp.md) Az adateléréshez való hozzáféréshez a Time Series Insights [preview REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) lekérdezési szintaxisáról olvashat bővebben.

* A JSON bevált módszereinek kombinálása [a Hogyan idősorozat-modellel.](./time-series-insights-update-how-to-tsm.md)
