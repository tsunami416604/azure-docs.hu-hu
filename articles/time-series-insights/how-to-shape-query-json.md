---
title: Gyakorlati tanácsok a JSON – Azure Time Series Insights-lekérdezések alakításához | Microsoft dokumentumok
description: Ismerje meg, hogyan javíthatja az Azure Time Series Insights lekérdezési hatékonyság a JSON kialakításával.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 8232fd4c2a1e17800c96854b4ba7298e57ed84b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989892"
---
# <a name="shape-json-to-maximize-query-performance"></a>A JSON alakítás a lekérdezési teljesítmény maximalizálásához 

Ez a cikk útmutatást nyújt a JSON formálásához az Azure Time Series Insights-lekérdezések hatékonyságának maximalizálása érdekében.

## <a name="video"></a>Videó

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Ismerje meg a JSON tárolási igényeinek megfelelő formázására vonatkozó gyakorlati tanácsokat.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Ajánlott eljárások

Gondolja át, hogyan küld eseményeket a Time Series Insights-nak. Nevezetesen, mindig:

1. Adatok küldése a hálózaton keresztül a lehető leghatékonyabban.
1. Győződjön meg arról, hogy az adatok tárolása oly módon, hogy a forgatókönyvnek megfelelő összesítéseket hajthass végre.
1. Győződjön meg arról, hogy nem éri el a Time Series Insights maximális tulajdonságkorlátait:
   - 600 tulajdonság (oszlop) S1 környezetekben.
   - 800 tulajdonság (oszlop) S2 környezetekben.

> [!TIP]
> Tekintse át [a korlátokat és a tervezést](time-series-insights-update-plan.md) az Azure Time Series Insights előzetes verziójában.

Az alábbi útmutató segít a lehető legjobb lekérdezési teljesítmény biztosításában:

1. Ne használjon dinamikus tulajdonságokat, például címkeazonosítót tulajdonságnévként. Ez a használat hozzájárul a maximális tulajdonságkorlát eléréséhez.
1. Ne küldjön felesleges tulajdonságokat. Ha egy lekérdezési tulajdonság nem kötelező, a legjobb, ha nem küldi el. Így elkerülheti a tárolási korlátozásokat.
1. A [hivatkozási adatok segítségével](time-series-insights-add-reference-data-set.md) elkerülheti a statikus adatok hálózaton keresztüli küldését.
1. A dimenziótulajdonságok at több esemény között oszthatja meg, így hatékonyabban küldhet adatokat a hálózaton keresztül.
1. Ne használjon mélytömbökbeágyat. A Time Series Insights legfeljebb két objektumot tartalmazó beágyazott tömbszintet támogat. A Time Series Insights az üzenetekben lévő tömböket több, tulajdonságérték-párral rendelkező eseménybe olvasztja.
1. Ha csak néhány mérték létezik az összes vagy a legtöbb esemény, akkor jobb, ha ezeket a mértékeket külön tulajdonságokként ugyanazon az objektumon belül. Külön küldés csökkenti az események számát, és javíthatja a lekérdezési teljesítményt, mert kevesebb eseményt kell feldolgozni. Ha több méria van, ha egyetlen tulajdonságban értékként küldi el őket, minimálisra csökken a maximális tulajdonságkorlát elérése.

## <a name="example-overview"></a>Példa – áttekintés

Az alábbi két példa bemutatja, hogyan küldhet eseményeket a korábbi javaslatok kiemeléséhez. Az egyes példákat követve áttekintheti a javaslatok alkalmazásának módját.

A példák egy olyan forgatókönyvön alapulnak, amelyben több eszköz küld méréseket vagy jeleket. A mérések vagy jelek lehetnek áramlási sebesség, motorolaj nyomás, hőmérséklet és páratartalom. Az első példában van néhány mérés az összes eszközön. A második példa számos eszközt, és minden eszköz küld sok egyedi méréseket.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Első forgatókönyv: Csak néhány mérés létezik

> [!TIP]
> Javasoljuk, hogy minden mérést vagy jelet külön tulajdonságként vagy oszlopban küldjön el.

A következő példában egyetlen Azure IoT Hub-üzenet található, ahol a külső tömb közös dimenzióértékek megosztott szakaszát tartalmazza. A külső tömb hivatkozási adatokat használ az üzenet hatékonyságának növelése érdekében. A referenciaadatok olyan eszközmetaadatokat tartalmaznak, amelyek nem változnak minden eseménynél, de hasznos tulajdonságokat biztosítaz adatok elemzéséhez. A közös dimenzióértékek kötegelése és a referenciaadatok használata a vezetéken keresztül küldött bájtokon ment, ami hatékonyabbá teszi az üzenetet.

Vegye figyelembe a következő JSON-tartalom elküldésével a Time Series Insights GA-környezet ben egy [IoT-eszközüzenet-objektum,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) amely szerializált JSON-ba, amikor elküldi az Azure-felhőbe:


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

* Referenciaadat-tábla, amely rendelkezik a kulcs tulajdonság **deviceId:**

   | deviceId | messageId | eszközelhelyezkedése |
   | --- | --- | --- |
   | FXXX | SORADATOK\_ | EU |
   | FYYY között | SORADATOK\_ | USA |

