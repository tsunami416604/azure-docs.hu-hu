---
title: Azure-szolgáltatások erőforrás-használatának áttekintése REST API-val | Microsoft Docs
description: Megtudhatja, hogyan tekintheti át az Azure-szolgáltatások erőforrás-használatát Azure REST API-kkal.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: eb444f090c1b2047e3d71c1b2ec52699a61bd880
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225932"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Azure-erőforráshasználat áttekintése a REST API-val

Az Azure Cost Management API-k az Azure-erőforrások használatának áttekintésében és kezelésében segítenek.

Ebből a cikkből megtudhatja, hogyan hozhat létre napi jelentést, amely egy vesszővel tagolt értéket tartalmazó dokumentumot készít az óránkénti használat adatairól, majd hogyan szabhatja testre a jelentést szűrőkkel, hogy lekérdezhesse a virtuális gépek, adatbázisok és címkézett adatok Azure-erőforráscsoportokban való használatát.

>[!NOTE]
> A Cost Management API jelenleg privát előzetes verzióban érhető el.

## <a name="create-a-basic-cost-management-report"></a>Alapszintű költségkezelési jelentés létrehozása

A Cost Management API `reports` műveletével határozhatja meg, hogyan jöjjenek létre a költségjelentések, és hol legyenek közzétéve a jelentések.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

A `{subscriptionGuid}` paraméter megadása kötelező, és tartalmaznia kell egy előfizetés-azonosítót, amely az API-jogkivonatban megadott hitelesítő adatok használatával olvasható be. A `{reportName}`

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):| Kötelező. Állítsa `application/json` értékre. |  
|*Authorization* (Engedélyezés):| Kötelező. Állítsa egy érvényes `Bearer` jogkivonatra. |

Konfigurálja a jelentés paramétereit a HTTP-kérelem törzsében. Az alábbi példában a jelentés úgy van beállítva, hogy minden aktív napon létrejöjjön egy CSV-fájl az Azure Storage-blobtárolóban, amely a `westus` erőforráscsoportban lévő összes erőforrás óránkénti költséginformációit tartalmazza.

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

A(z)

## <a name="filtering-reports"></a>Jelentések szűrése

Jelentés létrehozásakor a kérelem törzsének `filter` és `dimensions` szakaszában összpontosíthat az adott erőforrástípusok költségeire. Az előző kérelemtörzsben láthatja, hogyan szűrhet egy régió összes erőforrása alapján. 

### <a name="get-all-compute-usage"></a>Az összes számítási használat lekérése

A `ResourceType` dimenzióval készíthet jelentést az Azure-beli virtuális gép költségeiről az előfizetés összes régiójában.

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

### <a name="get-all-database-usage"></a>Az összes adatbázis használatának lekérése

A `ResourceType` dimenzióval készíthet jelentést az Azure SQL Database költségeiről az előfizetés összes régiójában.

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

### <a name="report-on-specific-instances"></a>Jelentéskészítés adott példányokról

A `Resource` dimenzióval adott erőforrások költségeiről készíthet jelentést.

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

### <a name="changing-timeframes"></a>Időkeretek módosítása

Állítsa a `timeframe` definíciót `Custom` értékre, ha a jelenlegi hét és a jelenlegi hónap beépített lehetőségén kívüli időkeretet szeretne beállítani.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>További lépések
- [Bevezetés az Azure REST API használatába](https://docs.microsoft.com/rest/api/azure/)   
