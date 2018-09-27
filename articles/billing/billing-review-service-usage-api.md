---
title: Tekintse át az erőforrás-használat az Azure szolgáltatás REST API-val |} A Microsoft Docs
description: Ismerje meg, tekintse át az Azure service erőforrás-használat az Azure REST API-k használatával.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: erikre
ms.openlocfilehash: bddb72f2b76d6e652dd26f2e383b7d06fded881b
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395457"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Tekintse át az Azure erőforrás-használati REST API használatával

Az Azure Cost Management API-k segítségével tekintse át és az Azure-erőforrások fogyasztásának kezeléséhez.

Ebből a cikkből elsajátíthatja, hogyan hoz létre egy vesszővel tagolt dokumentum óránkénti használati adatait, és szűrők használatával testre szabhatja a jelentést, hogy lekérdezheti, ha a virtuális gépek, adatbázisok, a használati és megjelölve napi jelentés létrehozása Azure-erőforráscsoportban lévő erőforrásokat.

>[!NOTE]
> A Cost Management API-t jelenleg private preview verzióban.

## <a name="create-a-basic-cost-management-report"></a>Alapszintű cost management jelentés létrehozása

Használja a `reports` műveletet a Cost Management API adható a költségek jelentéskészítési létrehozásának módját, és ahol a jelentések lesz közzétéve.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

A `{subscriptionGuid}` paraméter megadása kötelező, és tartalmaznia kell egy előfizetés-azonosító, amely az API-jogkivonat a hitelesítő adatok provieed használatával olvashatja. a `{reportName}`

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*| Kötelező. Állítsa be `application/json`. |  
|*Hitelesítés:*| Kötelező. Egy érvényes értékre `Bearer` token. |

Adja meg a jelentés paramétereit a HTTP-kérés törzse. Az alábbi példában a jelentésben van állítva minden nap, amikor aktív, CSV-fájl írása egy Azure Storage blob-tárolóba, és óránként költségadatokat erőforráscsoportban lévő összes erőforrást tartalmaz létrehozásához `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

a

## <a name="filtering-reports"></a>Jelentések szűrése

A `filter` és `dimensions` szakaszában a kérelem törzsében a költségeket az adott erőforrás-típus egy jelentés összpontosíthat a létrehozásakor. Az előző kérelem törzse mutatja be szűrhet az összes egy adott régió erőforrásait. 

### <a name="get-all-compute-usage"></a>Minden számítási használatának megtekintése

Használja a `ResourceType` dimenzió minden régióban az Azure virtuális gépek költségeit az előfizetésében jelenti.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Az összes adatbázis használatának megtekintése

Használja a `ResourceType` dimenzió jelentés Azure SQL Database a költségek az előfizetésében, minden régióban.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Jelentés az adott példányok

A `Resource` dimenzió lehetővé teszi az erőforrások költségek jelentése.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Változó időkeretének meghatározása

Állítsa be a `timeframe` definíciót `Custom` egy időkereten kívül a hét beállítani a dátumot és a hónap, dátum, a beállítások területen.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>További lépések
- [Azure REST API használatának első lépései](https://docs.microsoft.com/rest/api/azure/)   
