---
title: Ajánlott eljárások az Azure Time Series Insights-lekérdezések JSON alakító |} A Microsoft Docs
description: Ismerje meg, hogyan javíthatja az Azure Time Series Insights-lekérdezés hatékonyságát.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244463"
---
# <a name="shape-json-to-maximize-query-performance"></a>Alakzat JSON lekérdezési teljesítmény maximalizálása érdekében 

Ez a cikk útmutatást nyújt az alakzat maximalizálható a hatékonyság érdekében az Azure Time Series Insights-lekérdezések JSON való.

## <a name="video"></a>Videó

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Ismerje meg, ajánlott eljárások a tárolási igényei JSON alakításra.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Ajánlott eljárások
Milyen eseményeket küldeni a Time Series Insights gondolja. Nevezetesen hogy mindig:

1. az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
1. Ellenőrizze, hogy adatai olyan módon, hogy az adott forgatókönyvnek megfelelő összesítések hajthat végre.
1. Győződjön meg arról, hogy nem érik el a Time Series Insights maximális tulajdonság korlátai:
   - S1 környezetek 600 tulajdonságait (oszlop).
   - S2 környezetek 800 tulajdonságait (oszlop).

Az alábbi útmutató segít a legjobb lehetséges lekérdezési teljesítmény biztosítása érdekében:

1. Ne használjon dinamikus tulajdonságait, például a címke azonosítója-tulajdonság neveként. Ez a használati hozzájárul a Tulajdonságok maximális korlát elérése.
1. Ne küldjön a szükségtelen tulajdonságait. Ha egy lekérdezést a tulajdonság nem kötelező, nem célszerű küldje el. Így elkerülheti a storage korlátozásaival.
1. Használat [referenciaadatok](time-series-insights-add-reference-data-set.md) elkerülése érdekében a statikus adatok küldése a hálózaton keresztül.
1. Megosztás dimenzió tulajdonságai között több esemény adatok hatékonyabb a hálózaton keresztüli küldéshez.
1. Ne használja a részletes tömb beágyazást. A Time Series Insights objektumokat tartalmazó beágyazott tömbök legfeljebb két szintet támogat. A Time Series Insights tömbök üzenetek a tulajdonság-érték párok több események simítja egybe.
1. Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. Külön-külön elküldheti csökkenti az események számát, és előfordulhat, hogy javíthatja a lekérdezések teljesítményét, mert a kevesebb eseménnyel kell feldolgozni. Ha több mértékek, elküldheti az egy-egy tulajdonság értékeként minimálisra csökkenti a tulajdonság maximális korlát elérése lehetőségét.

## <a name="example-overview"></a>Példa – áttekintés

Az alábbi két példák bemutatják, hogyan küldhet eseményeket a fenti javaslatok kiemeléséhez. Következő valamennyi példa látható a javaslatok alkalmazott hogyan.

A példák egy forgatókönyvet, ahol több eszköz küldése mérések vagy jelek alapulnak. Flow arány, motor olaj nyomás, hőmérsékleti és páratartalom mérések vagy jelek is lehet. Az első példában nincsenek néhány mérések összes eszközön. A második példában sok eszköz van, és minden eszköz által számos egyedi mértékek.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Egy példa: Csak néhány mérések létezik

> [!TIP]
> Azt javasoljuk, hogy küldjön minden mérték vagy jel külön tulajdonság vagy oszlop.

A következő példában van egy adott Azure IoT Hub üzenet, ahol a külső tömb tartalmaz-e a közös dimenzióértékek egy közös szakaszhoz. A külső tömb referenciaadatok használja az üzenet a hatékonyság növelése érdekében. Referenciaadatok tartalmaz az eszköz metaadatait, amelyek nem változnak, az egyes események, de biztosítja a hasznos tulajdonságai adatelemzésre. Közös dimenzióértékek kötegelés és adatok takarít meg a hálózaton keresztül küldött bájtok referencia alkalmazó, így az üzenet hatékonyabb.

Példa JSON-adattartalom:

