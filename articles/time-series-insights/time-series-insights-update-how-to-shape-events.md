---
title: Alakzat-eseményeket az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Megtudhatja, hogyan formázhatja az eseményeket Azure Time Series Insights előzetes verzióval.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e1eb0d7450e1a7f263f29b8d4657547dd85d4276
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883296"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Alakzat eseményeket az Azure Time Series Insights előzetes verziója

Ez a cikk segít formázhatja a JSON-fájlt az Azure Time Series Insights előzetes verziója a lekérdezések a hatékonyság maximalizálása érdekében.

## <a name="best-practices"></a>Ajánlott eljárások

Gondolja át, hogyan küldi el az eseményeket Time Series Insights előzetes verzióra. Nevezetesen akkor mindig:

* az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
* A data Store oly módon, amely segítséget nyújt a forgatókönyvnek megfelelően további összesítés.

A legjobb lehetséges lekérdezési teljesítmény tegye a következőket:

* Ne küldjön a szükségtelen tulajdonságait. Time Series Insights előzetes verziója a használatra vonatkozó verziójánál. Érdemes tárolni és feldolgozni az adatokat, le fogja kérdezni.
* Szolgáltatáspéldány-mezők használata a statikus adatok. Ez a gyakorlat segít elkerülni a statikus adatok küldése a hálózaton keresztül. A példány mezői, az idősorozat-modell egyik összetevője, a Time Series Insights általánosan elérhető szolgáltatásban található hivatkozási adatokhoz hasonlóan működnek. Szolgáltatáspéldány-mezők kapcsolatos további információkért lásd: [Time Series modellek](./time-series-insights-update-tsm.md).
* Megosztás dimenzió tulajdonságai között két vagy több esemény. Ez az eljárás segítségével adatokat hatékonyabban a hálózaton keresztüli küldéshez.
* Ne használja a részletes tömb beágyazást. Time Series Insights az előzetes verzió legfeljebb két olyan beágyazott tömböt támogat, amelyek objektumokat tartalmaznak. Time Series Insights előzetes verziója az üzenetek tömbök több események tulajdonság-érték párok az simítja egybe.
* Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. A küldésük külön csökkenti az események számát, és növelheti a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni.

## <a name="example"></a>Példa

Az alábbi példa egy olyan forgatókönyvet, ahol a két vagy több eszköz küldése mérések vagy jelek alapján történik. A mérések vagy jelek *áramlási sebessége*, a *motor*olajnyomás, a *hőmérséklet*és a *páratartalom*lehet.

A következő példában egyetlen Azure IoT Hub üzenet jelenik meg, amelyben a külső tömb a közös dimenzió értékeinek közös szakaszát tartalmazza. A külső tömb idő sorozatú példányok adatait használja az üzenet a hatékonyság növelése érdekében. Time Series-példány tartalmaz az eszköz metaadatait, amely minden eseményhez nem változik, de olyan hasznos tulajdonságok adatelemzés céljából. A hálózaton keresztül küldött bájtok menti, és hatékonyabbá teheti az üzenetet, fontolja meg a kötegelés közös dimenzió értékeit, és leküzdheti a Time Series Instance metadata.

### <a name="example-json-payload"></a>Példa JSON-adattartalmat

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

### <a name="time-series-instance"></a>Time Series-példány 
> [!NOTE]
> A Time Series azonosító *deviceId*.

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

Time Series Insights előzetes verziója (után az egybesimítás) tábla lekérdezési idő alatt csatlakozik. A táblázat tartalmaz további oszlopok, mint például **típus**. Az alábbi példa bemutatja, hogyan alakíthatja [](./time-series-insights-send-events.md#json) át a telemetria adatait.

| deviceId  | Típus | L1 | 2\. | időbélyeg | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR | Akkumulátor rendszer | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULÁTOR |   Akkumulátor rendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | KÖZÖS LINE_DATA | SIMULÁTOR |    Akkumulátor rendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Az előző példában vegye figyelembe a következőket:

* A statikus tulajdonságok vannak tárolva a Time Series Insights előzetes verziója a hálózaton keresztül küldött adatok optimalizálása.
* Time Series Insights előnézet adatai a példányban definiált idősorozat-azonosító használatával csatlakoznak a lekérdezési időponthoz.
* Két réteg beágyazási vannak használatban, amely a legtöbb, a Time Series Insights előzetes verziója támogatott. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.
* Mivel néhány mértékek, jelentkezéseket használ különálló tulajdonságként az objektumon belül. A példában **sorozat. Flow arány psi**, **sorozat. Motor olaj nyomás psi**, és **sorozat. Flow láb3/s sebesség** egyedi oszlop.

>[!IMPORTANT]
> A példány mezői nem tárolódnak a telemetria. A rendszer metaadatokat tárol a **Time Series**-modellben.
> Az előző táblázatban a lekérdezés nézetben jelöli.

## <a name="next-steps"></a>További lépések

- Ezeket az irányelveket a gyakorlatba, tekintse meg [Azure Time Series Insights – előzetes lekérdezési szintaxis](./time-series-insights-query-data-csharp.md). Megismerkedhet a Time Series Insights előzetes verziója adatok a lekérdezési szintaxissal kapcsolatos további REST API eléréséhez.
- Támogatott JSON-alakzatok kapcsolatos további információkért lásd: [támogatott JSON-alakzatok](./time-series-insights-send-events.md#json).
