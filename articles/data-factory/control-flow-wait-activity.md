---
title: Várakozási tevékenység az Azure Data Factoryban
description: A Várakozás tevékenység felfüggeszti a folyamat végrehajtását a megadott időszakra.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678238"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Várakozási tevékenység végrehajtása az Azure Data Factoryban
Ha Wait tevékenységet használ egy folyamatban, akkor a folyamat kivárja a megadott időtartamot, és csak az után folytatja a további tevékenységek futtatását. 

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | A `Wait` tevékenység neve. | Sztring | Igen
type | A Várakozás **beállításnak**kell lennie. | Sztring | Igen
waitTimeInSeconds | Az a másodpercszám, ameddig a folyamat várakozik a feldolgozás folytatása előtt. | Egész szám | Igen

## <a name="example"></a>Példa

> [!NOTE]
> Ez a szakasz JSON-definíciókat és minta PowerShell-parancsokat biztosít a folyamat futtatásához. A Data Factory-folyamat Azure PowerShell- és JSON-definíciók használatával történő létrehozásához részletes útmutatót az oktatóanyag ban [talál: adatgyár létrehozása az Azure PowerShell használatával](quickstart-create-data-factory-powershell.md)című témakörben.

### <a name="pipeline-with-wait-activity"></a>Folyamat várakozási tevékenységgel
Ebben a példában a folyamat két tevékenységet rendelkezik: **Ig** és **Várjon**. A Várakozás tevékenység úgy van beállítva, hogy várjon egy másodpercet. A folyamat egy ciklusban futtatja a webes tevékenységet, és minden futtatás között egy másodperc várakozási idő áll. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresstevékenységet](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
