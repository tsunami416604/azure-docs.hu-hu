---
title: Gyakorlati tanácsok JSON alakító Azure idő adatsorozat Insights lekérdezésekben.
description: Ismerje meg, hogyan lehet fokozni az az idő adatsorozat Insights lekérdezés hatékonyságát.
services: time-series-insights
author: ashannon7
manager: timlt
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: bryanla
ms.openlocfilehash: 29919a3ce8c18982c88f7f0e6bbd774c612e9c44
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660930"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Hogyan alakul a lekérdezési teljesítmény maximalizálása érdekében JSON 

Ez a cikk nyújt útmutatást JSON Azure idő adatsorozat Insights (ÁME) lekérdezései hatékonyságának maximalizálása kialakításában.

## <a name="best-practices"></a>Ajánlott eljárások

Fontos, hogyan küldött események idő adatsorozat Insights gondolniuk. Kulcstartó akkor mindig:

1. adatokat küldeni a hálózaton keresztül lehető leghatékonyabb módon.
2. Győződjön meg arról, hogy lehetővé teszi a forgatókönyvnek megfelelő összesítések tárolja az adatokat.
3. Győződjön meg arról nem kattint az ÁME tartozó maximális tulajdonság korlátai
   - S1 környezetek 600 tulajdonságait (oszlop).
   - S2 környezetek 800 tulajdonságait (oszlop).

A következő útmutatást biztosíthatja a legjobb lehetséges lekérdezési teljesítmény:

1. Ne használjon dinamikus tulajdonságait, például egy tulajdonság neve, mint a címke azonosítója, mert részt vesz a Tulajdonságok maximális korlátot elérte-e.
2. Ne küldjön a szükségtelen tulajdonságok. Ha a lekérdezés tulajdonság nem szükséges, célszerű nem küldje el, és elkerülheti a tárolási korlátozások.
3. Használjon [referenciaadatok](time-series-insights-add-reference-data-set.md), nehogy statikus adatok elküldése a hálózaton keresztül.
4. Megosztás dimenzió tulajdonságai között több esemény hatékonyabban küldhet adatokat a hálózaton keresztül.
5. A részletes tömb beágyazási ne használjon. ÁME objektumokat tartalmazó beágyazott tömbök legfeljebb két szintet támogat. ÁME az üzenetek a tömbök simítja be tulajdonság-érték párok több eseményeket.
6. Ha csak néhány intézkedések a legtöbb vagy összes esemény létezik, akkor ezeket a mértékeket küldése külön tulajdonságként az objektumon belül. Külön-külön elküldi őket csökkenti az események számát, és teheti lekérdezések performant több, mint a kevesebb eseménnyel kell feldolgozni. Ha több intézkedések, elküldi őket egy-egy tulajdonság értékeiként minimálisra csökkenti a lehetőségét, hogy elérte-e a tulajdonság maximális korlátot.

## <a name="examples"></a>Példák

A következő két példák bemutatják, küldő események, jelölje ki az előző javaslatokat. Követően minden példában láthatja, hogyan ajánlások vannak érvényben.

A példa egy olyan forgatókönyvet, ahol több eszközre küldése mérések vagy jelek alapulnak. Mérések vagy jelek Flow sebességét, a motor olaj nyomás, a hőmérséklet és a páratartalom lehet. Az első példában nincsenek néhány mérések az összes eszközön. A második példában több eszközt, és minden egyes sok egyedi mérések küld.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Forgatókönyv: csak néhány mérések/jelek létezik

**Javaslat:** minden mérési küldeni egy külön tulajdonság oszlop.

A következő példában nincs egyetlen IoT-központ üzenet, ahol a a külső tömb közös dimenzióértékek megosztott szakasza tartalmaz. A külső tömb referenciaadatok használja az üzenet hatékonyságát. Referenciaadatok eszköz metaadatot tartalmaz, minden esemény nem változik, de hasznos tulajdonságok biztosítja az adatok elemzésére. Közös dimenzióértékek kötegelés, mind a referenciaadatok, takaríthat meg a hálózaton keresztül küldött bájtok alkalmazó így hatékonyabb az üzenetet.

Példa JSON-adattartalmat:

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

