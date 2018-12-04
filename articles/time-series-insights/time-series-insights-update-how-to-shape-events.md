---
title: Hogyan alakzat eseményeket az Azure Time Series Insights (előzetes verzió) |} A Microsoft Docs
description: Megértse, hogyan formázhatja az eseményeket az Azure Time Series Insights (előzetes verzió)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: edc1dac05a8ab4281eee3ee0eb4c5e6b7571b404
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856436"
---
# <a name="shaping-events-with-azure-time-series-insights-preview"></a>Alakításra eseményeket az Azure Time Series Insights (előzetes verzió)

Ez a cikk ismerteti, amelyeket formázhat JSON útmutatást, maximalizálása érdekében a hatékonyság érdekében, még az Azure Time Series Insights (előzetes verzió) lekérdezéseket.

## <a name="best-practices"></a>Ajánlott eljárások

> [!NOTE]
> Az Azure TSI (előzetes verzió) az S1/S2 600 – 800 tulajdonság korlátozásai nem vonatkoznak.

Fontos, milyen események küldése az Azure TSI állításoknak. Nevezetesen akkor mindig:

1. az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
1. Győződjön meg arról, az adatok olyan módon, amely lehetővé teszi, hogy a forgatókönyvnek megfelelő összesítések tárolása.

Az alábbi útmutató segít a legjobb lehetséges lekérdezési teljesítmény érdekében:

1. Ne küldjön a szükségtelen tulajdonságait. TSI (előzetes verzió) számolunk fel díjat a használat és a egy ajánlott tárolni, és lekérdezheti, adatok feldolgozásához.
1. Statikus adatok esetében szolgáltatáspéldány-mezők használatával elkerülheti a statikus adatok küldése a hálózaton keresztül. Szolgáltatáspéldány-mezők, az idősorozat-modellben összetevője működik, mint az általánosan elérhető a TSI szolgáltatás adatokra hivatkoznak. Pole instance kapcsolatos további információkért olvassa el [Time Series modellek](./time-series-insights-update-tsm.md).
1. Megosztás dimenzió tulajdonságai között több esemény adatok hatékonyabb a hálózaton keresztüli küldéshez.
1. Ne használja a részletes tömb beágyazást. A TSI objektumokat tartalmazó beágyazott tömbök legfeljebb két szintet támogat. A TSI tömbök az üzeneteket, a tulajdonság-érték párokat a több események simítja egybe.
1. Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. Külön-külön elküldheti csökkenti az események számát, és előfordulhat, hogy elérhetővé lekérdezések több nagy teljesítményű, mint a kevesebb eseménnyel kell feldolgozni.

## <a name="example"></a>Példa

A példában egy olyan forgatókönyvet, ahol több eszköz küldése mérések vagy jelek alapján történik. Mérések vagy jelek sikerült **Flow arány**, **motor olaj nyomás**, **hőmérséklet**, és **páratartalom**.

A következő példában van egy IoT Hub üzenet, ahol a külső tömb tartalmaz-e a közös dimenzióértékek egy közös szakaszhoz. A külső tömb idő sorozatú példányok adatait használja az üzenet a hatékonyság növelése érdekében. Time Series-példány tartalmazza az eszközök metaadatait, minden eseményhez nem változik, de hasznos tulajdonságok biztosít az adatok elemzésére. Mind közös dimenzióértékek kötegelés és leküzdheti a Time Series Instance metadata, menti a hálózaton, így még hatékonyabbá teszi az üzenetet küldött bájtok száma.

Példa JSON-adattartalom:

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

Time Series-példány (Megjegyzés: a **Time Series azonosító** van *deviceId*):

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

A TSI tartományhoz (után az egybesimítás) tábla lekérdezési idő alatt. Írja be például a további oszlopok a tábla tartalmazza. Ez a példa bemutatja, hogyan átalakíthatja a telemetriai adatok:

| deviceId  | Típus | L1 | 2. | időbélyeg | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR REVOLT | Akkumulátor rendszer | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SIMULÁTOR |    Akkumulátor rendszer |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | KÖZÖS LINE_DATA | SIMULÁTOR |    Akkumulátor rendszer |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Megjegyzés: az előző példában a következőket:

* A statikus tulajdonságok formájában a hálózaton keresztül küldött adatok optimalizálása a TSI tárolja.
* A TSI-adatok használatával lekérdezéskor csatlakozik a *timeSeriesId* példányában meghatározott.
* Két réteg beágyazási vannak használatban, amely a beágyazási TSI által támogatott maximális számát. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.
* Mértékek belül ugyanarra az objektumra, különálló tulajdonságként érkeznek, mivel néhány mértékeket. Itt **sorozat. Flow arány psi**, sorozat **motor olaj nyomás**, és **láb3/s** egyedi oszlop.

>[!IMPORTANT]
> Szolgáltatáspéldány-mezők nem tárolja a telemetriai adatokat, azokat a metaadatokat tartalmazó tárolja a **Idősorozat-modell**.
> A fenti táblázat egy lekérdezési nézet.

## <a name="next-steps"></a>További lépések

Ezeket az irányelveket a gyakorlatba, tekintse meg [Azure TSI lekérdezési szintaxis](./time-series-insights-query-data-csharp.md) további információ a lekérdezés szintaxisa a TSI adatelérési REST API számára.
