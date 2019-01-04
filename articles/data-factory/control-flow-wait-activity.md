---
title: Wait tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: A Várakozás tevékenység felfüggeszti a folyamat végrehajtását az adott időszakra vonatkozóan.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 731df55a11f4671670a65dac8a83927d81da454c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015797"
---
# <a name="wait-activity-in-azure-data-factory"></a>Wait tevékenység az Azure Data Factoryban
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

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | Neve a `Wait` tevékenység. | Karakterlánc | Igen
type | Meg kell **várjon**. | Karakterlánc | Igen
waitTimeInSeconds | A folyamat kivárja a feldolgozás folytatása előtt másodpercek számát. | Egész szám | Igen

## <a name="example"></a>Példa

> [!NOTE]
> Ez a szakasz a JSON-definíciók és minta PowerShell-parancsokat a folyamat futtatása biztosít. A részletes utasításokat követve hozzon létre egy Data Factory-folyamatot az Azure PowerShell és a JSON-definíciók bemutatóért lásd: [oktatóanyag: adat-előállító létrehozása az Azure PowerShell-lel](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Várakozás tevékenységet a folyamat
Ebben a példában a folyamat két tevékenységet tartalmaz: **Mindaddig, amíg** és **várjon**. A Várakozás tevékenység egy másodperces várakozás van konfigurálva. A folyamatfuttatások a webes tevékenység hurokba került a várakozási idő közötti minden egyes futtatásához egy másodperc. 

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
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
