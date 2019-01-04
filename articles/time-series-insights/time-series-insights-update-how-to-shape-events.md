---
title: Alakzat-eseményeket az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Megtudhatja, hogyan formázhatja az események az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: eb398ad621167ad9f9b245fb8aa98c6942b87668
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557427"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Alakzat eseményeket az Azure Time Series Insights előzetes verziója

Ez a cikk segít formázhatja a JSON-fájlt az Azure Time Series Insights előzetes verziója a lekérdezések a hatékonyság maximalizálása érdekében.

## <a name="best-practices"></a>Ajánlott eljárások

Fontos, milyen események küldése Time Series Insights előzetes verziója a állításoknak. Nevezetesen akkor mindig:

* az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
* A data Store oly módon, amely segítséget nyújt a forgatókönyvnek megfelelően további összesítés.

A legjobb lehetséges lekérdezési teljesítmény tegye a következőket:

* Ne küldjön a szükségtelen tulajdonságait. Time Series Insights előzetes verziója a használatra vonatkozó verziójánál. Érdemes tárolni és feldolgozni az adatokat, le fogja kérdezni.
* Szolgáltatáspéldány-mezők használata a statikus adatok. Ez a gyakorlat segít elkerülni a statikus adatok küldése a hálózaton keresztül. Szolgáltatáspéldány-mezők, az idősorozat-modellben összetevője működik, mint a Time Series Insights általánosan elérhető szolgáltatás adatokra hivatkoznak. Szolgáltatáspéldány-mezők kapcsolatos további információkért lásd: [Time Series modellek](./time-series-insights-update-tsm.md).
* Megosztás dimenzió tulajdonságai között két vagy több esemény. Ez az eljárás segítségével adatokat hatékonyabban a hálózaton keresztüli küldéshez.
* Ne használja a részletes tömb beágyazást. Time Series Insights előzetes verziója objektumokat tartalmazó beágyazott tömbök legfeljebb két szintet támogat. Time Series Insights előzetes verziója az üzenetek tömbök több események tulajdonság-érték párok az simítja egybe.
* Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. Külön-külön elküldheti csökkenti az események számát, és kevesebb eseménnyel kell feldolgozni, mert az eljárás lehet, hogy győződjön meg arról, lekérdezések több nagy teljesítményt nyújtva.

## <a name="example"></a>Példa

Az alábbi példa egy olyan forgatókönyvet, ahol a két vagy több eszköz küldése mérések vagy jelek alapján történik. A mértékek vagy jelek sikerült *Flow arány*, *motor olaj nyomás*, *hőmérséklet*, és *páratartalom*.

A következő példában van egy IoT Hub üzenet, ahol a külső tömb tartalmaz-e a közös dimenzióértékek egy közös szakaszhoz. A külső tömb idő sorozatú példányok adatait használja az üzenet a hatékonyság növelése érdekében. Time Series-példány tartalmaz az eszköz metaadatait, amely minden eseményhez nem változik, de olyan hasznos tulajdonságok adatelemzés céljából. A hálózaton keresztül küldött bájtok menti, és hatékonyabbá teheti az üzenetet, fontolja meg a kötegelés közös dimenzió értékeit, és leküzdheti a Time Series Instance metadata.

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
                "Flow Rate psi": 0.58015072345733643,
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

Time Series Insights előzetes verziója (után az egybesimítás) tábla lekérdezési idő alatt csatlakozik. A táblázat tartalmaz további oszlopok, mint például **típus**. A következő példa bemutatja, hogyan zajlik [alakzat](./time-series-insights-send-events.md#json) a telemetriai adatok:

| deviceId  | Típus | L1 | 2. | időbélyeg | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR REVOLT | Akkumulátor rendszer | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SIMULÁTOR |    Akkumulátor rendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | KÖZÖS LINE_DATA | SIMULÁTOR |    Akkumulátor rendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Az előző példában vegye figyelembe a következőket:

* A statikus tulajdonságok vannak tárolva a Time Series Insights előzetes verziója a hálózaton keresztül küldött adatok optimalizálása.
* Time Series Insights előzetes verziójában lévő adatokat a lekérdezéskor csatlakozik a *timeSeriesId* , amely a példány van definiálva.
* Két réteg beágyazási vannak használatban, amely a legtöbb, a Time Series Insights előzetes verziója támogatott. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.
* Mivel néhány mértékek, jelentkezéseket használ különálló tulajdonságként az objektumon belül. A példában **sorozat. Flow arány psi**, **sorozat. Motor olaj nyomás psi**, és **sorozat. Flow láb3/s sebesség** egyedi oszlop.

>[!IMPORTANT]
> Szolgáltatáspéldány-mezők nem tárolja a telemetriai adatokat. A metaadatok tárolódnak a **Idősorozat-modell**.
> Az előző táblázatban a lekérdezés nézetben jelöli.

## <a name="next-steps"></a>További lépések

Ezeket az irányelveket a gyakorlatba, tekintse meg [Azure Time Series Insights – előzetes lekérdezési szintaxis](./time-series-insights-query-data-csharp.md). Megismerkedhet a Time Series Insights előzetes verziója adatok a lekérdezési szintaxissal kapcsolatos további REST API eléréséhez.

Támogatott JSON-alakzatok kapcsolatos további információkért lásd: [támogatott JSON-alakzatok](./time-series-insights-send-events.md#json).
