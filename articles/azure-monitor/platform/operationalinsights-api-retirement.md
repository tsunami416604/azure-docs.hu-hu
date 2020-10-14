---
title: Azure Monitor API-nyugdíjazás
description: Ismerteti a OperationalInsights erőforrás-szolgáltató API régebbi verzióinak kivonulását.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c9a7ba63246f747a132c315e22452ca9c7144448
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058182"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API-verziójának nyugdíjazása
A Microsoft legalább 12 hónappal korábban értesítést küld egy API kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra. Megjelent egy új verzió (2020-08-01) a **OperationalInsights** erőforrás-szolgáltató API-khoz, és minden korábbi API-verziót kivonja a 2023. október 31-én. Mivel az új funkciók és funkciók és optimalizálások csak a jelenlegi API-hoz vannak hozzáadva, a lehető leghamarabb frissíteni kell a legújabb API-verzióra.

Október 31-ig a Azure Monitor a 2020-08-01-nél korábbi API-verziókat nem fogja támogatni a 2023. Ha nem szeretné frissíteni, a korábbi verziókból küldött kérelmeket a Azure Monitor szolgáltatás a 2023. október 31-ig továbbra is kiszolgálja.

## <a name="migration-steps"></a>A migrálás lépései
A használt konfigurációs módszertől függően frissítenie kell az új verziót a **Rest** -kérelmekben és a **Resource Manager-sablonokban**. Az API verziójának frissítéséhez kövesse az alábbi példákat:

1. REST API kérelmek az API-verziót használják a kérelem URL-címében. Cserélje le az adott verziót a legújabb verzióra (2020-08-01) az alábbi példában látható módon.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager-sablonok az erőforrás **apiVersion** tulajdonságában az API-verziót használják. Cserélje le az adott verziót a legújabb verzióra (2020-08-01) az alábbi példában látható módon.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Következő lépések

- Tekintse [meg a OperationalInsights-munkaterület API-ra mutató hivatkozást](https://docs.microsoft.com/rest/api/loganalytics/workspaces).
