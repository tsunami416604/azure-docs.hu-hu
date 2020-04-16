---
title: Tevékenység szűrése az Azure Data Factoryban
description: A Szűrő tevékenység szűri a bemeneteket.
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
ms.openlocfilehash: 2c8ada22b4492bef0239b6f811d5a7bd58e58510
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417213"
---
# <a name="filter-activity-in-azure-data-factory"></a>Tevékenység szűrése az Azure Data Factoryban
A folyamat szűrőtevékenységével szűrőkifejezést alkalmazhat egy bemeneti tömbre. 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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
név | A `Filter` tevékenység neve. | Sztring | Igen
type | A **szűrőbeállításra**kell beállítani. | Sztring | Igen
Feltétel | A bemenet szűréséhez használandó feltétel. | Kifejezés | Igen
Elemek | Bemeneti tömb, amelyre szűrőt kell alkalmazni. | Kifejezés | Igen

## <a name="example"></a>Példa

Ebben a példában a folyamat két tevékenységet rendelkezik: **Szűrő** és **ForEach**. A Szűrő tevékenység úgy van beállítva, hogy szűrje a bemeneti tömböt a 3-nál nagyobb értékű elemekhez. A ForEach tevékenység ezután a szűrt értékekfelett itat, és a **változótesztet** az aktuális értékre állítja be.

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
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
