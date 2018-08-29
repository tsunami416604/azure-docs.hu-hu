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
ms.openlocfilehash: 68c5d6e68562d4027c102e1bde42c775648e58c4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124843"
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

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| sourceType |   | A futtatási forrás típusa. Engedélyezett értékek a következők **Microsoft.Logic/workflows** és **egyéni**. (Kötelező) |
| Forrás |   | Ha a forrás típusa **Microsoft.Logic/workflows**, kövesse az ebben a sémában kell az adatforrások információit. Ha a forrás típusa **egyéni**, egy JToken sémája. (Kötelező) |
| Rendszerazonosító | Sztring | Logikai alkalmazás rendszer azonosítóját. (Kötelező) |
| runId | Sztring | A logikai alkalmazás futtatásának azonosítóját. (Kötelező) |
| operationName | Sztring | A művelet (például művelet vagy trigger) neve. (Kötelező) |
| repeatItemScopeName | Sztring | Ismételje meg a konfigurációelem neve, ha a művelet belül egy `foreach` / `until` ciklus. (Kötelező) |
| repeatItemIndex | Egész szám | E művelet belül van-e egy `foreach` / `until` ciklus. Azt jelzi, hogy az ismétlődő elem index. (Kötelező) |
| trackingId | Sztring | Követési azonosító, az üzenetek korrelációját. (Választható lehetőség) |
| correlationId | Sztring | Korrelációs azonosító, az üzenetek korrelációját. (Választható lehetőség) |
| clientRequestId | Sztring | Ügyfél töltheti fel, hogy üzeneteket összekapcsolását. (Választható lehetőség) |
| eventLevel |   | Az esemény szintjét. (Kötelező) |
| eventTime |   | ÉÉÉÉ-hh-DDTHH:MM:SS.00000Z UTC formátumban, az esemény időpontja. (Kötelező) |
| RecordType |   | A track rekord típusát. Az érték engedélyezett **egyéni**. (Kötelező) |
| rekord |   | Egyéni rekord típusa. Engedélyezett formátuma JToken. (Kötelező) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokoll követési sémák

B2B-protokoll követési sémák kapcsolatos információkért lásd:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [B2B-üzenetek figyelése](logic-apps-monitor-b2b-message.md)
* Ismerje meg [a Log Analytics B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)