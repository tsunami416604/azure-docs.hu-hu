---
title: Az Azure Stream Analytics folyamat konfigurálható küszöbérték-alapú szabályok
description: Ez a cikk ismerteti a referenciaadatok használata egy, a konfigurálható küszöbérték-alapú szabályok Azure Stream Analytics a riasztási megoldást eléréséhez.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 1c131c2c9ca12556c1d2cd52e7976d2f4272a0c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767951"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Folyamatok konfigurálható küszöbérték-alapú szabályok az Azure Stream Analytics
Ez a cikk ismerteti a referenciaadatok használata egy riasztási megoldást az Azure Stream Analytics konfigurálható küszöbérték-alapú szabályok használó eléréséhez.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Forgatókönyv: Állítható szabály küszöbértékek riasztások alapján.
Szükség lehet egy riasztást, ha a bejövő adatfolyamként továbbított események elérte az egy adott értékre, vagy ha a bejövő adatfolyamként továbbított események alapján összesített értéket meghaladja a meghatározott küszöbérték kimenet létrehozásához. Az egyszerű, amelyet az érték, amely rögzített, és előre meghatározott statikus küszöbértékre Stream Analytics-lekérdezés létrehozásához. A rögzített küszöbérték lehet kódolt be a folyamatos átviteli lekérdezés szintaxisát, egyszerű numerikus összehasonlítást használó (nagyobb, kisebb, és a egyenlő).

Bizonyos esetekben a küszöbértékek kell könnyebben konfigurálható a lekérdezés szintaxisa minden alkalommal, amikor megváltozik a küszöbérték szerkesztése nélkül. Más esetekben szükség lehet a számos eszközök vagy felhasználók dolgozza fel az összes őket az eszköz minden típusú különböző küszöbértékek rendelkező ugyanabban a lekérdezésben. 

Ez a minta segítségével dinamikusan konfigurálja a küszöbértékeket, szelektív milyen típusú eszközt a küszöbértéket a bemeneti adatok szűréssel vonatkozik és külön-külön kiválasztható mezőket a kimenetben.

## <a name="recommended-design-pattern"></a>Ajánlott kialakítási mintája
A Keresés a riasztási küszöbértékek a referenciaadat-bemenetek Stream Analytics-feladathoz használata:
- A küszöbértékek tárolja a referenciaadatok, kulcs egy értéket.
- A streamelési adatok bemeneti események csatlakoztatása a referenciaadatok a kulcs oszlopra.
- A referenciaadatok a kulccsal definiált értéket használják a küszöbértéket.

## <a name="example-data-and-query"></a>Példa adatok és a lekérdezés
A példában a riasztások akkor jönnek létre, adatfolyam, a perc-hosszú ablakban eszközökről összesített megegyezik a megadott hivatkozás adatként szabályban meghatározott értékek.

A lekérdezés minden deviceId, és minden metricName alatt a deviceId konfigurálhatja 0, a GROUP BY 5 dimenziók. Csak a megfelelő szűrőértékek rendelkező események vannak csoportosítva. Miután csoportosítva, ablakos összesítéseket, Min, Max, Avg, kiszámítása egy 60 másodperces átfedésmentes ablak keresztül. A beállított küszöbértéket. a hivatkozás a riasztás kimeneti esemény létrehozásához majd kiszámítása a szűrő az összesített értékek vannak.

Fel például, a Stream Analytics-feladat, amely rendelkezik a referenciaadat-bemenetek nevű van **szabályok**, és adatfolyam-nevű bemeneti **metrikák**. 

## <a name="reference-data"></a>Referenciaadat
A példa referenciaadatok jeleníti meg, hogyan küszöbérték-alapú szabály sikerült képviseli. A JSON-fájl tartalmazza a referenciaadatok, és az Azure blob storage mentéskor, és a blob storage tárolót a referenciaadat-bemenetek nevű vettük **szabályok**. Sikerült felül a JSON-fájlt, és cserélje le a konfiguráció idő előrehaladtával, leállítása és a folyamatos átviteli feladat indítása nélkül.

- A példában a szabály segítségével azonosítja a állítható riasztást, amikor a CPU-meghaladja (átlagolni nagyobb vagy egyenlő) értéke `90` százalék. A `value` mezője szükség szerint konfigurálható.
- Figyelje meg, a szabály egy **operátor** mező, amely dinamikusan értelmezi a lekérdezés szintaxisa a későbbi `AVGGREATEROREQUAL`. 
- A szabály egy adott dimenzió kulcs lévő adatok szűrése `2` értékű `C1`. A többi mező kitöltése jelzi a bemeneti adatfolyam esemény mezők szerint szűrheti a nem üres karakterlánc. További CPU-szabályok beállíthat más mezők szűréséhez, igény szerint.
- Nem minden oszlopa van a kimeneti figyelmeztetési esemény szerepeltetni. Ebben az esetben `includedDim` szám kulcs `2` be van kapcsolva `TRUE` képviseli, hogy, hogy az adatfolyam eseményadatok számot 2 fog szerepelni a megfelelő kimeneti események. A más mezők nem szerepelnek a riasztási kimeneti, de a mezőlista módosítható.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Adatfolyam-továbbítási példalekérdezés
A Stream Analytics példalekérdezés csatlakozik a **szabályok** nevű adatok egy bemeneti adatfolyam a fenti példában a referenciaadatok **metrikák**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Példa streamadatok bemeneti esemény
Ez a példa JSON-adatok jelöli a **metrikák** adja meg a fenti adatfolyam lekérdezésben használt adatokat. 

- Az 1 perces timespan érték belül felsorolt események például három `T14:50`. 
- Három rendelkezik azonos `deviceId` érték `978648`.
- A CPU-metrika értékek eltérők lehetnek, minden esetben `98`, `95`, `80` kulcsattribútumokkal. Csak az első két események például nagyobb, mint a szabályban meghatározott CPU riasztási szabályt.
- A riasztási szabály a includeDim mező kulcs száma 2 volt. Az események például a megfelelő kulcs 2 mezőre nevű `NodeName`. Az események három például értékűek `N024`, `N024`, és `N014` kulcsattribútumokkal. A kimenetben megjelenik a csomópont csak `N024` , vagyis az adatok közül kizárólag a riasztások jellemzői megegyezik a magas processzor. `N014` nem felel meg a magas CPU-küszöbérték.
- A riasztási szabály beállítása a `filter` csak a kulcs számát 2, amely megfelel a `cluster` mező mellett a minta események. Az összes események három például értéke lehet `C1` és szűrési feltételeknek.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Példa a kimenetre
Ebben a példában a kimeneti JSON adatokat jeleníti meg egy adott riasztás eseményhez állították a referenciaadatok definiált CPU küszöbérték szabály alapján. A kimeneti esemény a riasztást, valamint az összesített (átlagolni, min, max) minősül mező nevét tartalmazza. A kimeneti esemény kulcs Mezőszám 2 szerepel `NodeName` érték `N024` a konfiguráció miatt. (A JSON volt módosítani kell az olvashatóság sortörések megjelenítése.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```