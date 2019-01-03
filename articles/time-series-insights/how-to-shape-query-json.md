---
title: Ajánlott eljárások az Azure Time Series Insights-lekérdezések JSON alakító |} A Microsoft Docs
description: Ismerje meg, hogyan javíthatja az Azure Time Series Insights-lekérdezés hatékonyságát.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: aa09183d1dbb78822b6c46333dada3ed7f54811a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554571"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Hogyan formázhatja a JSON-lekérdezési teljesítmény maximalizálása 

Ez a cikk nyújt útmutatást, amelyeket formázhat JSON-t, a hatékonyság az Azure Time Series Insights (TSI) lekérdezéseket.

## <a name="video"></a>Videó: 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>Ez a videó tárolási igényei JSON alakításra körül ajánlott eljárásokat ismerteti.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Ajánlott eljárások

Fontos, milyen eseményeket küldeni a Time Series Insights állításoknak. Nevezetesen akkor mindig:

1. az adatok elküldése a hálózaton keresztül lehető leghatékonyabb.
2. Győződjön meg arról, az adatok olyan módon, amely lehetővé teszi, hogy a forgatókönyvnek megfelelő összesítések tárolása.
3. Győződjön meg, hogy ne eléri a TSI a maximális tulajdonság korlátai
   - S1 környezetek 600 tulajdonságait (oszlop).
   - S2 környezetek 800 tulajdonságait (oszlop).

Az alábbi útmutató segít a legjobb lehetséges lekérdezési teljesítmény érdekében:

1. NA használjon dinamikus tulajdonságait, például a címke azonosítója,-tulajdonság neveként, mivel a üzletmentében szerezze meg a Tulajdonságok maximális korlátot.
2. Ne küldjön a szükségtelen tulajdonságait. Ha egy lekérdezést a tulajdonság nem szükséges, célszerű nem küldje el, és elkerülheti a storage korlátozásaival.
3. Használat [referenciaadatok](time-series-insights-add-reference-data-set.md), nehogy a statikus adatok küldése a hálózaton keresztül.
4. Megosztás dimenzió tulajdonságai között több esemény adatok hatékonyabb a hálózaton keresztüli küldéshez.
5. Ne használja a részletes tömb beágyazást. A TSI objektumokat tartalmazó beágyazott tömbök legfeljebb két szintet támogat. A TSI tömbök az üzeneteket, a tulajdonság-érték párokat a több események simítja egybe.
6. Ha csak néhány mértékek minden vagy a legtöbb esemény létezik, érdemes küldeni ezeket a mértékeket különálló tulajdonságként az objektumon belül. Külön-külön elküldheti csökkenti az események számát, és előfordulhat, hogy elérhetővé lekérdezések több nagy teljesítményű, mint a kevesebb eseménnyel kell feldolgozni. Ha több mértékek, elküldheti az egy-egy tulajdonság értékeként minimálisra csökkenti a szerezze meg a tulajdonság maximális korlát lehetőségét.

## <a name="examples"></a>Példák

Az alábbi két példák bemutatják, küldő események, a fenti javaslatok kiemeléséhez. Következő valamennyi példa láthatja, hogyan lettek alkalmazva a javaslatokat.

A példák egy forgatókönyvet, ahol több eszköz küldése mérések vagy jelek alapulnak. Mérések vagy jelek Flow arány, motor olaj nyomás, hőmérsékleti és páratartalom lehet. Az első példában nincsenek néhány mérések összes eszközön. A második példában sok eszköz, és minden egyes számos egyedi mértékek küld.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Forgatókönyv: csak néhány mérések/jelek létezik

