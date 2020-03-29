---
title: Konfigurálható küszöbérték-alapú szabályok az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan használhatja a referenciaadatokat egy olyan riasztási megoldás eléréséhez, amely konfigurálható küszöbértékalapú szabályokkal rendelkezik az Azure Stream Analytics szolgáltatásban.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 94fdddf11acb6763ed98a4b7e17304fbde0e25dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369711"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Konfigurálható küszöbérték-alapú szabályok feldolgozása az Azure Stream Analytics szolgáltatásban
Ez a cikk ismerteti, hogyan használhatja a referenciaadatokat egy olyan riasztási megoldás eléréséhez, amely konfigurálható küszöbérték-alapú szabályokat használ az Azure Stream Analytics-ben.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Eset: Riasztás a módosítható szabályküszöbértékek alapján
Előfordulhat, hogy riasztást kell létrehoznia kimenetként, ha a bejövő streamelt események elértek egy bizonyos értéket, vagy ha a bejövő streamelt eseményeken alapuló összesített érték túllép egy bizonyos küszöbértéket. Egyszerűen állíthat be egy Stream Analytics-lekérdezést, amely összehasonlította az értéket egy rögzített és előre meghatározott statikus küszöbértékkel. A rögzített küszöbértéket egyszerűen numerikus összehasonlításokkal lehet kódolni a streamelési lekérdezés szintaxisába (nagyobb, kisebb, mint és egyenlőség).

Bizonyos esetekben a küszöbértékek könnyebben konfigurálhatóknak kell lenniük a lekérdezés szintaxisának módosítása nélkül minden alkalommal, amikor egy küszöbérték megváltozik. Más esetekben előfordulhat, hogy számos eszközt vagy felhasználót dolgoz fel ugyanaz a lekérdezés, és mindegyiknek különböző küszöbértékevan az egyes eszközökön. 

Ez a minta segítségével dinamikusan konfigurálhatja a küszöbértékeket, szelektíven kiválaszthatja, hogy a küszöbérték milyen típusú eszközt alkalmaz a bemeneti adatok szűrésével, és szelektíven válassza ki, hogy mely mezőket szeretné felvenni a kimenetbe.

## <a name="recommended-design-pattern"></a>Ajánlott tervezési minta
A riasztási küszöbértékek kereséséhez használjon adatbevitelt egy Stream Analytics-feladatba:
- A küszöbértékeket kulcsonként egy értékkel tárolja a referenciaadatokban.
- Csatlakoztassa a streamelési adatbeviteli eseményeket a kulcsoszlop referenciaadataihoz.
- A referenciaadatok kulcsos értékét használja küszöbértékként.

## <a name="example-data-and-query"></a>Példa adatok ra és lekérdezésre
A példában riasztások jönnek létre, ha az eszközökről egy perc hosszú ablakban lévő adatok összesítése megegyezik a referenciaadatként megadott értékekkel.

A lekérdezésben minden egyes deviceId, és minden metrikaNév az eszközazonosító alatt, 0-tól 5 dimenziók GROUP BY konfigurálható. Csak a megfelelő szűrőértékekkel rendelkező események vannak csoportosítva. A csoportosítás után a Min, Max, Avg ablakokkal körülvett összesítéseit egy 60 másodperces bukdácsoló ablakban számítja ki a rendszer. Az összesített értékek szűrőit a rendszer a hivatkozásban megadott küszöbérték nek megfelelően számítja ki a riasztási kimeneti esemény létrehozásához.

Tegyük fel például, hogy van egy Stream Analytics-feladat, amely rendelkezik egy hivatkozási adatbevitelnevű **szabályokkal**, és a streamelt adatbevitel nevű **metrikákkal rendelkezik.** 

## <a name="reference-data"></a>Referenciaadatok
Ez a példa referenciaadatok azt mutatják, hogy egy küszöbérték-alapú szabály hogyan jeleníthető meg. A JSON-fájl tartalmazza a referenciaadatokat, és menti az Azure blob storage, és hogy a blob storage-tároló ként használják a referencia-adatbevitel nevű **szabályok.** Felülírhatja ezt a JSON-fájlt, és lecserélheti a szabály konfigurációját az idő előrehaladtával, a streamelési feladat leállítása vagy elindítása nélkül.

- A példaszabály egy állítható riasztás ábrázolására szolgál, ha a PROCESSZOR túllépi `90` (az átlag nagyobb vagy egyenlő) az érték százalékát. A `value` mező szükség szerint konfigurálható.
- Figyelje meg, hogy a szabály **operátormezővel** rendelkezik, amelyet a lekérdezés szintaxisa később dinamikusan `AVGGREATEROREQUAL`értelmez. 
- A szabály szűri az adatokat egy bizonyos dimenziókulcs `2` értékkel. `C1` Más mezők üres karakterláncok, amelyek azt jelzik, hogy a bemeneti adatfolyamot ne szűrje az eseménymezők szerint. További CPU-szabályokat is beállíthat, hogy szükség szerint szűrje a többi egyező mezőt.
- Nem minden oszlop szerepel a kimeneti riasztási esemény. Ebben az `includedDim` esetben `2` a kulcsszám be van kapcsolva, `TRUE` hogy képviselje, hogy az adatfolyamban lévő eseményadatok 2-es számú mezője szerepelni fog a feljogosító kimeneti eseményekben. A többi mező nem szerepel a riasztás kimenetében, de a mezőlista módosítható.


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

## <a name="example-streaming-query"></a>Példa streamelési lekérdezésre
Ez a példa Stream Analytics lekérdezés egyesíti a **szabályok** referencia adatok a fenti példában, egy bemeneti adatnevű **metrikák**bemeneti adatfolyam.

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

## <a name="example-streaming-input-event-data"></a>Példa streamelési bemeneti eseményadataira
Ez a példa JSON-adatok a fenti streamelési lekérdezésben használt **metrikák bemeneti** adatait jelölik. 

- Három példa esemény szerepel az 1 perces időtartamon belül, az érték `T14:50`. 
- Mindhárom azonos `deviceId` értékű `978648`.
- A CPU-metrikus értékek `98` `95`eseményenként változnak , illetőleg. `80` Csak az első két példa esemény meghaladja a szabályban létrehozott CPU-riasztási szabályt.
- A riasztási szabály includeDim mezője a 2-es kulcsvolt. A példaesemények `NodeName`megfelelő 2. A három példaesemény `N024` `N024`értékei `N014` , illetve. A kimenetben csak a csomópont `N024` látható, mivel ez az egyetlen olyan adat, amely megfelel a magas PROCESSZOR riasztási feltételeinek. `N014`nem felel meg a magas CPU-küszöbértéknek.
- A riasztási szabály `filter` csak a 2-es kulcson `cluster` van konfigurálva, amely megfelel a mintaesemények mezőjének. A három példa esemény `C1` mindegyike értékkel rendelkezik, és megfelel a szűrési feltételeknek.

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

## <a name="example-output"></a>Példa kimenetre
Ez a példa kimeneti JSON-adatok azt mutatják, hogy egyetlen riasztási esemény készült a referenciaadatokban meghatározott CPU-küszöbérték szabály alapján. A kimeneti esemény tartalmazza a riasztás nevét, valamint a figyelembe vett mezők összesített (átlagos, min, max) összesítését. A kimeneti esemény adatai `NodeName` tartalmazzák a 2-es számú mezőkulcs értékét `N024` a szabály konfigurációja miatt. (A JSON-t úgy módosították, hogy az olvashatóság érdekében sortöréseket mutasson.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