```json
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

* A tulajdonsággal rendelkező hivatkozási adattábla **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | SOR\_ADATOK | EU |
   | PÉÉÉ | SOR\_ADATOK | USA |

* Time Series Insights esemény tábla, egybesimítását után:

   | deviceId | messageId | deviceLocation | timestamp | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | PÉÉÉ | SOR\_ADATOK | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

A két táblázat megjegyzések:

- A **deviceId** oszlop szolgál a különböző eszközökhöz egy járműflotta az az oszlop fejlécére. DeviceId értékét a saját tulajdonságnév korlátozza az összes eszköz 595 (az S1-környezetek) vagy az egyéb öt oszlopokat (az S2-környezetek) 795.
- A felesleges tulajdonságok elkerülhetők a, például a gyártmány és modell információt. A tulajdonságok a jövőben nem kérdezhető le, mert megszüntetése lehetővé teszi, hogy jobb hálózati és tárolási hatékonyságot.
- Referenciaadatok segítségével csökkentheti a hálózaton keresztül továbbított bájtok száma. A két attribútum **üzenetazonosító** és **deviceLocation** összekapcsolhatók a kulcstulajdonság a **deviceId**. Az adatok bejövő időpontban a telemetriai adatokat a tartományhoz csatlakozik, és aztán a Time Series Insights lekérdezéséhez.
- Két réteg beágyazási használnak, amely az a maximális beágyazási Time Series Insights támogatja. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.
- Mértékek különálló tulajdonságként az objektumon belüli érkeznek, mert néhány mértékeket. Itt **sorozat. Flow arány psi** és **sorozat. A motor olaj nyomás láb3/s** egyedi oszlop.

## <a name="scenario-two-several-measures-exist"></a>Második forgatókönyv: Több mértékek létezik

> [!TIP]
> Azt javasoljuk, hogy küldjön mérések, "type", "egység" és "érték" rekordokat.

Példa JSON-adattartalom:

```json
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

* Kulcs tulajdonságai a referencia-adattábla **deviceId** és **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | Egység |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s |
   | FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi |
   | PÉÉÉ | pumpRate | SOR\_ADATOK | USA | A folyamat arány | láb3/s |
   | PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Olaj nyomás motor | psi |

* Time Series Insights esemény tábla, egybesimítását után:

   | deviceId | series.tagId | messageId | deviceLocation | type | Egység | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi | 2018-01-17T01:17:00Z | 49.2 |
   | PÉÉÉ | pumpRate | SOR\_ADATOK | USA | A folyamat arány | láb3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Olaj nyomás motor | psi | 2018-01-17T01:18:00Z | 22.2 |

A két táblázat megjegyzések:

- Az oszlopok **deviceId** és **series.tagId** az oszlopfejléceket a különböző eszközökön és a egy járműflotta a címkék szolgál. A többi hat oszlopokkal rendelkező eszközök használatával minden egyes, a saját attribútum korlátok a lekérdezés (az S1-környezetek) 594 vagy 794 (az S2-környezetek) teljes száma.
- a felesleges tulajdonságok lettek kerülni, az okból az első példában szereplő.
- Referenciaadatok segítségével csökkentse a bevezetésével a hálózaton keresztül továbbított bájtok számát **deviceId**, az egyedi párosítást a használt **üzenetazonosító** és **deviceLocation**. Az összetett kulcs **series.tagId** használatos egyedi párjai **típus** és **egység**. Az összetett kulcs lehetővé teszi, hogy a **deviceId** és **series.tagId** négy értékeire hivatkozhatnak használt kulcspár: **üzenetazonosító, deviceLocation, írja be,** és **egység**. Ezek az adatok bejövő időpontban a telemetriai adatokat a tartományhoz. Ez aztán a Time Series Insights lekérdezéséhez.
- két réteg beágyazási használata esetén az első példában szereplő okból.

### <a name="for-both-scenarios"></a>Mindkét forgatókönyv esetében

A tulajdonság a lehetséges értékek nagy számú érdemes elküldeni belül minden egyes érték egy olyan új oszlop létrehozása helyett csak egy oszlop különböző értékeket. Az előző két példákban:

  - Az első példában néhány tulajdonságai számos értékkel rendelkezik, így szükséges, hogy minden egyes külön tulajdonság.
  - A második példában a mértékek nem megadott egyes tulajdonságokat. Ehelyett egy tömb, értékek és a egy közös sorozat tulajdonság intézkedések zajlik. Az új kulcs **tagId** érkezik, az új oszlopot hoz létre, amely **series.tagId** a egybesimított táblában. Az új tulajdonságok **típus** és **egység** jönnek létre, hogy a tulajdonság határértékét nem referencia-adatok használatával.

## <a name="next-steps"></a>További lépések

- Olvasási [Azure Time Series Insights-lekérdezés szintaxisa](/rest/api/time-series-insights/ga-query-syntax) további információ a lekérdezés szintaxisa a Time Series Insights-adatok a REST API elérésével.
- Ismerje meg, [alakzat eseményeket hogyan](./time-series-insights-send-events.md).
