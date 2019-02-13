---
title: Tekintse át az erőforrás-használat az Azure szolgáltatás REST API-val |} A Microsoft Docs
description: Ismerje meg, tekintse át az Azure service erőforrás-használat az Azure REST API-k használatával.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: erikre
ms.openlocfilehash: d3db4166810da981ff0117536d8550a6b2203924
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106188"
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

A `{subscriptionGuid}` paraméter megadása kötelező, és tartalmaznia kell egy előfizetés-azonosító, amely az API-jogkivonatot a megadott hitelesítő adatokkal is olvasható. a `{reportName}`

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

Állítsa be a `timeframe` definíciót `Custom` dátumának hónap, dátum beépített beállításai és a egy időkereten kívül a hét beállítása.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>További lépések
- [Azure REST API használatának első lépései](https://docs.microsoft.com/rest/api/azure/)   
