---
title: Szűrési tevékenység Azure Data Factoryban | Microsoft Docs
description: A szűrő tevékenység szűri a bemeneteket.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: a7e2e735baa7e40b4170d3397327e90fc1a5d2d5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141675"
---
# <a name="filter-activity-in-azure-data-factory"></a>Szűrési tevékenység Azure Data Factory
Egy folyamat szűrési tevékenységével szűrő kifejezést alkalmazhat egy bemeneti tömbre. 

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

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
name | A `Filter` tevékenység neve. | Sztring | Igen
type | Szűrésre kell beállítani. | Sztring | Igen
condition | A bemenet szűréséhez használandó feltétel. | Kifejezés | Igen
items | Az a bemeneti tömb, amelyen alkalmazni kell a szűrőt. | Kifejezés | Igen

## <a name="example"></a>Példa

Ebben a példában a folyamat két tevékenységgel rendelkezik: **Szűrő** -és **foreach**. A szűrési tevékenység úgy van konfigurálva, hogy a bemeneti tömböt a 3 értéknél nagyobb értékű elemekre szűrje. A ForEach tevékenység ekkor megismétli a szűrt értékeket, és megvárja az aktuális érték által megadott másodpercek számát.

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
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
