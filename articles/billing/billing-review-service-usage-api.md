---
title: Az Azure-szolgáltatások erőforrás-használatának áttekintése a REST API használatával | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure REST API-kat az Azure-szolgáltatások erőforrás-használatának áttekintéséhez.
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
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443066"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Az Azure-erőforrások használatának áttekintése a REST API használatával

A Azure Cost Management API-k segítségével áttekintheti és kezelheti az Azure-erőforrások felhasználását.

Ebből a cikkből megtudhatja, hogyan hozhat létre napi jelentést, amely egy vesszővel tagolt értéket tartalmazó dokumentumot készít az óránkénti használati adataival, majd hogyan használhatja a szűrőket a jelentés testreszabásához, hogy lekérdezze a virtuális gépek, adatbázisok és címkézett adatok használatát. Azure-erőforráscsoport erőforrásai.

>[!NOTE]
> A Cost Management API jelenleg privát előzetes verzióban érhető el.

## <a name="create-a-basic-cost-management-report"></a>Alapszintű Cost Management-jelentés létrehozása

A Cost Management API-val végzett művelettelmeghatározhatja,hogyarendszerhogyanhozzalétreaköltségeket,éshogyajelentésekhollesznekközzétéve.`reports`

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

A `{subscriptionGuid}` paraméter megadása kötelező, és tartalmaznia kell egy előfizetés-azonosítót, amely az API-jogkivonatban megadott hitelesítő adatok használatával olvasható. A`{reportName}`

A következő fejlécek szükségesek: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*| Kötelező. Állítsa a `application/json`következőre:. |  
|*Authorization:*| Kötelező. Érvényes `Bearer` tokenre van állítva. |

Konfigurálja a jelentés paramétereit a HTTP-kérelem törzsében. Az alábbi példában a jelentés úgy van beállítva, hogy minden nap, amikor az aktív, egy Azure Storage blob-tárolóba írt CSV-fájl, amely óradíj-információkat tartalmaz az erőforráscsoport `westus`összes erőforrásához.

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

A `filter` jelentés `dimensions` létrehozásakor a kérés törzsének és szakaszának meghatározása lehetővé teszi, hogy az adott erőforrástípusok költségeire koncentráljon. Az előző kérelem törzse azt mutatja be, hogyan szűrheti a régió összes erőforrását. 

### <a name="get-all-compute-usage"></a>Az összes számítási használat beolvasása

A `ResourceType` dimenzió használatával jelentheti be az Azure-beli virtuális gépek költségeit az előfizetésben az összes régióban.

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

### <a name="get-all-database-usage"></a>Az összes adatbázis-használat beolvasása

Használja a `ResourceType` dimenziót az előfizetéshez tartozó Azure SQL Database költségek jelentésére minden régióban.

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

### <a name="report-on-specific-instances"></a>Jelentés adott példányokról

A `Resource` dimenzió lehetővé teszi, hogy adott erőforrásokra vonatkozó költségeket jelentsen.

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

Állítsa be `timeframe` a definíciót úgy `Custom` , hogy egy időkeretet állítson be a héten kívüli időpontra a dátum és a hónap között a beépített lehetőségek közül.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>További lépések
- [Ismerkedés az Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
