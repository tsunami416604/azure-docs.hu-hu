---
title: Egyéni követési sémák B2B figyelés - Azure Logic Apps |} Microsoft Docs
description: Hozzon létre egyéni követési sémák figyelése az Azure-integráció fiókban tranzakciók B2B üzeneteit.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 431235370c52be4c6e1ad6cd1af6a412e9eac230
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299834"
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>A teljes, végpontok közötti munkafolyamat figyelése nyomon követése
Nincs beépített nyomon követése, hogy engedélyezheti a vállalatok munkafolyamat, például követési AS2 vagy X12 egyes részeinek üzeneteket. Logikai alkalmazás, a BizTalk Serveren, SQL Server vagy más réteg munkafolyamatok létrehozásakor tartalmazza, majd engedélyezheti egyéni, amely eseményeket naplózza az elejéről, a munkafolyamat végén. 

Ez a témakör a rétegek kívül a logikai alkalmazás használhat egyéni kódot. 

## <a name="custom-tracking-schema"></a>Egyéni követési séma
````java

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

````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| sourceType |   | A futtatási forrás típusa. Két érték engedélyezett **Microsoft.Logic/workflows** és **egyéni**. (Kötelező) |
| Forrás |   | Ha a forrás típusa **Microsoft.Logic/workflows**, kövesse az ebben a sémában kell az adatforrásra vonatkozó információ. Ha a forrás típusa **egyéni**, a séma egy JToken. (Kötelező) |
| Rendszerazonosító | Sztring | Logic app rendszer azonosítóját. (Kötelező) |
| runId | Sztring | Logikai alkalmazás futtatásához azonosítóját. (Kötelező) |
| operationName | Sztring | A művelet (például a művelet vagy az eseményindító) neve. (Kötelező) |
| repeatItemScopeName | Sztring | Ismételje meg az elem neve, ha a művelet belül egy `foreach` / `until` hurok. (Kötelező) |
| repeatItemIndex | Egész szám | Hogy a művelet belül van-e egy `foreach` / `until` hurok. Azt jelzi, hogy az ismétlődő elemek index. (Kötelező) |
| trackingId | Sztring | Nyomkövetési azonosító, az üzenetek összefüggéseket. (Választható lehetőség) |
| correlationId | Sztring | Korrelációs azonosító, az üzenetek összefüggéseket. (Választható lehetőség) |
| clientRequestId | Sztring | Ügyfél töltheti fel az üzenetek összefüggéseket. (Választható lehetőség) |
| EventLevel |   | Az esemény szintje. (Kötelező) |
| eventTime |   | ÉÉÉÉ-hh-DDTHH:MM:SS.00000Z UTC formátumban, az esemény időpontja. (Kötelező) |
| recordType |   | A track rekord típusa. Az engedélyezett értéket **egyéni**. (Kötelező) |
| rekord |   | Egyéni rekordtípus. Engedélyezett JToken érvénytelen. (Kötelező) |

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll követési sémák
Követés sémák B2B protokoll kapcsolatos információkért lásd:
* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>További lépések
* További információ [B2B üzenetek figyelése](logic-apps-monitor-b2b-message.md).   
* További tudnivalók [Naplóelemzési a B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* További információ a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md).
