---
title: Változótevékenység beállítása az Azure Data Factoryban
description: Ismerje meg, hogyan állítható be a Változó beállítása tevékenység egy Data Factory-folyamatban definiált meglévő változó értékének beállítása
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
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417980"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Változótevékenység beállítása az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Változó beállítása tevékenység segítségével állítsa be a Data Factory-folyamatban definiált Karakterlánc, Bool vagy Tömb típusú meglévő változó értékét.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
név | A folyamatban lévő tevékenység neve | igen
leírás | A tevékenység tevékenységét leíró szöveg | nem
type | **SetVariable (SetVariable)** beállításnak kell lennie. | igen
érték | A változóhoz rendelt karakterlánc-konstans vagy kifejezésobjektum-érték | igen
változóNév | A tevékenység által beállítandó változó neve | igen

## <a name="incrementing-a-variable"></a>Változó növekményének megforgatása

Az Azure Data Factory változóit érintő gyakori forgatókönyv egy változót használ iterátorként egy-vagy foreach tevékenységen belül. Halmazváltozó-tevékenységben nem lehet hivatkozni a `value` mezőben beállított változóra. A korlátozás megkerüléséhez állítson be egy ideiglenes változót, majd hozzon létre egy második halmazváltozó-tevékenységet. A második halmazváltozó-tevékenység az iterátor értékét az ideiglenes változóra állítja be. 

Az alábbiakban egy példa erre a mintára:

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
További információ a Data Factory által támogatott kapcsolódó vezérlési folyamattevékenységről: 

- [Változó tevékenység hozzáfűzése](control-flow-append-variable-activity.md)
