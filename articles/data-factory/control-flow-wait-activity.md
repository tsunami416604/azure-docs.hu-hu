---
title: "Várjon, amíg az Azure Data Factory tevékenység |} Microsoft Docs"
description: "A Várakozás tevékenység felfüggesztése a feldolgozási sor végrehajtása az adott időszakra vonatkozóan."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: shlo
ms.openlocfilehash: 3ff06ea07fcf5e391783575adf9dd5d99255eced
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="wait-activity-in-azure-data-factory"></a>Várjon, amíg az Azure Data Factory tevékenység
Amikor egy Várakozás tevékenységet használja egy folyamatot, a feldolgozási sor megvárja-e a soron következő tevékenységek végrehajtásának folytatása előtt a megadott időn. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Data Factory V1 dokumentáció](v1/data-factory-introduction.md).

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
név | Neve a `Wait` tevékenység. | Karakterlánc | Igen
type | Meg kell **Várjon, amíg**. | Karakterlánc | Igen
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

## <a name="next-steps"></a>Következő lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Ha a feltétel tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Tevékenység-ig](control-flow-until-activity.md)