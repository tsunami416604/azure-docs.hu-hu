---
title: Azure Resource Health-események beszerzése a REST API használatával | Microsoft Docs
description: Használja az Azure REST API-kat az Azure-erőforrások állapotának beszerzéséhez.
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 353bd65b0466902e450e38677a350a177a1d602c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451388"
---
# <a name="get-resource-health-using-the-rest-api"></a>Resource Health beolvasása a REST API használatával 

Ez a példa azt mutatja be, hogyan kérhető le az előfizetésben található Azure-erőforrások állapot-eseményeinek listája az [azure REST API](/rest/api/azure/)használatával.

A REST API dokumentációja és a további minták a [Azure monitor Rest-referenciában](/rest/api/monitor)érhetők el. 

## <a name="build-the-request"></a>A kérelem felépítése

A következő `GET` HTTP-kérelem használatával sorolja fel az előfizetéséhez tartozó állapotadatok listáját `2018-05-16` és `2018-06-20`közötti időtartamra.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Kérésfejlécek

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Érvényes `Bearer` [hozzáférési tokenre](/rest/api/azure/#authorization-code-grant-interactive-clients)van állítva. |  

### <a name="uri-parameters"></a>URI-paraméterek

| Név | Leírás |
| :--- | :---------- |
| subscriptionId | Az Azure-előfizetést azonosító előfizetés azonosítója. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)című témakört. |
| api-verzió | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum a fenti URL-cím részét képező API-Version `2015-04-01`tartalmazza.  |
| $filter | A visszaadott eredmények készletének csökkentésére szolgáló szűrési beállítás. A paraméter engedélyezett mintái a [tevékenységi naplók műveletéhez tartozó hivatkozásban](/rest/api/monitor/activitylogs/list#uri-parameters)érhetők el. A megjelenített példa a 2018-05-16 és 2018-06-20 közötti időtartományban lévő összes eseményt rögzíti. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nem szükséges a kérelem törzse.

## <a name="handle-the-response"></a>A válasz kezelése

Az 200-as állapotkódot a rendszer a Filter paraméternek megfelelő állapotadatok listáját adja vissza, valamint egy `nextlink` URI-t az eredmények következő oldalának lekéréséhez.

## <a name="example-response"></a>Példaválasz 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