Referencia-adattábla (kulcstulajdonsága deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | SOR\_ADATOK | EU |
| PÉÉÉ | SOR\_ADATOK | USA |

Adatsorozat Insights esemény időtáblázatát (után egybesimítását):

| deviceId | messageId | deviceLocation | időbélyeg | adatsorozat. Egymást követő láb3/s sebesség | adatsorozat. Motor olaj nyomás psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | SOR\_ADATOK | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| PÉÉÉ | SOR\_ADATOK | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Megjegyzés: az előző példában a következőket:

- A **deviceId** oszlop fejlécének egy járműflotta különböző eszközökön funkcionál. DeviceId érték létesíteni saját tulajdonság neve volna korlátozott eszközök teljes száma (S1 környezetben) 595 vagy 795 (S2 környezetben), az egyéb öt oszlopokat.

- A felesleges tulajdonságok elkerülhetők, például a gyártmányának és modelljének információt, stb. Mivel azok nem kérdezhető le, a jövőben, megszüntetése lehetővé teszi a nagyobb hálózati és tároló-hatékonyságot biztosít.

- Referenciaadatok segítségével csökkentheti a hálózaton keresztül továbbított bájtok száma. Két attribútum **messageId** és **deviceLocation** , a kulcstulajdonsága van illesztve **deviceId**. Ezek az adatok csatlakoztatni a telemetriai adatokat a érkező időpontban, és ezt követően tárolja ÁME lekérdezése.

- Két réteg beágyazási használ, amely az a maximális beágyazási ÁME által támogatott. Nagyon fontos a mélyen beágyazott tömbök elkerülése érdekében.

- Intézkedések küldése a külön tulajdonságokat ugyanahhoz az objektumhoz, mert néhány mérték. Itt **adatsorozat. Egymást követő arány psi** és **adatsorozat. A motor olaj nyomás láb3/s** egyedi oszlop.

### <a name="scenario-several-measures-exist"></a>Forgatókönyv: több intézkedések létezik

**Javaslat:** mérések elküldeni a "type", "egység", "érték" rekordokat.

Példa JSON-adattartalmat:

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

A referenciaadatok (kulcsfontosságú tulajdonságainak deviceId és series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | type | egység |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | SOR\_ADATOK | EU | Attribútumfolyam arány | láb3/s |
| FXXX | oilPressure | SOR\_ADATOK | EU | Motor olaj nyomás | psi |
| PÉÉÉ | pumpRate | SOR\_ADATOK | USA | Attribútumfolyam arány | láb3/s |
| PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Motor olaj nyomás | psi |

Adatsorozat Insights esemény időtáblázatát (után egybesimítását):

| deviceId | series.tagId | messageId | deviceLocation | type | egység | időbélyeg | Series.Value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | SOR\_ADATOK | EU | Attribútumfolyam arány | láb3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | SOR\_ADATOK | EU | Motor olaj nyomás | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | SOR\_ADATOK | EU | Attribútumfolyam arány | láb3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | SOR\_ADATOK | EU | Motor olaj nyomás | PSI | 2018-01-17T01:17:00Z | 49.2 |
| PÉÉÉ | pumpRate | SOR\_ADATOK | USA | Attribútumfolyam arány | láb3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| PÉÉÉ | oilPressure | SOR\_ADATOK | USA | Motor olaj nyomás | psi | 2018-01-17T01:18:00Z | 22.2 |

Vegye figyelembe a következő az előző példában, és az első példához hasonló:

- oszlopok **deviceId** és **series.tagId** az oszlopfejlécek a különféle eszközök és egy járműflotta címkék szolgál. Egyes saját attribútumaként lesz korlátozott az 594 (S1 környezetben) vagy 794 (S2 környezetben) eszközök teljes száma a más hat oszlopokkal lekérdezést.

- szükségtelen tulajdonságok volt elkerülhető, fájljaiban az első példában szereplő okból.

- Referenciaadatok szolgál, mert megjelentek a hálózaton keresztül továbbított bájtok számának csökkentése érdekében **deviceId**, az egyedi két **messageId** és **deviceLocation**. Összetett kulcsot használnak, **series.tagId**, az egyedi párjainak **típus** és **egységben**. Az összetett kulcs lehetővé teszi, hogy a **deviceId** és **series.tagId** használandó, tekintse meg a négy érték párt: **messageId, deviceLocation, írja be,** és **egység** . Ezek az adatok csatlakoztatni a telemetriai adatokat a érkező időpontban, és ezt követően tárolja ÁME lekérdezése.

- két réteg beágyazási használata esetén az első idézett okból.

### <a name="for-both-scenarios"></a>Mindkét forgatókönyvek esetén

Ha egy tulajdonság lehetséges értékei nagy mennyiségű, érdemes elküldeni különböző értékeket belül csak egy oszlop, nem pedig minden egyes értékre egy olyan új oszlop létrehozása. Az előző két példákban:
  - Az első példában, hogy egy külön tulajdonság megkönnyítésére mindegyik több értéket tartalmazó néhány tulajdonságok vannak. 
  - Azonban a második példában láthatja, hogy a mértékeket egyéni tulajdonságok, de ehelyett egy közös series tulajdonságának értékek/intézkedéseinek tömb kapcsolójaként nincs megadva. Egy új kulcsot a rendszer küld **tagId** , ami létrehoz egy olyan új oszlop **series.tagId** a egybesimított táblában. Új tulajdonságok jönnek létre, **típus** és **egység**, hivatkozási adatok használatával, így a tulajdonság korlátját meggátolja alatt kattintson a.

## <a name="next-steps"></a>További lépések

Ezeket az irányelveket a gyakorlatba, tekintse meg [Azure idő adatsorozat Insights lekérdezés szintaxisa](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) további információt a lekérdezés szintaxisa az ÁME adatok elérésével REST API-t.