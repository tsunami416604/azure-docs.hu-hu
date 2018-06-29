---
title: Várjon, amíg az Azure Data Factory tevékenység |} Microsoft Docs
description: A Várakozás tevékenység felfüggesztése a feldolgozási sor végrehajtása az adott időszakra vonatkozóan.
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
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 74a5d687535915fab7d518faaf916b98ab262c4b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053898"
---
# <a name="wait-activity-in-azure-data-factory"></a>Várjon, amíg az Azure Data Factory tevékenység
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

## <a name="type-properties"></a>A típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | Neve a `Wait` tevékenység. | Sztring | Igen
type | Meg kell **Várjon, amíg**. | Sztring | Igen
waitTimeInSeconds | A feldolgozási folytatása előtt megvárja-e a folyamat másodpercek száma. | Egész szám | Igen

## <a name="example"></a>Példa

> [!NOTE]
> Ez a szakasz a JSON-definíciók és futtatjuk a folyamatot a PowerShell Példaparancsok nyújt. Az Azure PowerShell és a JSON-definíciók használatával a Data Factory-folyamathoz létrehozásának részletes útmutatóját bemutatóért lásd: [oktatóanyag: Azure PowerShell használatával létrehozni egy adat-előállító](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>A Várakozás tevékenység-feldolgozási folyamat
Ebben a példában az adatcsatorna két tevékenység rendelkezik: **amíg** és **Várjon, amíg**. A Várakozás tevékenység egy másodperces várakozás van konfigurálva. A folyamat fut a webes tevékenység ismétlődő várakozási idő közötti minden egyes futtatásához egy második. 

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
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
