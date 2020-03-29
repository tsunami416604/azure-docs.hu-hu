---
title: AS2 követési sémák B2B üzenetekhez
description: Követési sémák létrehozása az AS2-üzenetek figyeléséhez az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906971"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Sémák létrehozása AZ AS2-üzenetek nyomon követéséhez az Azure Logic Apps alkalmazásban

A vállalkozások közötti (B2B) tranzakciók sikeres, hibáiés üzenettulajdonságainak figyelése érdekében használhatja ezeket az AS2-követési sémákat az integrációs fiókban:

* AS2 üzenetkövetési séma
* AS2 üzenetintézkedési értesítés (MDN) követési séma

## <a name="as2-message-tracking-schema"></a>AS2 üzenetkövetési séma

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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | AS2 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | AZ AS2 üzenetfogadó partnerének neve |
| as2To | Igen | Sztring | AS2 üzenetfogadó neve az AS2 üzenet fejlécéből |
| as2From | Igen | Sztring | AS2 üzenet küldőjének neve az AS2 üzenet fejlécéből |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az AS2-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Sztring | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| messageId | Nem | Sztring | AS2 üzenetazonosító az AS2 üzenet fejlécéből |
| dispositionType | Nem | Sztring | Üzenetintézkedési értesítés (MDN) intézkedési típusértéke |
| fileName | Nem | Sztring | Fájlnév az AS2 üzenet fejlécéből |
| isMessageFailed | Igen | Logikai | Azt jelzi, hogy az AS2-üzenet nem sikerült-e |
| isMessageSigned | Igen | Logikai | Az AS2 üzenet aláírása |
| isMessageEncrypted | Igen | Logikai | Azt jelzi, hogy az AS2 üzenet titkosítva volt-e |
| isMessageCompressed | Igen | Logikai | Az AS2 üzenet tömörítése |
| correlationMessageId | Nem | Sztring | AS2 üzenetazonosító, üzenetek és MDN-ek korrelációja |
| bejövőfejlécek | Nem | JToken szótár | Bejövő AS2 üzenetfejléc részletei |
| kimenőfejlécek | Nem | JToken szótár | Kimenő AS2 üzenetfejléc részletei |
| isNrrEnabled | Igen | Logikai | Az alapértelmezett érték használata, ha az érték nem ismert |
| isMdnVárt | Igen | Logikai | Az alapértelmezett érték használata, ha az érték nem ismert |
| mdnType típus | Igen | Felsorolás | Megengedett `NotConfigured`értékek: `Sync`, , és`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN követési séma

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

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | AS2 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | AZ AS2 üzenetfogadó partnerének neve |
| as2To | Igen | Sztring | Az AS2 üzenetet fogadó partnernév |
| as2From | Igen | Sztring | Az AS2 üzenetet küldő partnernév |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az AS2-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Sztring | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| messageId | Nem | Sztring | AS2 üzenetazonosító |
| eredeti MessageId | Nem | Sztring | AS2 eredeti üzenetazonosító |
| dispositionType | Nem | Sztring | MDN intézkedéstípus-érték |
| isMessageFailed | Igen | Logikai | Azt jelzi, hogy az AS2-üzenet nem sikerült-e |
| isMessageSigned | Igen | Logikai | Az AS2 üzenet aláírása |
| isNrrEnabled | Igen | Logikai | Az alapértelmezett érték használata, ha az érték nem ismert |
| statusCode | Igen | Felsorolás | Megengedett `Accepted`értékek: `Rejected`, , és`AcceptedWithErrors` |
| micVerificationStatus | Igen | Felsorolás | Megengedett`NotApplicable`értékek: `Succeeded`, , és`Failed` |
| correlationMessageId | Nem | Sztring | Korrelációs azonosító, amely az MDN-vel konfigurált eredeti üzenet azonosítója |
| bejövőfejlécek | Nem | JToken szótár | Bejövő üzenetfejléc részletei |
| kimenőfejlécek | Nem | JToken szótár | Kimenő üzenet fejlécének részletei |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokollkövetési sémák

A B2B protokollkövetési sémákról a következő témakörökben talál további információt:

* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B egyéni követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)