---
title: Ajánlott eljárások a JSON-Azure Time Series Insights lekérdezések alakításához | Microsoft Docs
description: Ismerje meg, hogyan javíthatja Azure Time Series Insights lekérdezési hatékonyságát a JSON alakításával.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 386d10c8e4bd7d5f46d2081d5a26371fb37ff30f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007002"
---
# <a name="shape-json-to-maximize-query-performance"></a>A lekérdezési teljesítmény maximalizálása a JSON alakzat használatával 

Ez a cikk útmutatást nyújt a JSON formázásához a Azure Time Series Insights lekérdezések hatékonyságának maximalizálása érdekében.

## <a name="video"></a>Videó

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Ajánlott eljárások a JSON alakításához a tárolási igények kielégítése érdekében.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Ajánlott eljárások

Gondolja át, hogyan küldi el az eseményeket Time Series Insightsba. Tehát mindig:

1. az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
1. Győződjön meg arról, hogy az adatok tárolása oly módon történik, hogy az adott forgatókönyvnek megfelelő összesítéseket lehessen végrehajtani.
1. Győződjön meg arról, hogy nem éri el a maximálisan megengedett Time Series Insights:
   - 600 tulajdonságok (oszlopok) S1 környezetekhez.
   - 800 tulajdonságok (oszlopok) S2 környezetekhez.

> [!TIP]
> Tekintse át a [korlátozásokat és a tervezést](time-series-insights-update-plan.md) Azure Time Series Insights előzetes verzióban.

A következő útmutató segítséget nyújt a lehető legjobb lekérdezési teljesítmény biztosításához:

1. Ne használjon dinamikus tulajdonságokat, például egy címke AZONOSÍTÓját a tulajdonság neveként. Ez a használat a maximális tulajdonságok korlátjának eléréséhez járul hozzá.
1. Ne küldjön a szükségtelen tulajdonságait. Ha nincs szükség lekérdezési tulajdonságra, a legjobb, ha nem szeretné elküldeni. Így elkerülhetők a tárolási korlátozások.
1. A statikus adatok hálózaton keresztüli küldésének elkerüléséhez használjon [hivatkozási adatokat](time-series-insights-add-reference-data-set.md) .
1. Ossza meg a dimenzió tulajdonságait több esemény között, hogy hatékonyabban küldjön adatküldést a hálózaton.
1. Ne használja a részletes tömb beágyazást. A Time Series Insights az objektumokat tartalmazó beágyazott tömbök legfeljebb két szintjét támogatja. Time Series Insights az üzenetekben lévő tömböket több, tulajdonság érték párokkal rendelkező eseménybe.
1. Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. A küldésük külön csökkenti az események számát, és növelheti a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni. Ha több mérték is van, akkor egy adott tulajdonság értékeként való küldéssel minimálisra csökkenthető a maximálisan megengedett tulajdonságértékek elérése.

## <a name="example-overview"></a>Példa – áttekintés

Az alábbi két példa azt szemlélteti, hogyan küldhet eseményeket az előző javaslatok kiemeléséhez. Az alábbi példákban láthatja a javaslatok alkalmazásának módját.

A példák olyan forgatókönyveken alapulnak, ahol több eszköz is küld méréseket vagy jeleket. A mérések vagy jelek áramlási sebessége, a motor olajnyomás, a hőmérséklet és a páratartalom lehet. Az első példában néhány mérés van az összes eszközön. A második példában számos eszköz van, és minden eszköz sok egyedi mérést küld.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Első forgatókönyv: csak néhány mérés létezik

> [!TIP]
> Azt javasoljuk, hogy az egyes méréseket vagy jeleket külön tulajdonságként vagy oszlopként küldje el.

A következő példában egyetlen Azure IoT Hub üzenet jelenik meg, amelyben a külső tömb a közös dimenzió értékeinek közös szakaszát tartalmazza. A külső tömb a hivatkozási adatmennyiséget használja az üzenet hatékonyságának növelésére. A hivatkozási adatok olyan eszköz-metaadatokat tartalmaznak, amelyek nem változnak minden eseménnyel, de hasznos tulajdonságokat biztosítanak az adatok elemzéséhez. A közös dimenzióértékeket és a hivatkozásokat használó adatmennyiségeket a rendszer a huzalon küldött bájtok alapján menti, így hatékonyabbá válik az üzenet.

Vegye figyelembe a következő JSON-adattartalomot, amelyet a rendszer az Azure-felhőbe való küldéskor JSON-ként szerializált [IoT-üzenetet](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) küld a Time Series Insights GA-környezetnek:


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

* A (z) **deviceId**Key tulajdonsággal rendelkező hivatkozási adattábla:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | USA |

* Time Series Insights Event Table az összeolvasztás után:

   | deviceId | messageId | deviceLocation | időbélyeg | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - A **deviceId** oszlop a flotta különböző eszközeinek oszlop fejlécét szolgálja. A **deviceId** értékének kiszámításához a saját tulajdonságnév korlátozza a teljes eszközt 595 (S1 környezet esetén) vagy 795 (S2 környezet esetén) a másik öt oszloppal.