**Javaslat:** egyes mérések küldése egy külön tulajdonság oszlop.

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Referencia-adattábla (kulcstulajdonság a deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | SOR\_ADATOK | EU |
| PÉÉÉ | SOR\_ADATOK | USA |

Time Series Insights esemény tábla (után az egybesimítás):

| deviceId | messageId | deviceLocation | időbélyeg | adatsorozat. A folyamat arány láb3/s | adatsorozat. Olaj nyomás psi motor |
| --- | --- | --- | --- | --- | --- |
| FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| PÉÉÉ | SOR\_ADATOK | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Megjegyzés: az előző példában a következőket:

- A **deviceId** oszlop szolgál a különböző eszközökhöz egy járműflotta az az oszlop fejlécére. DeviceId érték létesíteni a saját tulajdonságnév volna csak korlátozott 595 (S1 környezetben) vagy 795 (S2 környezetben), az összes eszköz az egyéb öt oszlopokat.

- A felesleges tulajdonságok elkerülhetők a, például a gyártmány és modell, stb. Mivel azok nem kérdezhető le a jövőben, távolítsa el őket lehetővé teszi, hogy jobb hálózati és tárolási hatékonyságot.

- Referenciaadatok segítségével csökkentheti a hálózaton keresztül továbbított bájtok száma. Két attribútum **üzenetazonosító** és **deviceLocation** , a kulcs tulajdonságot, csatlakozott **deviceId**. Ezeket az adatokat a telemetriai adatokat a bejövő egyidejűleg csatlakoztatott, és ezt követően a TSI-ben tárolt lekérdezéséhez.

- Két réteg beágyazási vannak használatban, amely a beágyazási TSI által támogatott maximális számát. Rendkívül fontos a mélyen beágyazott tömbök elkerülése érdekében.

- Mértékek belül ugyanarra az objektumra, különálló tulajdonságként érkeznek, mivel néhány mértékeket. Itt **sorozat. Flow arány psi** és **sorozat. A motor olaj nyomás láb3/s** egyedi oszlop.

### <a name="scenario-several-measures-exist"></a>Forgatókönyv: több mértékek létezik

**Javaslat:** mérések küldése a "type", "egység", "value" rekordokat.

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

A referenciaadatok (kulcstulajdonságok olyan deviceId és series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | type | egység |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s |
| FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi |
| PÉÉÉ | pumpRate | SOR\_ADATOK | USA | A folyamat arány | láb3/s |
| PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Olaj nyomás motor | psi |

Time Series Insights esemény tábla (után az egybesimítás):

| deviceId | series.tagId | messageId | deviceLocation | type | egység | időbélyeg | Series.Value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | SOR\_ADATOK | EU | A folyamat arány | láb3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | SOR\_ADATOK | EU | Olaj nyomás motor | PSI | 2018-01-17T01:17:00Z | 49.2 |
| PÉÉÉ | pumpRate | SOR\_ADATOK | USA | A folyamat arány | láb3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Olaj nyomás motor | psi | 2018-01-17T01:18:00Z | 22.2 |

Vegye figyelembe a következő az előző példában, és az első példához hasonló:

- oszlopok **deviceId** és **series.tagId** az oszlopfejléceket a különböző eszközökön és a egy járműflotta a címkék szolgál. Mindegyik a saját attribútumként használatával szeretné csak korlátozottan 594 (S1 környezetben) vagy 794 (S2 környezetben) összes eszköz az egyéb hat oszlopokat a lekérdezésben.

- a felesleges tulajdonságok lettek kerülni, az okból az első példában szereplő.

- Referenciaadatok segítségével csökkentse a bevezetésével a hálózaton keresztül továbbított bájtok számát **deviceId**, az egyedi párjai **üzenetazonosító** és **deviceLocation**. Összetett kulcsot használ, **series.tagId**, az egyedi párjainak **típus** és **időegységet**. Az összetett kulcs lehetővé teszi, hogy a **deviceId** és **series.tagId** pár használandó, tekintse meg négy értéket: **üzenetazonosító, deviceLocation, írja be,** és **egység** . Ezeket az adatokat a telemetriai adatokat a bejövő egyidejűleg csatlakoztatott, és ezt követően a TSI-ben tárolt lekérdezéséhez.

- két réteg beágyazási használata esetén az első példában szereplő okból.

### <a name="for-both-scenarios"></a>Mindkét forgatókönyv esetében

Ha egy tulajdonság lehetséges értékek nagy számú, célszerű különböző értékeket belül csak egy oszlop, nem pedig minden egyes érték egy olyan új oszlop létrehozása, küldése. Az előző két példákban:
  - Az első példában nincsenek néhány tulajdonsággal, amely számos értékkel rendelkezik, így a szükséges, hogy minden egyes külön tulajdonság. 
  - Azonban a második példában láthatja, hogy a mértékek nincs megadva az egyes tulajdonságok, de, egy közös series tulajdonságának értékek/intézkedéseinek tömb. Egy új kulcsot a rendszer elküldte, **tagId** , ami létrehoz egy olyan új oszlop **series.tagId** a egybesimított táblában. Új tulajdonságok jönnek létre, **típus** és **egység**, referenciaadatokkal, így meggátolja, hogy a tulajdonság korlát találati folyamatban van.

## <a name="next-steps"></a>További lépések

Ezeket az irányelveket a gyakorlatba, tekintse meg [Azure Time Series Insights-lekérdezés szintaxisa](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) további információ a lekérdezés szintaxisa a TSI adatelérési REST API számára.