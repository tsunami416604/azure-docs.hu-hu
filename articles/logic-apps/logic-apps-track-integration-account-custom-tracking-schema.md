---
title: Egyéni követési sémák B2B üzenetekhez
description: Egyéni követési sémák létrehozása a B2B-üzenetek figyeléséhez az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903052"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Egyéni követési sémák létrehozása, amelyek az Azure Logic A teljes munkafolyamatait figyelik

Az Azure Logic Apps beépített nyomon követést, amely a munkafolyamat egyes részeihez engedélyezhető. Beállíthatja azonban az egyéni nyomon követést, amely az eseményeket a munkafolyamatok elejétől a végéig naplózza, például olyan munkafolyamatokat, amelyek logikai alkalmazást, BizTalk Servert, SQL Servert vagy bármely más réteget tartalmaznak. Ez a cikk egyéni kódot tartalmaz, amely a logikai alkalmazáson kívüli rétegekben használható.

## <a name="custom-tracking-schema"></a>Egyéni követési séma

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| forrástípus | Igen | Sztring | A futtatási forrás típusa az `Microsoft.Logic/workflows`alábbi megengedett értékekkel: ,`custom` |
| source | Igen | Karakterlánc vagy JToken | Ha a forrástípus a `Microsoft.Logic/workflows`forrástípus, a forrásinformációnak követnie kell ezt a sémát. Ha a forrástípus a `custom`, a séma egy JToken. |
| systemId | Igen | Sztring | Logikai alkalmazásrendszer azonosítója |
| futtatási azonosító | Igen | Sztring | A logikai alkalmazás futtatási azonosítója |
| operationName | Igen | Sztring | A művelet neve, például művelet vagy eseményindító |
| repeatItemScopeName | Igen | Sztring | Elemnév ismétlése, ha `foreach`a `until` művelet egy vagy cikluson belül van |
| repeatItemIndex | Igen | Egész szám | Azt jelzi, hogy a `foreach` `until` művelet egy vagy cikluson belül van, és az ismétlődő cikkindexszám. |
| nyomon követésazonosító | Nem | Sztring | Az üzenetek összefüggéséhez kövesse az azonosítót |
| correlationId | Nem | Sztring | Korrelációs azonosító az üzenetek korrelációjához |
| clientRequestId | Nem | Sztring | Az ügyfél feltöltheti ezt a tulajdonságot az üzenetek korrelációja érdekében |
| eventLevel | Igen | Sztring | Az esemény szintje |
| eventTime | Igen | DateTime | Az esemény időpontja UTC formátumban: *YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType (rekordtípus) | Igen | Sztring | A csak engedélyezett értékkel rendelkező nyomrekord típusa:`custom` |
| Rekord | Igen | JToken | Egyéni bejegyzéstípus csak JToken formátummal |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokollkövetési sémák

A B2B protokollkövetési sémákról a következő témakörökben talál további információt:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* További információ a [B2B-üzenetek figyeléséről az Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)