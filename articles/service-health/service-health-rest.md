---
title: Azure-erőforrás lekérése a REST API-val hálózatállapot-események |} A Microsoft Docs
description: Az Azure REST API-k használatával a Szolgáltatásállapot-események lekérése az Azure-erőforrások.
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878391"
---
# <a name="get-resource-health-using-the-rest-api"></a>A REST API használatával a Resource Health beolvasása 

Ez a példa a cikk bemutatja, hogyan hálózatállapot-események az előfizetés az Azure-erőforrások listájának lekéréséhez a [Azure REST API](/rest/api/azure/).

Teljes dokumentáció és a REST API további minták érhetők el a [Azure Monitor REST-referencia](/rest/api/monitor). 

## <a name="build-the-request"></a>A kérelem létrehozása

Használja a következő `GET` HTTP-kérelem a különböző között eltelt idő, az előfizetés állapotának eseményeinek listázása `2018-05-16` és `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Kérelemfejlécek

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|  
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-paraméterek

| Name (Név) | Leírás |
| :--- | :---------- |
| subscriptionId | Az előfizetés-azonosítója, amely azonosítja az Azure-előfizetéssel. Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| API-verzió | A használandó a kérelem API-verzió.<br /><br /> Ez a dokumentum ismerteti az api-version `2015-04-01`, a fenti URL-címe megtalálható.  |
| $filter | A szűrési a beállítással úgy csökkenthető a kapott találatok közül készletét. Ez a paraméter a megengedett minták érhetők el [a hivatkozás a vizsgálati naplók művelethez](/rest/api/monitor/activitylogs/list#uri-parameters). A példában látható rögzíti eseményeit a 2018-05-16. és 2018-06-20 időtartomány |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>A kérés törzse

Ez a művelet nem a kérelem törzsében van szükség.

## <a name="handle-the-response"></a>A válasz kezelése

A megfelelő a szűrő paramétert, az egészségügyi esemény értékekből álló listát ad vissza 200-as állapotkód egy `nextlink` URI-t az eredmények a következő lap beolvasásához.

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
