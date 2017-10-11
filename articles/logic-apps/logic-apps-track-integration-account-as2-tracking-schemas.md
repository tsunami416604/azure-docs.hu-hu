---
title: "AS2 követési sémák B2B figyelés - Azure Logic Apps |} Microsoft Docs"
description: "AS2 követési sémák használatával figyelheti az Azure-integráció fiókban tranzakciók B2B üzeneteit."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 31bd296dc5ed5ac6998a6c05ee80fd38b12d662c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Indítsa el, vagy a figyelő sikeres, a hibák és az üzenet tulajdonságai AS2 üzenetek és MDNs nyomon követése
Ezek AS2 követési sémák használhatja az Azure-integráció fiókban üzleti vállalatközi (B2B) tranzakciók figyeléséhez nyújt segítséget:

* AS2 üzenet követési séma
* AS2 MDN követési séma

## <a name="as2-message-tracking-schema"></a>AS2 üzenet követési séma
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Karakterlánc | AS2 üzenet küldőjének partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | AS2 üzenetet fogadó partner neve. (Választható) |
| as2To | Karakterlánc | AS2 üzenetet fogadó neve, a fejlécek az AS2-üzenet. (Kötelező) |
| as2From | Karakterlánc | AS2 üzenet küldőjének neve, a fejlécek az AS2-üzenet. (Kötelező) |
| agreementName | Karakterlánc | Az AS2-megállapodás, amelyhez az üzenetek fakadó problémák megoldásával neve. (Választható) |
| Iránya | Karakterlánc | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| messageId | Karakterlánc | AS2-azonosító tartozik, az a fejlécek az AS2-üzenet (nem kötelező) |
| dispositionType |Karakterlánc | Üzenet törlése értesítés (MDN) típusú érték. (Választható) |
| fileName | Karakterlánc | Fájl neve, az AS2 üzenet fejlécében. (Választható) |
| isMessageFailed |Logikai érték | Hogy az AS2 üzenetet nem sikerült. (Kötelező) |
| isMessageSigned | Logikai érték | Hogy az AS2 üzenet aláírása. (Kötelező) |
| isMessageEncrypted | Logikai érték | Hogy az AS2 üzenet titkosítva van. (Kötelező) |
| isMessageCompressed |Logikai érték | Az AS2 üzenet tömörítették e. (Kötelező) |
| correlationMessageId | Karakterlánc | AS2-azonosító tartozik, az üzenetek a kivizsgált MDNs. (Választható) |
| incomingHeaders |JToken álló Dictionary | Bejövő AS2 üzenet fejlécének adatai. (Választható) |
| outgoingHeaders |JToken álló Dictionary | Kimenő AS2 üzenet fejlécének adatai. (Választható) |
| isNrrEnabled | Logikai érték | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| isMdnExpected | Logikai érték | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| mdnType | Enum | Két érték engedélyezett **NotConfigured**, **szinkronizálási**, és **aszinkron**. (Kötelező) |

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN követési séma
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Karakterlánc | AS2 üzenet küldőjének partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | AS2 üzenetet fogadó partner neve. (Választható) |
| as2To | Karakterlánc | Az AS2 üzenetet megkapó partner neve. (Kötelező) |
| as2From | Karakterlánc | Az AS2 üzenetet küldő partner neve. (Kötelező) |
| agreementName | Karakterlánc | Az AS2-megállapodás, amelyhez az üzenetek fakadó problémák megoldásával neve. (Választható) |
| Iránya |Karakterlánc | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| messageId | Karakterlánc | AS2 azonosító. (Választható) |
| originalMessageId |Karakterlánc | AS2 eredeti azonosító. (Választható) |
| dispositionType | Karakterlánc | MDN típusú érték. (Választható) |
| isMessageFailed |Logikai érték | Hogy az AS2 üzenetet nem sikerült. (Kötelező) |
| isMessageSigned |Logikai érték | Hogy az AS2 üzenet aláírása. (Kötelező) |
| isNrrEnabled | Logikai érték | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| statusCode | Enum | Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| micVerificationStatus | Enum | Két érték engedélyezett **NotApplicable**, **sikeres**, és **sikertelen**. (Kötelező) |
| correlationMessageId | Karakterlánc | Korrelációs azonosítója. Az eredeti messaged azonosítója (az Üzenetazonosító az üzenet, amely MDN beállított). (Választható) |
| incomingHeaders | JToken álló Dictionary | Azt jelzi, hogy a bejövő üzenet fejlécének adatai. (Választható) |
| outgoingHeaders |JToken álló Dictionary | Azt jelzi, hogy a kimenő üzenet fejlécének adatai. (Választható) |

## <a name="next-steps"></a>Következő lépések
* További információ a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* További információ [B2B üzenetek figyelése](logic-apps-monitor-b2b-message.md).   
* További információ [egyéni sémák követési B2B](logic-apps-track-integration-account-custom-tracking-schema.md).   
* További információ [sémák követési X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* További tudnivalók [az Operations Management Suite-portálon B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
