---
title: Egyéni nyomkövetési sémák B2B-üzenetekhez
description: Olyan egyéni nyomkövetési sémákat hozhat létre, amelyek a Azure Logic Apps integrációs fiókjaiban található B2B-üzeneteket figyelik Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792794"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>A végpontok közötti munkafolyamatokat figyelő egyéni nyomkövetési sémák létrehozása Azure Logic Apps

A beépített nyomon követési funkció lehetővé teszi a vállalat és az üzleti munkafolyamat különböző részeinek, például az AS2-vagy X12-üzenetek követését. Ha logikai alkalmazást, BizTalk Server, SQL Server vagy bármely más réteget tartalmazó munkafolyamatokat hoz létre, akkor engedélyezheti az olyan egyéni nyomkövetést, amely az eseményeket a munkafolyamata elejétől a végéig naplózza. 

Ez a cikk egyéni kódot tartalmaz, amelyet a logikai alkalmazáson kívüli rétegekben is használhat. 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| Tulajdonság | Szükséges | Type (Típus) | Leírás |
| --- | --- | --- | --- |
| Forrás típusa | Igen |   | A futtatási forrás típusa. Az engedélyezett értékek: **Microsoft. Logic/munkafolyamatok** és **Egyéni**. |
| source | Igen |   | Ha a forrás típusa **Microsoft. Logic/munkafolyamatok**, a forrás adatainak követniük kell ezt a sémát. Ha a forrás típusa **Egyéni**, a séma egy JToken. |
| systemId | Igen | Sztring | Logikai alkalmazás rendszerazonosítója |
| RunId | Igen | Sztring | Logikai alkalmazás futtatásának azonosítója. |
| operationName | Igen | Sztring | A művelet neve (például művelet vagy trigger). |
| repeatItemScopeName | Igen | Sztring | Adja meg az elem nevét, ha a művelet `foreach`/`until` hurokon belül van. |
| repeatItemIndex | Igen | Egész szám | Azt jelzi, hogy a művelet egy `foreach`/`until` hurokon belül van-e. Az ismétlődő elemek indexét jelzi. |
| trackingId | Nem | Sztring | Követési azonosító, amely az üzenetek korrelációját okozhatja. |
| correlationId | Nem | Sztring | Korrelációs azonosító, amely az üzenetek korrelációját okozhatja. |
| ügyfélkérelem | Nem | Sztring | Az ügyfél feltöltheti az üzenetek korrelációját. |
| eventLevel | Igen |   | Az esemény szintje. |
| eventTime | Igen |   | Az esemény időpontja, UTC formátumban, éééé-hh-NNTÓÓ: PP: SS. 00000Z. |
| Rekordtípus | Igen |   | A nyomkövetési rekord típusa Az engedélyezett érték **Egyéni**. |
| rekord | Igen |   | Egyéni bejegyzéstípus. Az engedélyezett formátum a JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* További információ a [B2B-üzenetek monitorozásáról](logic-apps-monitor-b2b-message.md)
* Tudnivalók a [B2B-üzenetek nyomon követéséről Azure monitor naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
