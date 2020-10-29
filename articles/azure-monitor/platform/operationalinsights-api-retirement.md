---
title: Azure Monitor API-nyugdíjazás
description: Ismerteti a OperationalInsights erőforrás-szolgáltató API régebbi verzióinak kivonulását.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 4075e27c3711cbc4c584005126b53f7c99942049
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926725"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API-verziójának nyugdíjazása
A Microsoft legalább 12 hónappal korábban értesítést küld egy API kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra. Megjelent egy új verzió (2020-08-01) a **OperationalInsights** erőforrás-szolgáltató API-khoz, és minden korábbi API-verziót 2014. február 29-én kivonja.

Javasoljuk, hogy a 2020-08-01-es verzió használatának megkezdéséhez használja az új funkciók előnyeit, például a [dedikált fürtöt](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters), az [ügyfél által felügyelt kulcsokat](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys), a [privát kapcsolatot](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security) és az [adatexportálást](https://docs.microsoft.com/azure/azure-monitor/platform/logs-data-export). Emellett az új funkciók és funkciók és optimalizációk csak az aktuális API-hoz lesznek hozzáadva.

2014. február 29-én Azure Monitor többé nem támogatja a korábbi API-verziókat, mint a 2020-08-01. Ha inkább nem szeretné frissíteni, a korábbi verziókból küldött kérelmeket a Azure Monitor szolgáltatás a 2014. február 29-én továbbra is kiszolgálja.

## <a name="migration-steps"></a>A migrálás lépései
A használt konfigurációs módszertől függően frissítenie kell az új verziót a **Rest** -kérelmekben és a **Resource Manager-sablonokban** . Az API verziójának frissítéséhez kövesse az alábbi példákat:

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

- Tekintse [meg a OperationalInsights-munkaterület API-ra mutató hivatkozást](/rest/api/loganalytics/workspaces).
