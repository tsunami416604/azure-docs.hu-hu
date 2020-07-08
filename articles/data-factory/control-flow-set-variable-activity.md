---
title: Változó tevékenység beállítása Azure Data Factoryban
description: Megtudhatja, hogyan állíthatja be egy Data Factory-folyamatban definiált meglévő változó értékét a változó beállítása tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: a0b5fa16658d3e354bcb4f90ad998997fc844a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83832785"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Változó tevékenység beállítása Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A változó beállítása tevékenységgel egy Data Factory-folyamatban definiált karakterlánc, bool vagy Array típusú változó értékét állíthatja be.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
name | A tevékenység neve a folyamatban | igen
leírás | A tevékenység működését leíró szöveg | nem
típus | **SetVariable** értékre kell állítani | igen
value | Karakterlánc-vagy kifejezési objektum értéke, amelyhez a változó hozzá van rendelve | igen
variableName | A tevékenység által beállított változó neve | igen

## <a name="incrementing-a-variable"></a>Változó növelése

Az Azure Data Factory változóit érintő gyakori forgatókönyvek egy-egy változót használnak egy-egy vagy foreach-tevékenységen belül. A set változó tevékenységben nem hivatkozhat a mezőben beállított változóra `value` . A korlátozás megkerülő megoldásához állítson be egy ideiglenes változót, majd hozzon létre egy második set változó tevékenységet. A második set változó tevékenység beállítja az iteráció értékét az ideiglenes változóra. 

Az alábbiakban egy példa látható erre a mintára:

![Növekmény változó](media/control-flow-set-variable-activity/increment-variable.png "Növekmény változó")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>További lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenység hozzáfűzése](control-flow-append-variable-activity.md)
