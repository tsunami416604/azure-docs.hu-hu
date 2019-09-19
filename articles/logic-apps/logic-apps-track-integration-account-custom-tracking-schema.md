---
title: Egyéni követési sémák a B2B-üzenetek – Azure Logic Apps |} A Microsoft Docs
description: Egyéni követési sémákat az integrációs fiókok B2B-üzenetek monitorozása az Azure Logic Apps Enterprise Integration Pack-létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 76a9ece9e925543e856136a798a60038316caad9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203043"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Hozzon létre egyéni követési sémák, a végpontok közötti munkafolyamatokat az Azure Logic Apps figyelése

Nincs beépített követési is engedélyezheti a vállalatok munkafolyamatot, például követési AS2 vagy X12 különböző részei az üzeneteket. Egy logikai alkalmazást, a BizTalk Server, SQL Server vagy bármely más réteget, munkafolyamatok létrehozásakor tartalmazza, majd engedélyezheti az egyéni követési, amely rögzíti az eseményeket az elejéről, a munkafolyamat végén. 

Ez a cikk az egyéni kódot is használhatja a rétegek kívül a logikai alkalmazás. 

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

| Tulajdonság | Kötelező | Típus | Leírás |
| --- | --- | --- | --- |
| sourceType | Igen |   | A futtatási forrás típusa. Engedélyezett értékek a következők **Microsoft.Logic/workflows** és **egyéni**. |
| source | Igen |   | Ha a forrás típusa **Microsoft.Logic/workflows**, kövesse az ebben a sémában kell az adatforrások információit. Ha a forrás típusa **egyéni**, egy JToken sémája. |
| systemId | Igen | String | Logikai alkalmazás rendszer azonosítóját. |
| runId | Igen | String | A logikai alkalmazás futtatásának azonosítóját. |
| operationName | Igen | String | A művelet (például művelet vagy trigger) neve. |
| repeatItemScopeName | Igen | String | Ismételje meg a konfigurációelem neve, ha a művelet belül egy `foreach` / `until` ciklus. |
| repeatItemIndex | Igen | Integer | E művelet belül van-e egy `foreach` / `until` ciklus. Azt jelzi, hogy az ismétlődő elem index. |
| trackingId | Nem | String | Követési azonosító, az üzenetek korrelációját. |
| correlationId | Nem | String | Korrelációs azonosító, az üzenetek korrelációját. |
| clientRequestId | Nem | String | Ügyfél töltheti fel, hogy üzeneteket összekapcsolását. |
| eventLevel | Igen |   | Az esemény szintjét. |
| eventTime | Igen |   | ÉÉÉÉ-hh-DDTHH:MM:SS.00000Z UTC formátumban, az esemény időpontja. |
| recordType | Igen |   | A track rekord típusát. Az érték engedélyezett **egyéni**. |
| record | Igen |   | Egyéni rekord típusa. Engedélyezett formátuma JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokoll követési sémák

B2B-protokoll követési sémák kapcsolatos információkért lásd:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [B2B-üzenetek figyelése](logic-apps-monitor-b2b-message.md)
* Ismerje meg [az Azure Monitor naplóira B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
