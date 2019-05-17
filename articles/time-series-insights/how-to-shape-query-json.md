---
title: Ajánlott eljárások az Azure Time Series Insights-lekérdezések JSON alakító |} A Microsoft Docs
description: Ismerje meg, hogyan javíthatja az Azure Time Series Insights-lekérdezés hatékonyságát.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 535fe9a7920db89e434b4cc1b66aa38bf72a7829
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790070"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Hogyan formázhatja a JSON-lekérdezési teljesítmény maximalizálása 

Ez a cikk nyújt útmutatást, amelyeket formázhat JSON-t, a hatékonyság az Azure Time Series Insights-lekérdezéseket.

## <a name="video"></a>Videó

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Ismerje meg, ajánlott eljárások a tárolási igényei JSON alakításra.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Ajánlott eljárások

Fontos, milyen eseményeket küldeni a Time Series Insights állításoknak. Nevezetesen akkor mindig:

1. az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
1. Győződjön meg arról, az adatok olyan módon, amely lehetővé teszi, hogy a forgatókönyvnek megfelelő összesítések tárolása.
1. Győződjön meg, hogy ne eléri a Time Series Insights maximális tulajdonság korlátai:
   - S1 környezetek 600 tulajdonságait (oszlop).
   - S2 környezetek 800 tulajdonságait (oszlop).

Az alábbi útmutató segít a legjobb lehetséges lekérdezési teljesítmény érdekében:

1. NA használjon dinamikus tulajdonságait, például a címke azonosítója,-tulajdonság neveként, mivel a üzletmentében szerezze meg a Tulajdonságok maximális korlátot.
1. Ne küldjön a szükségtelen tulajdonságait. Ha egy lekérdezést a tulajdonság nem szükséges, célszerű nem küldje el, és elkerülheti a storage korlátozásaival.
1. Használat [referenciaadatok](time-series-insights-add-reference-data-set.md), nehogy a statikus adatok küldése a hálózaton keresztül.
1. Megosztás dimenzió tulajdonságai között több esemény adatok hatékonyabb a hálózaton keresztüli küldéshez.
1. Ne használja a részletes tömb beágyazást. A Time Series Insights objektumokat tartalmazó beágyazott tömbök legfeljebb két szintet támogat. A Time Series Insights több események tulajdonság-érték párok az simítja egybe az üzeneteket, a tömb.
1. Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. Külön-külön elküldheti csökkenti az események számát, és előfordulhat, hogy elérhetővé lekérdezések több nagy teljesítményű, mint a kevesebb eseménnyel kell feldolgozni. Ha több mértékek, elküldheti az egy-egy tulajdonság értékeként minimálisra csökkenti a szerezze meg a tulajdonság maximális korlát lehetőségét.

## <a name="example-overview"></a>Példa – áttekintés

Az alábbi két példák bemutatják, küldő események, a fenti javaslatok kiemeléséhez. Következő valamennyi példa láthatja, hogyan lettek alkalmazva a javaslatokat.

A példák egy forgatókönyvet, ahol több eszköz küldése mérések vagy jelek alapulnak. Mérések vagy jelek Flow arány, motor olaj nyomás, hőmérsékleti és páratartalom lehet. Az első példában nincsenek néhány mérések összes eszközön. A második példában sok eszköz, és minden egyes számos egyedi mértékek küld.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Első forgatókönyv: csak néhány mérések létezik

> [!TIP]
> **Az ajánlás**: minden mérték/jel elküldeni egy külön tulajdonság oszlop.

A következő példában van egy IoT Hub üzenet, ahol a külső tömb tartalmaz-e a közös dimenzióértékek egy közös szakaszhoz. A külső tömb referenciaadatok használja az üzenet a hatékonyság növelése érdekében. Referenciaadatok tartalmazza az eszközök metaadatait, minden eseményhez nem változik, de hasznos tulajdonságok biztosít az adatok elemzésére. Közös dimenzióértékek kötegelés, mind referenciaadatok takaríthat meg a hálózaton keresztül küldött bájtok alkalmazó így hatékonyabb az üzenetet.

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

* Referencia-adattábla (kulcs tulajdonság **deviceId**):

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | SOR\_ADATOK | EU |
   | PÉÉÉ | SOR\_ADATOK | USA |

* Time Series Insights esemény tábla (után az egybesimítás):

   | deviceId | messageId | deviceLocation | időbélyeg | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | PÉÉÉ | SOR\_ADATOK | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Fent:

- A **deviceId** oszlop szolgál a különböző eszközökhöz egy járműflotta az az oszlop fejlécére. DeviceId érték létesíteni a saját tulajdonságnév volna csak korlátozott 595 (S1 környezetben) vagy 795 (S2 környezetben), az összes eszköz az egyéb öt oszlopokat.

