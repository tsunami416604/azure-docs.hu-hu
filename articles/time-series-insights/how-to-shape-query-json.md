---
title: Ajánlott eljárások a JSON-Azure Time Series Insights lekérdezések alakításához | Microsoft Docs
description: Ismerje meg, hogyan javíthatja Azure Time Series Insights lekérdezési hatékonyságát a JSON alakításával.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 8232fd4c2a1e17800c96854b4ba7298e57ed84b7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989892"
---
# <a name="shape-json-to-maximize-query-performance"></a>A lekérdezési teljesítmény maximalizálása a JSON alakzat használatával 

Ez a cikk útmutatást nyújt a JSON formázásához a Azure Time Series Insights lekérdezések hatékonyságának maximalizálása érdekében.

## <a name="video"></a>Videó

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Ajánlott eljárások a JSON alakításához a tárolási igények kielégítése érdekében.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Ajánlott eljárások

Gondolja át, hogyan küldi el az eseményeket Time Series Insightsba. Tehát mindig:

1. Az adatküldés a hálózaton keresztül a lehető leghatékonyabban.
1. Győződjön meg arról, hogy az adatok tárolása oly módon történik, hogy az adott forgatókönyvnek megfelelő összesítéseket lehessen végrehajtani.
1. Győződjön meg arról, hogy nem éri el a maximálisan megengedett Time Series Insights:
   - 600 tulajdonságok (oszlopok) S1 környezetekhez.
   - 800 tulajdonságok (oszlopok) S2 környezetekhez.

> [!TIP]
> Tekintse át a [korlátozásokat és a tervezést](time-series-insights-update-plan.md) Azure Time Series Insights előzetes verzióban.

A következő útmutató segítséget nyújt a lehető legjobb lekérdezési teljesítmény biztosításához:

1. Ne használjon dinamikus tulajdonságokat, például egy címke AZONOSÍTÓját a tulajdonság neveként. Ez a használat a maximális tulajdonságok korlátjának eléréséhez járul hozzá.
1. Ne küldjön felesleges tulajdonságokat. Ha nincs szükség lekérdezési tulajdonságra, a legjobb, ha nem szeretné elküldeni. Így elkerülhetők a tárolási korlátozások.
1. A statikus adatok hálózaton keresztüli küldésének elkerüléséhez használjon [hivatkozási adatokat](time-series-insights-add-reference-data-set.md) .
1. Ossza meg a dimenzió tulajdonságait több esemény között, hogy hatékonyabban küldjön adatküldést a hálózaton.
1. Ne használjon mély tömbös beágyazást. A Time Series Insights az objektumokat tartalmazó beágyazott tömbök legfeljebb két szintjét támogatja. Time Series Insights az üzenetekben lévő tömböket több, tulajdonság érték párokkal rendelkező eseménybe.
1. Ha csak néhány mérték létezik az összes vagy a legtöbb eseménynél, érdemes elküldenie ezeket a mértékeket különálló tulajdonságokként ugyanazon az objektumon belül. A küldésük külön csökkenti az események számát, és növelheti a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni. Ha több mérték is van, akkor egy adott tulajdonság értékeként való küldéssel minimálisra csökkenthető a maximálisan megengedett tulajdonságértékek elérése.

## <a name="example-overview"></a>Példa – áttekintés

Az alábbi két példa azt szemlélteti, hogyan küldhet eseményeket az előző javaslatok kiemeléséhez. Az egyes példákat követve áttekintheti a javaslatok alkalmazásának módját.

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

   | deviceId | messageId | DeviceLocation |
   | --- | --- | --- |
   | FXXX | SOROK\_ | EU |
   | FYYY | SOROK\_ | Egyesült Államok |

* Time Series Insights Event Table az összeolvasztás után:

   | deviceId | messageId | DeviceLocation | időbélyeg | sorozat. Áramlási sebesség FT3/s | sorozat. Motor olajnyomás PSI |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | SOROK\_ | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34,7 |
   | FXXX | SOROK\_ | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49,2 |
   | FYYY | SOROK\_ | Egyesült Államok | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22,2 |

> [!NOTE]
> - A **deviceId** oszlop a flotta különböző eszközeinek oszlop fejlécét szolgálja. A **deviceId** értékének kiszámításához a saját tulajdonságnév korlátozza a teljes eszközt 595 (S1 környezet esetén) vagy 795 (S2 környezet esetén) a másik öt oszloppal.
> - A felesleges tulajdonságok elkerülhetők (például a gyártmány és a modell adatai). Mivel a tulajdonságok nem lesznek lekérdezve a jövőben, így a jobb hálózati és tárolási hatékonyságot is lehetővé teszi.
> - A hivatkozási adat a hálózaton keresztül továbbított bájtok számának csökkentésére szolgál. A **messageId** és a **deviceLocation** két attribútum a Key tulajdonság **deviceId**használatával csatlakozik. Ezek az adatforgalom a telemetria-és a bejövő adatforgalom időpontjában szerepelnek, és a lekérdezés Time Series Insights tárolja őket.
> - Két beágyazási réteg van használatban, amely a Time Series Insights által támogatott beágyazások maximális száma. Fontos, hogy elkerülje a mélyen beágyazott tömböket.
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

   | deviceId | adatsorozat. tagId | messageId | DeviceLocation | type | Egység |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SOROK\_ | EU | Áramlási sebesség | FT3/s |
   | FXXX | oilPressure | SOROK\_ | EU | Motor olajnyomás | psi |
   | FYYY | pumpRate | SOROK\_ | Egyesült Államok | Áramlási sebesség | FT3/s |
   | FYYY | oilPressure | SOROK\_ | Egyesült Államok | Motor olajnyomás | psi |

* Time Series Insights Event Table az összeolvasztás után:

   | deviceId | adatsorozat. tagId | messageId | DeviceLocation | type | Egység | időbélyeg | adatsorozat. érték |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SOROK\_ | EU | Áramlási sebesség | FT3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | SOROK\_ | EU | Motor olajnyomás | psi | 2018-01-17T01:17:00Z | 34,7 |
   | FXXX | pumpRate | SOROK\_ | EU | Áramlási sebesség | FT3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | SOROK\_ | EU | Motor olajnyomás | psi | 2018-01-17T01:17:00Z | 49,2 |
   | FYYY | pumpRate | SOROK\_ | Egyesült Államok | Áramlási sebesség | FT3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | SOROK\_ | Egyesült Államok | Motor olajnyomás | psi | 2018-01-17T01:18:00Z | 22,2 |

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
