---
title: A vadászati és a livestream-lekérdezések kezelése az Azure Sentinelben REST API használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy az Azure Sentinel-vadászati funkciói Hogyan vehetik igénybe Log Analytics "REST API a vadászati és a livestream-lekérdezések kezeléséhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: cd52f22004bf72f3328d1e6a0d1ec988c2406317
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660848"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>A vadászati és a livestream-lekérdezések kezelése az Azure Sentinelben REST API használatával

Az Azure Sentinel a Azure Monitor Log Analytics részeként készült, lehetővé teszi Log Analytics "REST API használatát a vadászati és a livestream-lekérdezések kezeléséhez. Ebből a dokumentumból megtudhatja, hogyan hozhat létre és kezelhet vadászati lekérdezéseket a REST API használatával.  Az ily módon létrehozott lekérdezések az Azure Sentinel felhasználói felületén fognak megjelenni.

A [mentett keresések API](/rest/api/loganalytics/savedsearches)-val kapcsolatos további részletekért tekintse meg a végleges REST API referenciát.

## <a name="api-examples"></a>API-példák

Az alábbi példákban cserélje le ezeket a helyőrzőket az alábbi táblázatban leírt helyettesítésre:

| Helyőrző | Csere erre |
|-|-|
| **SubscriptionId** | annak az előfizetésnek a neve, amelyre a vadászati vagy a livestream-lekérdezést alkalmazza. |
| **ResourceGroupName** | azon erőforráscsoport neve, amelyre a vadászati vagy a livestream-lekérdezést alkalmazza. |
| **{savedSearchId}** | egyedi azonosító (GUID) minden egyes vadászati lekérdezéshez. |
| **WorkspaceName** | a lekérdezés céljaként szolgáló Log Analytics munkaterület neve. |
| **DisplayName** | a lekérdezéshez választott megjelenítendő név. |
| **Leírás** | a vadászat vagy a livestream lekérdezés leírása. |
| **Taktikát** | a lekérdezésre vonatkozó, a megfelelő MITRE ATT&. |
| **Lekérdezés** | a lekérdezés lekérdezési kifejezése. |
|  

### <a name="example-1"></a>1\. példa

Ebből a példából megtudhatja, hogyan hozhat létre vagy frissíthet egy vadászati lekérdezést egy adott Azure Sentinel-munkaterületen.  A livestream-lekérdezések esetében a *"kategória"* kifejezést cserélje le a "Category" kifejezésre a "kategória" kifejezéssel: " *livestream-lekérdezések"* a **kérelem törzsében**: 

#### <a name="request-header"></a>Kérelem fejléce

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>A kérés törzse

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>2\. példa

Ebből a példából megtudhatja, hogyan törölhet egy vadászati vagy livestream-lekérdezést egy adott Azure Sentinel-munkaterülethez:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>3\. példa

Ez a példa azt mutatja be, hogy egy adott munkaterülethez tartozó vadászati vagy livestream-lekérdezést kér le:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan kezelheti a vadászati és a livestream-lekérdezéseket az Azure Sentinelben a Log Analytics API használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [Automatikus vadászati kampányok futtatása jegyzetfüzetek használatával](notebooks.md)