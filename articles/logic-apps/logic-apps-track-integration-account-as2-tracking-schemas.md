---
title: AS2 követési sémák B2B figyelés - Azure Logic Apps |} Microsoft Docs
description: AS2 követési sémák használatával figyelheti az Azure-integráció fiókban tranzakciók B2B üzeneteit.
author: padmavc
manager: jeconnoc
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
ms.openlocfilehash: 48e39fd20716e962c4a3e367fdff18e0b4fba32d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300881"
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
| senderPartnerName | Sztring | AS2 üzenet küldőjének partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | AS2 üzenetet fogadó partner neve. (Választható lehetőség) |
| as2To | Sztring | AS2 üzenetet fogadó neve, a fejlécek az AS2-üzenet. (Kötelező) |
| as2From | Sztring | AS2 üzenet küldőjének neve, a fejlécek az AS2-üzenet. (Kötelező) |
| agreementName | Sztring | Az AS2-megállapodás, amelyhez az üzenetek fakadó problémák megoldásával neve. (Választható lehetőség) |
| irány | Sztring | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| messageId | Sztring | AS2-azonosító tartozik, az a fejlécek az AS2-üzenet (nem kötelező) |
| dispositionType |Sztring | Üzenet törlése értesítés (MDN) típusú érték. (Választható lehetőség) |
| fileName | Sztring | Fájl neve, az AS2 üzenet fejlécében. (Választható lehetőség) |
| isMessageFailed |Logikai | Hogy az AS2 üzenetet nem sikerült. (Kötelező) |
| isMessageSigned | Logikai | Hogy az AS2 üzenet aláírása. (Kötelező) |
| isMessageEncrypted | Logikai | Hogy az AS2 üzenet titkosítva van. (Kötelező) |
| isMessageCompressed |Logikai | Az AS2 üzenet tömörítették e. (Kötelező) |
| correlationMessageId | Sztring | AS2-azonosító tartozik, az üzenetek a kivizsgált MDNs. (Választható lehetőség) |
| incomingHeaders |JToken álló Dictionary | Bejövő AS2 üzenet fejlécének adatai. (Választható lehetőség) |
| outgoingHeaders |JToken álló Dictionary | Kimenő AS2 üzenet fejlécének adatai. (Választható lehetőség) |
| isNrrEnabled | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| isMdnExpected | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
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
| senderPartnerName | Sztring | AS2 üzenet küldőjének partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | AS2 üzenetet fogadó partner neve. (Választható lehetőség) |
| as2To | Sztring | Az AS2 üzenetet megkapó partner neve. (Kötelező) |
| as2From | Sztring | Az AS2 üzenetet küldő partner neve. (Kötelező) |
| agreementName | Sztring | Az AS2-megállapodás, amelyhez az üzenetek fakadó problémák megoldásával neve. (Választható lehetőség) |
| irány |Sztring | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| messageId | Sztring | AS2 azonosító. (Választható lehetőség) |
| originalMessageId |Sztring | AS2 eredeti azonosító. (Választható lehetőség) |
| dispositionType | Sztring | MDN típusú érték. (Választható lehetőség) |
| isMessageFailed |Logikai | Hogy az AS2 üzenetet nem sikerült. (Kötelező) |
| isMessageSigned |Logikai | Hogy az AS2 üzenet aláírása. (Kötelező) |
| isNrrEnabled | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| állapotkód | Enum | Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| micVerificationStatus | Enum | Két érték engedélyezett **NotApplicable**, **sikeres**, és **sikertelen**. (Kötelező) |
| correlationMessageId | Sztring | Korrelációs azonosítója. Az eredeti messaged azonosítója (az Üzenetazonosító az üzenet, amely MDN beállított). (Választható lehetőség) |
| incomingHeaders | JToken álló Dictionary | Azt jelzi, hogy a bejövő üzenet fejlécének adatai. (Választható lehetőség) |
| outgoingHeaders |JToken álló Dictionary | Azt jelzi, hogy a kimenő üzenet fejlécének adatai. (Választható lehetőség) |

## <a name="next-steps"></a>További lépések
* További információ a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* További információ [B2B üzenetek figyelése](logic-apps-monitor-b2b-message.md).   
* További információ [egyéni sémák követési B2B](logic-apps-track-integration-account-custom-tracking-schema.md).   
* További információ [sémák követési X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* További tudnivalók [Naplóelemzési a B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