- A felesleges tulajdonságok elkerülhetők a, például a gyártmány és modell, stb. Mivel azok nem kérdezhető le a jövőben, távolítsa el őket lehetővé teszi, hogy jobb hálózati és tárolási hatékonyságot.

- Referenciaadatok segítségével csökkentheti a hálózaton keresztül továbbított bájtok száma. Két attribútum **üzenetazonosító** és **deviceLocation**, a kulcs tulajdonságot, csatlakozott **deviceId**. Ezeket az adatokat a telemetriai adatokat a bejövő egyidejűleg csatlakoztatott, és ezt követően tárolja a Time Series Insightsban lekérdezéséhez.

- Két réteg beágyazási használnak, amely az a maximális beágyazási Time Series Insights támogatja. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.

- Mértékek belül ugyanarra az objektumra, különálló tulajdonságként érkeznek, mivel néhány mértékeket. Itt **sorozat. Flow arány psi** és **sorozat. A motor olaj nyomás láb3/s** egyedi oszlop.

## <a name="scenario-two-several-measures-exist"></a>Második forgatókönyv: több mértékek létezik

> [!TIP]
> **Javaslat:** mérések küldése a "type", "egység", "value" rekordokat.

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

* Referenciaadatok (kulcs a tulajdonságok akkor vannak **deviceId** és **series.tagId**):

   | deviceId | series.tagId | messageId | deviceLocation | type | egység |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s |
   | FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi |
   | PÉÉÉ | pumpRate | SOR\_ADATOK | USA | A folyamat arány | láb3/s |
   | PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Olaj nyomás motor | psi |

* Time Series Insights esemény tábla (után az egybesimítás):

   | deviceId | series.tagId | messageId | deviceLocation | type | egység | időbélyeg | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | Psi | 2018-01-17T01:17:00Z | 49.2 |
   | PÉÉÉ | pumpRate | SOR\_ADATOK | USA | A folyamat arány | láb3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Olaj nyomás motor | psi | 2018-01-17T01:18:00Z | 22.2 |

Fent:

- Az oszlopok **deviceId** és **series.tagId** az oszlopfejléceket a különböző eszközökön és a egy járműflotta a címkék szolgál. Mindegyik a saját attribútumként használatával szeretné csak korlátozottan 594 (S1 környezetben) vagy 794 (S2 környezetben) összes eszköz az egyéb hat oszlopokat a lekérdezésben.

- a felesleges tulajdonságok lettek kerülni, az okból az első példában szereplő.

- Referenciaadatok segítségével csökkentse a bevezetésével a hálózaton keresztül továbbított bájtok számát **deviceId**, az egyedi párjai **üzenetazonosító** és **deviceLocation**. Összetett kulcsot használ, **series.tagId**, az egyedi párjainak **típus** és **időegységet**. Az összetett kulcs lehetővé teszi, hogy a **deviceId** és **series.tagId** pár használandó, tekintse meg négy értéket: **üzenetazonosító, deviceLocation, írja be,** és **egység** . Ezeket az adatokat a telemetriai adatokat a bejövő egyidejűleg csatlakoztatott, és ezt követően tárolja a Time Series Insightsban lekérdezéséhez.

- két réteg beágyazási használata esetén az első példában szereplő okból.

### <a name="for-both-scenarios"></a>Mindkét forgatókönyv esetében

Ha egy tulajdonság lehetséges értékek nagy számú, célszerű különböző értékeket belül csak egy oszlop, nem pedig minden egyes érték egy olyan új oszlop létrehozása, küldése. Az előző két példákban:

  - Az első példában nincsenek néhány tulajdonsággal, amely számos értékkel rendelkezik, így a szükséges, hogy minden egyes külön tulajdonság.
  - Azonban a második példában láthatja, hogy a mértékek nincs megadva az egyes tulajdonságok, de, egy közös series tulajdonságának értékek/intézkedéseinek tömb. Egy új kulcsot a rendszer elküldte, **tagId** , ami létrehoz egy olyan új oszlop **series.tagId** a egybesimított táblában. Új tulajdonságok jönnek létre, **típus** és **egység**, referenciaadatokkal, így meggátolja, hogy a tulajdonság korlát találati folyamatban van.

## <a name="next-steps"></a>További lépések

- Olvasási [Azure Time Series Insights-lekérdezés szintaxisa](/rest/api/time-series-insights/ga-query-syntax) további információ a lekérdezés szintaxisa a Time Series Insights-adatok a REST API elérésével.

- Ismerje meg, [alakzat eseményeket hogyan](./time-series-insights-send-events.md).