* Time Series Insights eseménytábla összeolvasztás után:

   | deviceId | messageId | eszközelhelyezkedése | időbélyeg | Sorozat. Áramlási sebesség ft3/s | Sorozat. Motorolaj nyomás psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | SORADATOK\_ | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | SORADATOK\_ | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY között | SORADATOK\_ | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - A **deviceId** oszlop szolgál az oszlop fejléce a különböző eszközök a flotta. Az **eszközazonosító** értékének beállítása saját tulajdonságnévvel az összes eszközre (S1 környezetek esetén) vagy 795-re (S2 környezetben) korlátozza a másik öt oszlopot.
> - A szükségtelen tulajdonságok elkerülhetők (például a gyártmányra és a modellre vonatkozó információk). Mivel a tulajdonságok a jövőben nem lesznek lekérdezve, kiküszöbölésük lehetővé teszi a jobb hálózati és tárolási hatékonyságot.
> - A referenciaadatok a hálózaton keresztül átvitt bájtok számának csökkentésére szolgálnak. A **messageId** és **az deviceLocation** két attribútuma az eszközazonosító kulcstulajdonság-azonosítójával **csatlakozik.** Ezeket az adatokat a telemetriai adatok bejövő időben, és ezután tárolja a Time Series Insights lekérdezése.
> - A beágyazás két rétege használatos, amely a Time Series Insights által támogatott beágyazás maximális mennyisége. Fontos, hogy elkerülje a mélyen beágyazott tömböket.
> - A mértékek külön tulajdonságokként kerülnek elküldésre ugyanazon az objektumon belül, mivel kevés mérték van. Tessék, **sorozat. Flow Rate psi** és **sorozat. Motorolaj nyomás ft3 / s** egyedi oszlopok.

## <a name="scenario-two-several-measures-exist"></a>Második forgatókönyv: Több intézkedés létezik

> [!TIP]
> Javasoljuk, hogy a mértékegységeket "típus", "egység" és "érték" értékként küldje el.

Példa JSON hasznos teher:

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

* Referenciaadat-tábla, amely az **eszközazonosító** és **a series.tagId**kulcstulajdonságokat tartalmaz:

   | deviceId | sorozat.tagId | messageId | eszközelhelyezkedése | type | egység |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SORADATOK\_ | EU | Áramlási sebesség | ft3/s |
   | FXXX | olajNyomás | SORADATOK\_ | EU | Motorolaj nyomása | psi |
   | FYYY között | pumpRate | SORADATOK\_ | USA | Áramlási sebesség | ft3/s |
   | FYYY között | olajNyomás | SORADATOK\_ | USA | Motorolaj nyomása | psi |

* Time Series Insights eseménytábla összeolvasztás után:

   | deviceId | sorozat.tagId | messageId | eszközelhelyezkedése | type | egység | időbélyeg | sorozat.érték |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SORADATOK\_ | EU | Áramlási sebesség | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | olajNyomás | SORADATOK\_ | EU | Motorolaj nyomása | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | SORADATOK\_ | EU | Áramlási sebesség | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | olajNyomás | SORADATOK\_ | EU | Motorolaj nyomása | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY között | pumpRate | SORADATOK\_ | USA | Áramlási sebesség | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY között | olajNyomás | SORADATOK\_ | USA | Motorolaj nyomása | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - Az **eszközök és** **a series.tagId** oszlopok fejléceként szolgálnak a flotta különböző eszközeihez és címkéihez. Mindegyik saját attribútumként való használata a lekérdezést 594 (S1 környezetben) vagy 794 (S2 környezetben) összes eszközre korlátozza a másik hat oszlopmal.
> - A szükségtelen tulajdonságokat elkerülték az első példában említett ok miatt.
> - A referenciaadatok segítségével csökken a hálózaton keresztül átvitt bájtok száma az **eszközazonosító**bevezetésével , amelyet az egyedi **messageId** és **deviceLocation**párhoz használnak . Az összetett **kulcssorozat.tagId** az egyedi **típus-** és **egységpárhoz**használatos. Az összetett kulcs lehetővé teszi, hogy az **eszközazonosító** és **a series.tagId** pár négy értékre utaljon: **messageId, deviceLocation, type** és **unit**. Ezek az adatok a telemetriai adatok at ingress time. Ezután a Time Series Insights lekérdezése tárolja.
> - Az első példában említett ok miatt a beágyazás két rétege használatos.

### <a name="for-both-scenarios"></a>Mindkét esetben

Ha egy tulajdonság nagy számú lehetséges értékkel rendelkező tulajdonság, a legjobb, ha külön értékekként küld egy oszlopon belül, ahelyett, hogy minden egyes értékhez új oszlopot hozna létre. Az előző két példa:

  - Az első példában néhány tulajdonság több értéktel rendelkezik, ezért célszerű mindegyiket külön tulajdonságot készíteni.
  - A második példában a mértékek nincsenek megadva egyedi tulajdonságokként. Ehelyett értékek vagy mértéktömbök egy közös sorozat tulajdonság alatt. A rendszer elküldi az új **kulcstagId azonosítót,** amely létrehozza az új **oszlopsorozatot.tagId** az összeolvasztott táblázatban. Az új tulajdonságok **típusa** és **egysége** hivatkozási adatok használatával jön létre, így a tulajdonságkorlát nem érhető el.

## <a name="next-steps"></a>További lépések

- További információ az [IoT Hub-eszközüzenetek felhőbe küldéséről.](../iot-hub/iot-hub-devguide-messages-construct.md)

- Olvassa el [az Azure Time Series Insights lekérdezési szintaxisát,](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) ha többet szeretne megtudni a Time Series Insights-adatok eléréséhez a REST API lekérdezési szintaxisáról.

- Ismerje [meg, hogyan alakíthatja az eseményeket.](./time-series-insights-send-events.md)
