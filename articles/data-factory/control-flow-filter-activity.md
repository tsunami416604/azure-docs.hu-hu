---
title: Tevékenységszűrés az Azure Data Factoryban |} A Microsoft Docs
description: 'A tevékenység: Filter szűri a bemeneti adatok.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: 787c9393e2700bd7ed349b501e70abc4a0687b9c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021832"
---
# <a name="filter-activity-in-azure-data-factory"></a>Az Azure Data Factoryben a tevékenység szűréséhez
Egy tevékenység: Filter a folyamat segítségével egy kifejezést a bemeneti tömb vonatkoznak. 

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

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | Neve a `Filter` tevékenység. | Sztring | Igen
type | Meg kell **szűrő**. | Sztring | Igen
feltétel | A bemeneti szűréshez használandó feltétel. | Kifejezés | Igen
elem | A bemeneti tömb, amelyen szűrőt kell alkalmazni. | Kifejezés | Igen

## <a name="example"></a>Példa

Ebben a példában a folyamat két tevékenységet tartalmaz: **Szűrő** és **ForEach**. A szűrő tevékenység a bemeneti számtömbből 3-nál nagyobb értéket az elemek szűréséhez van konfigurálva. A ForEach tevékenység ezután a szűrt értékek ismétel, és megvárja, amíg a jelenlegi érték által megadott másodpercek számát.

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
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
