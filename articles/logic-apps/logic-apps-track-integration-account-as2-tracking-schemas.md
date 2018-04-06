---
title: AS2 követési sémák B2B figyelés - Azure Logic Apps |} Microsoft Docs
description: AS2 követési sémák használatával figyelheti az Azure-integráció fiókban tranzakciók B2B üzeneteit.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8605950f3cc07730ee13b91999076e3a093e8d92
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
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
| senderPartnerName | Karakterlánc | AS2 üzenet küldőjének partner neve. (Választható lehetőség) |
| receiverPartnerName | Karakterlánc | AS2 üzenetet fogadó partner neve. (Választható lehetőség) |
| as2To | Karakterlánc | AS2 üzenetet fogadó neve, a fejlécek az AS2-üzenet. (Mandatory) |
| as2From | Karakterlánc | AS2 üzenet küldőjének neve, a fejlécek az AS2-üzenet. (Mandatory) |
| agreementName | Karakterlánc | Az AS2-megállapodás, amelyhez az üzenetek fakadó problémák megoldásával neve. (Választható lehetőség) |
| irány | Karakterlánc | Az üzenet folyamat iránya küld és fogad. (Mandatory) |
| messageId | Karakterlánc | AS2-azonosító tartozik, az a fejlécek az AS2-üzenet (nem kötelező) |
| dispositionType |Karakterlánc | Üzenet törlése értesítés (MDN) típusú érték. (Választható lehetőség) |
| fileName | Karakterlánc | Fájl neve, az AS2 üzenet fejlécében. (Választható lehetőség) |
| isMessageFailed |Logikai | Hogy az AS2 üzenetet nem sikerült. (Mandatory) |
| isMessageSigned | Logikai | Hogy az AS2 üzenet aláírása. (Mandatory) |
| isMessageEncrypted | Logikai | Hogy az AS2 üzenet titkosítva van. (Mandatory) |
| isMessageCompressed |Logikai | Az AS2 üzenet tömörítették e. (Mandatory) |
| correlationMessageId | Karakterlánc | AS2-azonosító tartozik, az üzenetek a kivizsgált MDNs. (Választható lehetőség) |
| incomingHeaders |JToken álló Dictionary | Bejövő AS2 üzenet fejlécének adatai. (Választható lehetőség) |
| outgoingHeaders |JToken álló Dictionary | Kimenő AS2 üzenet fejlécének adatai. (Választható lehetőség) |
| isNrrEnabled | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Mandatory) |
| isMdnExpected | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Mandatory) |
| mdnType | Enum | Két érték engedélyezett **NotConfigured**, **szinkronizálási**, és **aszinkron**. (Mandatory) |

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
| senderPartnerName | Karakterlánc | AS2 üzenet küldőjének partner neve. (Választható lehetőség) |
| receiverPartnerName | Karakterlánc | AS2 üzenetet fogadó partner neve. (Választható lehetőség) |
| as2To | Karakterlánc | Az AS2 üzenetet megkapó partner neve. (Mandatory) |
| as2From | Karakterlánc | Az AS2 üzenetet küldő partner neve. (Mandatory) |
| agreementName | Karakterlánc | Az AS2-megállapodás, amelyhez az üzenetek fakadó problémák megoldásával neve. (Választható lehetőség) |
| irány |Karakterlánc | Az üzenet folyamat iránya küld és fogad. (Mandatory) |
| messageId | Karakterlánc | AS2 azonosító. (Választható lehetőség) |
| originalMessageId |Karakterlánc | AS2 eredeti azonosító. (Választható lehetőség) |
| dispositionType | Karakterlánc | MDN típusú érték. (Választható lehetőség) |
| isMessageFailed |Logikai | Hogy az AS2 üzenetet nem sikerült. (Mandatory) |
| isMessageSigned |Logikai | Hogy az AS2 üzenet aláírása. (Mandatory) |
| isNrrEnabled | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Mandatory) |
| állapotkód | Enum | Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Mandatory) |
| micVerificationStatus | Enum | Két érték engedélyezett **NotApplicable**, **sikeres**, és **sikertelen**. (Mandatory) |
| correlationMessageId | Karakterlánc | Korrelációs azonosítója. Az eredeti messaged azonosítója (az Üzenetazonosító az üzenet, amely MDN beállított). (Választható lehetőség) |
| incomingHeaders | JToken álló Dictionary | Azt jelzi, hogy a bejövő üzenet fejlécének adatai. (Választható lehetőség) |
| outgoingHeaders |JToken álló Dictionary | Azt jelzi, hogy a kimenő üzenet fejlécének adatai. (Választható lehetőség) |

## <a name="next-steps"></a>További lépések
* További információ a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* További információ [B2B üzenetek figyelése](logic-apps-monitor-b2b-message.md).   
* További információ [egyéni sémák követési B2B](logic-apps-track-integration-account-custom-tracking-schema.md).   
* További információ [sémák követési X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* További tudnivalók [Naplóelemzési a B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
