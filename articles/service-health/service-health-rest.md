---
title: Azure-erőforrás-állapotesemények beszereznie a REST API használatával | Microsoft dokumentumok
description: Az Azure REST API-k segítségével az Azure-erőforrások állapoteseményeit szerezheti be.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654001"
---
# <a name="get-resource-health-using-the-rest-api"></a>Erőforrás-állapot beszereznie a REST API-val 

Ez a példacikk bemutatja, hogyan lehet lekérni az Azure-erőforrások állapotesemények listáját az előfizetésben az [Azure REST API használatával.](/rest/api/azure/)

A REST API-hoz teljes körű referenciadokumentáció és további minták érhetők el az [Azure Monitor REST-hivatkozásában.](/rest/api/monitor) 

## <a name="build-the-request"></a>A kérelem felépítése

Az alábbi `GET` HTTP-kérelem segítségével sorolja fel az előfizetés `2018-05-16` állapoteseményeit a között és `2018-06-20`a között.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Kérésfejlécek

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-paraméterek

| Név | Leírás |
| :--- | :---------- |
| subscriptionId | Az Azure-előfizetést azonosító előfizetés-azonosító. Ha több előfizetéssel rendelkezik, olvassa el [a Több előfizetés sel végzett témakört.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) |
| api-verzió | A kérelemhez használandó API-verzió.<br /><br /> Ez a dokumentum `2015-04-01`a fenti URL-ben található api-verzióra vonatkozik.  |
| $filter | A visszaadott eredmények készletének csökkentésére irányuló szűrési lehetőség. A paraméter megengedett mintái [a Tevékenységnaplók művelet hivatkozásában](/rest/api/monitor/activitylogs/list#uri-parameters)érhetők el. A bemutatott példa az összes eseményt rögzíti a 2018-05-16 és 2018-06-20 közötti időtartományban |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ehhez a művelethez nincs szükség kéréstörzsre.

## <a name="handle-the-response"></a>A válasz kezelése

A 200-as állapotkód a szűrőparaméternek megfelelő állapotesemény-értékek `nextlink` listájával, valamint egy URI-val együtt jelenik meg az eredmények következő lapjának lekéréséhez.

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
