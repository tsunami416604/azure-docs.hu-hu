---
title: Az Azure Data Factory tevékenység szűréséhez |} Microsoft Docs
description: A szűrő tevékenység szűrése az adatokat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: b3b26869a84b8519ced19a4c93a6d39d6ed20f9b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050352"
---
# <a name="filter-activity-in-azure-data-factory"></a>Az Azure Data Factory szűrő tevékenység
Egy folyamatot egy szűrő tevékenység segítségével egy kifejezést vonatkozik a bemeneti tömb. 

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>A típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | Neve a `Filter` tevékenység. | Sztring | Igen
type | Meg kell **szűrő**. | Sztring | Igen
feltétel | A bemeneti szűréshez használandó feltétel. | Kifejezés | Igen
elem | A bemeneti tömb amelyen szűrőt kell alkalmazni. | Kifejezés | Igen

## <a name="example"></a>Példa

Ebben a példában az adatcsatorna két tevékenység rendelkezik: **szűrő** és **ForEach**. A szűrő konfigurálta a bemeneti tömb 3-nál nagyobb értéket elemek szűrésére. A ForEach tevékenység majd elemein végiglépkedve szűrt értékeit, és megvárja, az aktuális értéke által meghatározott másodpercben.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
