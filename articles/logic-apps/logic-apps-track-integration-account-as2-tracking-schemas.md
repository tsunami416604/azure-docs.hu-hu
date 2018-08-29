---
title: AS2-követési sémák B2B-üzenetek – Azure Logic Apps |} A Microsoft Docs
description: Hozzon létre az integrációs fiókok B2B-üzenetek monitorozása az Azure Logic Apps Enterprise Integration Pack-AS2-követési sémák
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 6c4144d26042729684e507b1afaa5e3006d8a34e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125930"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Sémák nyomon követési AS2-üzenetek és visszaigazolással integrációs fiókok az Azure Logic Apps létrehozása

Segítségével nyomon, hogy sikeres, a hibákat, és a vállalatközi (B2B) tranzakciókra vonatkozóan üzenettulajdonságok, az integrációs fiókban lévő ezek AS2-követési sémák is használhatja:

* AS2-üzenet követési séma
* AS2 MDN követési séma

## <a name="as2-message-tracking-schema"></a>AS2-üzenet követési séma

```json
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
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | AS2 üzenetet küldő partner nevének. (Választható lehetőség) |
| receiverPartnerName | Sztring | AS2 üzenetet fogadó partner nevének. (Választható lehetőség) |
| as2To | Sztring | AS2 üzenetet fogadó nevét, az AS2-üzenet fejlécét. (Kötelező) |
| as2From | Sztring | AS2 üzenet küldője nevét, az AS2-üzenet fejlécét. (Kötelező) |
| agreementname tulajdonság | Sztring | Az AS2-megállapodás, amelyhez az üzenetek elhárulnak neve. (Választható lehetőség) |
| irány | Sztring | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| messageId | Sztring | AS2-Üzenetazonosító, a a fejlécek az AS2-üzenet (nem kötelező) |
| dispositionType |Sztring | Üzenet Disposition-Notification (MDN) típusú érték. (Választható lehetőség) |
| fileName | Sztring | Az AS2-üzenet fejlécének a fájl neve. (Választható lehetőség) |
| isMessageFailed |Logikai | Hogy az AS2-üzenet nem sikerült. (Kötelező) |
| isMessageSigned | Logikai | Hogy az AS2-üzenet alá van írva. (Kötelező) |
| isMessageEncrypted | Logikai | Hogy az AS2-üzenet titkosítva van. (Kötelező) |
| isMessageCompressed |Logikai | Hogy az AS2-üzenet tömörítve volt. (Kötelező) |
| correlationMessageId | Sztring | AS2-Üzenetazonosító, üzenetek korrelációját, ha a visszaigazolással. (Választható lehetőség) |
| incomingHeaders |Tartalmazó JToken | Bejövő AS2 üzenet részletei. (Választható lehetőség) |
| outgoingHeaders |Tartalmazó JToken | Kimenő AS2-üzenet fejléc részleteit. (Választható lehetőség) |
| isNrrEnabled | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| isMdnExpected | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| mdnType | Enum | Engedélyezett értékek a következők **NotConfigured**, **szinkronizálási**, és **aszinkron**. (Kötelező) |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN követési séma

```json
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
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | AS2 üzenetet küldő partner nevének. (Választható lehetőség) |
| receiverPartnerName | Sztring | AS2 üzenetet fogadó partner nevének. (Választható lehetőség) |
| as2To | Sztring | Az AS2-üzenetek fogadó partner nevének. (Kötelező) |
| as2From | Sztring | Az AS2-üzenetek küldő partner nevének. (Kötelező) |
| agreementname tulajdonság | Sztring | Az AS2-megállapodás, amelyhez az üzenetek elhárulnak neve. (Választható lehetőség) |
| irány |Sztring | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| messageId | Sztring | AS2-üzenet azonosítója. (Választható lehetőség) |
| originalMessageId |Sztring | AS2 eredeti üzenet azonosítója. (Választható lehetőség) |
| dispositionType | Sztring | MDN típusú érték. (Választható lehetőség) |
| isMessageFailed |Logikai | Hogy az AS2-üzenet nem sikerült. (Kötelező) |
| isMessageSigned |Logikai | Hogy az AS2-üzenet alá van írva. (Kötelező) |
| isNrrEnabled | Logikai | Használja az alapértelmezett értéket, ha az érték nem ismert. (Kötelező) |
| állapotkód | Enum | Engedélyezett értékek a következők **elfogadva**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| micVerificationStatus | Enum | Engedélyezett értékek a következők **NotApplicable**, **sikeres**, és **sikertelen**. (Kötelező) |
| correlationMessageId | Sztring | Korrelációs azonosító. Az eredeti messaged azonosítója (az üzenet azonosítója az üzenet MDN van konfigurálva). (Választható lehetőség) |
| incomingHeaders | Tartalmazó JToken | Azt jelzi, hogy a bejövő üzenet részletei. (Választható lehetőség) |
| outgoingHeaders |Tartalmazó JToken | Azt jelzi, hogy a kimenő üzenet részletei. (Választható lehetőség) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokoll követési sémák

B2B-protokoll követési sémák kapcsolatos információkért lásd:

* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B egyéni követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* Ismerje meg [B2B-üzenetek figyelése](logic-apps-monitor-b2b-message.md)
* Ismerje meg [a Log Analytics B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)