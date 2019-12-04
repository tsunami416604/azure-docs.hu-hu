---
title: AS2-követési sémák B2B-üzenetekhez
description: Olyan AS2 nyomkövető sémákat hozhat létre, amelyek a Azure Logic Apps integrációs fiókjaiban található B2B-üzeneteket figyelik Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792812"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Sémák létrehozása az AS2-üzenetek és a MDNs követéséhez Azure Logic Apps integrációs fiókjaiban

Ha segítségre van szüksége a vállalatok közötti (B2B) tranzakciók sikerességének, hibáinak és üzenetének figyeléséhez, használhatja az alábbi AS2-követési sémákat az integrációs fiókjában:

* AS2-üzenet követési sémája
* AS2 MDN-követési séma

## <a name="as2-message-tracking-schema"></a>AS2-üzenet követési sémája

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | AS2-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | AS2-üzenet fogadójának partnere neve. Választható |
| as2To | Sztring | Az AS2-üzenet fogadójának neve az AS2-üzenet fejlécében. Kötelező |
| as2From | Sztring | AS2-üzenet feladójának neve az AS2-üzenet fejlécében. Kötelező |
| agreementName | Sztring | Azon AS2-egyezmény neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Sztring | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| messageId | Sztring | AS2-üzenet azonosítója az AS2-üzenet fejlécében (nem kötelező) |
| dispositionType |Sztring | Az üzenet-törlési értesítés (MDN) megírási típusának értéke. Választható |
| fileName | Sztring | A fájl neve az AS2-üzenet fejlécében. Választható |
| isMessageFailed |Logikai | Az AS2-üzenet sikertelen volt-e. Kötelező |
| isMessageSigned | Logikai | Azt jelzi, hogy az AS2-üzenet aláírt-e. Kötelező |
| isMessageEncrypted | Logikai | Azt jelzi, hogy az AS2-üzenet titkosított volt-e. Kötelező |
| isMessageCompressed |Logikai | Azt jelzi, hogy az AS2-üzenet tömörített-e. Kötelező |
| CorrelationMessageId | Sztring | AS2-üzenet azonosítója az üzenetek MDNs-vel való összekapcsolásához. Választható |
| incomingHeaders |JToken szótár | Bejövő AS2-üzenet fejlécének részletei. Választható |
| outgoingHeaders |JToken szótár | Kimenő AS2-üzenet fejlécének részletei. Választható |
| isNrrEnabled | Logikai | Ha az érték nem ismert, használja az alapértelmezett értéket. Kötelező |
| isMdnExpected | Logikai | Ha az érték nem ismert, használja az alapértelmezett értéket. Kötelező |
| mdnType | Felsorolás | Az engedélyezett értékek a következők: **NotConfigured**, **Sync**és **aszinkron**. Kötelező |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN-követési séma

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | AS2-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | AS2-üzenet fogadójának partnere neve. Választható |
| as2To | Sztring | Az AS2-üzenetet fogadó partner neve. Kötelező |
| as2From | Sztring | Az AS2-üzenetet küldő partner neve. Kötelező |
| agreementName | Sztring | Azon AS2-egyezmény neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány |Sztring | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| messageId | Sztring | AS2-üzenet azonosítója. Választható |
| originalMessageId |Sztring | AS2 eredeti üzenet azonosítója. Választható |
| dispositionType | Sztring | MDN-törlési típus értéke Választható |
| isMessageFailed |Logikai | Az AS2-üzenet sikertelen volt-e. Kötelező |
| isMessageSigned |Logikai | Azt jelzi, hogy az AS2-üzenet aláírt-e. Kötelező |
| isNrrEnabled | Logikai | Ha az érték nem ismert, használja az alapértelmezett értéket. Kötelező |
| StatusCode | Felsorolás | Az engedélyezett értékek **elfogadása**, **elutasítása**és **AcceptedWithErrors**. Kötelező |
| micVerificationStatus | Felsorolás | Az engedélyezett értékek: **NotApplicable**, **sikeres**és **sikertelen**. Kötelező |
| CorrelationMessageId | Sztring | Korrelációs azonosító. Az eredeti üzenetbe állított azonosító (az üzenet azonosítója, amelyhez az MDN konfigurálva van). Választható |
| incomingHeaders | JToken szótár | Megadja a bejövő üzenetek fejlécének részleteit. Választható |
| outgoingHeaders |JToken szótár | Megadja a kimenő üzenetek fejlécének részleteit. Választható |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni B2B-követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [B2B-üzenetek monitorozásáról](logic-apps-monitor-b2b-message.md)
* Tudnivalók a [B2B-üzenetek nyomon követéséről Azure monitor naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)