> - A felesleges tulajdonságok elkerülhetők (például a gyártmány és a modell adatai). Mivel a tulajdonságok nem lesznek lekérdezve a jövőben, így a jobb hálózati és tárolási hatékonyságot is lehetővé teszi.
> - A hivatkozási adat a hálózaton keresztül továbbított bájtok számának csökkentésére szolgál. A **messageId** és a **deviceLocation** két attribútum a Key tulajdonság **deviceId**használatával csatlakozik. Ezek az adatforgalom a telemetria-és a bejövő adatforgalom időpontjában szerepelnek, és a lekérdezés Time Series Insights tárolja őket.
> - Két beágyazási réteg van használatban, amely a Time Series Insights által támogatott beágyazások maximális száma. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.
> - A mértékek külön tulajdonságokként lesznek elküldve ugyanazon az objektumon belül, mert kevés a mérték. Itt a **sorozat. A flow aránya PSI** és **sorozat. A motor olajnyomás FT3/s** egyedi oszlopok.

## <a name="scenario-two-several-measures-exist"></a>Második forgatókönyv: több mérték létezik

> [!TIP]
> Javasoljuk, hogy a méréseket "típus", "egység" és "value" rekordok küldje el.

Példa JSON-adattartalomra:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* A Key Properties **deviceId** és a **Series. tagId**kulcsot tartalmazó hivatkozási adattábla:

   | deviceId | series.tagId | messageId | deviceLocation | type | egység |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Áramlási sebesség | FT3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Motor olajnyomás | psi |
   | FYYY | pumpRate | LINE\_DATA | USA | Áramlási sebesség | FT3/s |
   | FYYY | oilPressure | LINE\_DATA | USA | Motor olajnyomás | psi |

* Time Series Insights Event Table az összeolvasztás után:

   | deviceId | series.tagId | messageId | deviceLocation | type | egység | időbélyeg | adatsorozat. érték |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Áramlási sebesség | FT3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Motor olajnyomás | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Áramlási sebesség | FT3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Motor olajnyomás | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | USA | Áramlási sebesség | FT3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | USA | Motor olajnyomás | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - A **deviceId** és a **Series. tagId** oszlopok a különböző eszközök és címkék oszlopaiként szolgálnak a flottában. A saját attribútumaik használatával a lekérdezés a 594 (S1 környezetek esetén) vagy a 794 (S2 környezet esetén) értékre van korlátozva a többi hat oszloppal rendelkező összes eszköz esetében.
> - A szükségtelen tulajdonságokat a rendszer az első példában hivatkozott okból nem tudta elkerülni.
> - A hivatkozási adat a hálózaton keresztül továbbított bájtok számának csökkentésére szolgál a **messageId** és a **deviceLocation**egyedi párosításához használt **deviceId**bevezetésével. Az összetett Key **sorozat. tagId** a **típus** és az **egység**egyedi párosítására szolgál. Az összetett kulcs lehetővé teszi, hogy a **deviceId** és a **Series. tagId** pár négy értékre hivatkozzon: **messageId, deviceLocation, Type** és **Unit**. Ezek az adatforgalom a telemetria-adatforgalom időpontjában vannak csatlakoztatva. Ezt követően a lekérdezés Time Series Insights tárolja.
> - A rendszer két beágyazási réteget használ az első példában hivatkozott okból.

### <a name="for-both-scenarios"></a>Mindkét forgatókönyv esetén

A nagy mennyiségű lehetséges értéket tartalmazó tulajdonság esetében érdemes egyetlen oszlopon belül külön értékként elküldeni az egyes értékek új oszlopának létrehozása helyett. Az előző két példából:

  - Az első példában néhány tulajdonság több értékkel rendelkezik, ezért célszerű külön tulajdonságokat készíteni.
  - A második példában a mértékek nem egyedi tulajdonságokként vannak megadva. Ehelyett az értékek vagy mértékek tömbje a Common Series tulajdonság alatt van. Az új kulcs **tagId** el lesz küldve, amely létrehozza az új oszlopdiagram **. tagId** az összeolvasztott táblában. Az új tulajdonságok **típusát** és **egységét** a rendszer a hivatkozási adatai alapján hozza létre, így a tulajdonság korlátja nem érhető el.

## <a name="next-steps"></a>Következő lépések

- További információ [a IoT hub eszköz üzeneteinek a felhőbe való](../iot-hub/iot-hub-devguide-messages-construct.md)küldéséről.

- A Time Series Insights adatelérési REST API lekérdezési szintaxisával kapcsolatos további információért olvassa el [Azure Time Series Insights lekérdezési szintaxisát](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) .

- Megtudhatja [, hogyan alakíthat ki eseményeket](./time-series-insights-send-events.md).
