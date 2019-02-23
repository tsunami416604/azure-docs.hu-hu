---
title: Folyamat konfigurálható küszöbérték-alapú szabályok az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan érhet el egy riasztási megoldást, amely rendelkezik a konfigurálható küszöbérték-alapú szabályok az Azure Stream Analyticsben referenciaadatok használatával.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731191"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Folyamat konfigurálható küszöbérték-alapú szabályok az Azure Stream Analytics szolgáltatásban
Ez a cikk ismerteti, hogyan érhet el egy riasztási megoldást, amely konfigurálható küszöbérték-alapú szabályok használja az Azure Stream Analyticsben referenciaadatok használatával.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Forgatókönyv: Riasztás alapján állítható szabály küszöbértékét
Előfordulhat, hogy szeretne riasztást előállítani, kimenet, amikor bejövő adatfolyamként továbbított események elérte az egy adott értékre, vagy ha egy összesített értéket a bejövő adatfolyamként továbbított események alapján meghalad egy bizonyos küszöbértéket. Ez egyszerűen egy Stream Analytics-lekérdezés, amelyet az érték rögzített, és előre meghatározott statikus küszöb beállításához. A rögzített küszöbértéket lehet szokott, a streamelési lekérdezési szintaxis használatával egyszerű numerikus összehasonlítások (nagyobb, kisebb, és egyenlő).

Bizonyos esetekben a küszöbértékeket kell lennie a könnyebben konfigurálható a lekérdezés szintaxisa minden alkalommal, amikor módosul egy küszöbértéket szerkesztése nélkül. Más esetekben szükség lehet a számos dolgozza fel ugyanabból a lekérdezés az egyes kellene az egyes típusú eszközről egy másik küszöbértékek őket a felhasználók vagy eszközök. 

Ez a minta segítségével dinamikusan konfigurálja a küszöbértékeket szelektív válassza az milyen típusú eszközt a küszöbértéket a bemeneti adatok szűrésével vonatkozik, és szelektív válassza ki a mezőket a kimenetben.

## <a name="recommended-design-pattern"></a>Ajánlott tervezési minta
Keresés a riasztástípusok küszöbértékét, használja a Stream Analytics-feladat egy referenciaadat-bemenetek:
- Store a küszöbértékeket a referenciaadatokat, kulcsonként egy értéket.
- Csatlakoztassa a streamelési adatok bemeneti események a referenciaadatok a kulcsoszlop.
- A küszöbérték, a a referenciaadatok kulcsalgoritmus értéket használja.

## <a name="example-data-and-query"></a>Példa adatok és a lekérdezés
A példában a riasztások akkor jönnek létre, a perc-hosszú ablakban eszközökről streamelt adatokat az összesítése megegyezik a meghatározott értékeket a szabályban, csakúgy, mint a referenciaadatok.

A lekérdezés minden egyes deviceId, és minden egyes metricName alatt az eszköz azonosítójával konfigurálhatja a 0, a GROUP BY, 5 dimenziója. Csak az események a megfelelő szűrési értékek szerint vannak csoportosítva. Miután vannak csoportosítva, Min, Max, Avg, az ablakos összesítéseket 60 másodperces átfedésmentes ablak fölé számítjuk ki. A beállított küszöbértéket. a hivatkozás a riasztás kimeneti esemény létrehozása majd kiszámítása a szűrőket az összesített értékekre vannak.

Például tegyük fel, van egy Stream Analytics-feladatot, amely rendelkezik egy nevű referenciaadat-bemenetek **szabályok**, és a streamelési adatok bemeneti nevű **metrikák**. 

## <a name="reference-data"></a>Referenciaadat
Ebben a példában referenciaadatok bemutatja, hogyan küszöbérték-alapú szabály megjeleníthetők. Egy JSON-fájlt a referencia-adatokat tartalmazó tárban, és az Azure blob storage-bA mentett, és a blob storage-tároló szolgál egy nevű referenciaadat-bemenetek **szabályok**. Sikerült írhatja felül a JSON-fájlt, és cserélje le a szabály konfigurálását idő előrehaladtával, leállítása és a streamelési feladat indítása nélkül.

- A példában a szabály szolgál, amelyek állítható riasztást, ha túllépi a CPU (átlag nagyobb vagy egyenlő) értéke `90` százalék. A `value` mező szükség szerint konfigurálható.
- Figyelje meg, hogy a szabály egy **operátor** mező, amely dinamikusan értelmezi a lekérdezési szintaxisban később `AVGGREATEROREQUAL`. 
- A szabály szűri az adatokat egy adott dimenzió kulcs `2` értékkel `C1`. A többi mező kitöltése nem adatfolyam által adott esemény mezők szűréséhez jelző üres karakterlánc. További CPU-szabályok beállíthat más egyező mezők szűréséhez, igény szerint.
- Nem minden oszlopa, amelyeknek szerepelnie a kimeneti figyelmeztetési esemény vannak. Ebben az esetben `includedDim` szám kulcs `2` be van kapcsolva `TRUE` jelölésére, hogy a mező száma 2 eseményadatokat a Stream szerepelni fog a feltételeknek megfelelő kimeneti események. A többi mező nem szerepelnek a riasztási kimenet, de a mezők listájában módosítható.


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

## <a name="example-streaming-query"></a>Streamelési. példalekérdezés
Ez a példa a Stream Analytics lekérdezés csatlakozik a **szabályok** adatokra hivatkoznak az a fenti példában egy nevű bemeneti adatfolyam **metrikák**.

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
Ez a példa JSON-adatokat jelöli a **metrikák** adjon meg a fenti streamelési lekérdezésben használt adatokat. 

- Az érték 1 perces timespan belül felsorolt események például három `T14:50`. 
- Mindhárom rendelkezik azonos `deviceId` érték `978648`.
- Minden esemény eltérő lehet a CPU-metrikaértékek `98`, `95`, `80` jelölik. Csak az első két példa események haladhatja meg a riasztási szabály CPU, a szabály létrejött.
- A riasztási szabályt a includeDim mező legfontosabb száma 2 volt. A megfelelő, az események például a 2. kulcs mező neve `NodeName`. Az események három például értékűek `N024`, `N024`, és `N014` jelölik. A kimenetben láthatja a csomópont csak `N024` , vagyis kizárólag azokat az adatokat, amely megfelel a riasztási feltételek magas processzor. `N014` nem felel meg a magas CPU-küszöbértéket.
- A riasztási szabály beállítása a `filter` csak a kulcs számát 2, amely megfelel a `cluster` mezőbe a minta eseményekre. Az összes példában három események értékkel rendelkeznie `C1` és a szűrési feltételeknek.

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
Ebben a példában a kimeneti JSON adatokat jeleníti meg egy adott riasztás eseményhez állították a Processzor küszöbértékét szabály a referenciaadatok meghatározott alapján. A kimeneti esemény a riasztást, valamint az összesített (átlagos, minimális és maximális) figyelembe venni a mezők nevét tartalmazza. A kimeneti esemény adatokat tartalmaz a mező legfontosabb 2-es számú `NodeName` érték `N024` a konfiguráció miatt. (A JSON az olvashatóság érdekében a sortörések megjeleníthető lett módosítva.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```