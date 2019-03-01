---
title: A B2B-üzeneteken alapuló Azure Logic Apps - sémák nyomon követése X12 |} A Microsoft Docs
description: Hozzon létre X12, hogy az integrációs fiókok B2B-üzenetek monitorozása az Azure Logic Apps Enterprise Integration Pack-sémák nyomon követése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195183"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Sémák nyomon követési X12 létrehozása az Azure Logic Apps integrációs fiókjainak üzenetek

Segítséget nyújtanak a figyelő sikeres, hibák és -vállalatközi (B2B) tranzakciókhoz üzenettulajdonságok használhatja ezeket az integrációs fiókjában sémák nyomon követése X12:

* X12 tranzakciókészlet követési séma
* X12 tranzakciókészlet nyugtázása követési séma
* X12 adatcsere követési séma
* X12 adatcsere nyugtázása követési séma
* X12 funkcionális csoport követési séma
* X12 funkcionális csoport nyugtázása követési séma

## <a name="x12-transaction-set-tracking-schema"></a>X12 tranzakciókészlet követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | String | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | String | X12 üzenetet fogadó partner nevének. (Választható lehetőség) |
| senderQualifier | String | Partner minősítő küldése. (Kötelező) |
| senderIdentifier | String | Küldeni a partner azonosítója. (Kötelező) |
| receiverQualifier | String | Partner minősítő kapnak. (Kötelező) |
| receiverIdentifier | String | Partner azonosítót kap. (Kötelező) |
| agreementName | String | A X12 nevét, amelyhez az üzenetek elhárulnak szerződés. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | String | Adatcsere ellenőrzőszáma. (Választható lehetőség) |
| functionalGroupControlNumber | String | Funkcionális ellenőrző szám. (Választható lehetőség) |
| transactionSetControlNumber | String | A tranzakciókészlet ellenőrzőszáma. (Választható lehetőség) |
| CorrelationMessageId | String | Korrelációs állapotüzenet-azonosító. {Agreementname tulajdonság} kombinációját {*GroupControlNumber*} {TransactionSetControlNumber}. (Választható lehetőség) |
| messageType | String | Tranzakció állítsa be, vagy a dokumentum típusa. (Választható lehetőség) |
| isMessageFailed | Logikai | -E a X12 üzenetet nem sikerült. (Kötelező) |
| isTechnicalAcknowledgmentExpected | Logikai | A technikai nyugtázása a X12 beállított e szerződés. (Kötelező) |
| isFunctionalAcknowledgmentExpected | Logikai | A működési nyugtázása a X12 beállított e szerződés. (Kötelező) |
| needAk2LoopForValidMessages | Logikai | E a: AK2 hurok kötelező megadni egy érvényes üzenetet. (Kötelező) |
| segmentsCount | Egész szám | A X12 a szegmensek száma tranzakciókészlet. (Választható lehetőség) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 tranzakciókészlet nyugtázása követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | String | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | String | X12 üzenetet fogadó partner nevének. (Választható lehetőség) |
| senderQualifier | String | Partner minősítő küldése. (Kötelező) |
| senderIdentifier | String | Küldeni a partner azonosítója. (Kötelező) |
| receiverQualifier | String | Partner minősítő kapnak. (Kötelező) |
| receiverIdentifier | String | Partner azonosítót kap. (Kötelező) |
| agreementName | String | A X12 nevét, amelyhez az üzenetek elhárulnak szerződés. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | String | Adatcsere ellenőrzőszáma, a funkcionális nyugtázással leállt. Érték tölti fel a küldési oldalon, ahol érkezik, a funkcionális nyugtázási küldött üzenetek csak a partnernek. (Választható lehetőség) |
| functionalGroupControlNumber | String | Funkcionális csoport-ellenőrzőszám az működési átvételi. Érték tölti fel a küldési oldalon, ahol érkezik, a funkcionális nyugtázási küldött üzenetek csak a partnernek. (Választható lehetőség) |
| isaSegment | String | Az ISA-szegmens az üzenet. Érték tölti fel a küldési oldalon, ahol érkezik, a funkcionális nyugtázási küldött üzenetek csak a partnernek. (Választható lehetőség) |
| gsSegment | String | A GS-szegmens az üzenet. Érték tölti fel a küldési oldalon, ahol érkezik, a funkcionális nyugtázási küldött üzenetek csak a partnernek. (Választható lehetőség) |
| respondingfunctionalGroupControlNumber | String | Adatcsere ellenőrzőszáma válaszol. (Választható lehetőség) |
| respondingFunctionalGroupId | String | Funkcionális csoport azonosítója, amely AK101 a visszaigazolás válaszol. (Választható lehetőség) |
| respondingtransactionSetControlNumber | String | Tranzakciókészlet ellenőrzőszáma válaszol-e. (Választható lehetőség) |
| respondingTransactionSetId | String | Válaszol tranzakciókészlet-Azonosítót, amelynek a visszaigazolás AK201 rendeli. (Választható lehetőség) |
| statusCode | Logikai | A tranzakciókészlet a nyugta állapotkódjának. (Kötelező) |
| segmentsCount | Enum | A nyugta állapotkódjának. Engedélyezett értékek a következők **elfogadva**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | A nyugtázási feldolgozási állapotát. Engedélyezett értékek a következők **fogadott**, **Generated**, és **elküldött**. (Kötelező) |
| CorrelationMessageId | String | Korrelációs állapotüzenet-azonosító. {Agreementname tulajdonság} kombinációját {*GroupControlNumber*} {TransactionSetControlNumber}. (Választható lehetőség) |
| isMessageFailed | Logikai | -E a X12 üzenetet nem sikerült. (Kötelező) |
| ak2Segment | String | Egy tranzakció, állítsa be a kapott funkcionális csoport nyugtázása. (Választható lehetőség) |
| ak3Segment | String | Az adatok szegmens hibát jelez. (Választható lehetőség) |
| ak5Segment | String | Jelentés-e a szegmensben: AK2 azonosított tranzakciókészlet van elfogad vagy elutasít, és hogy miért. (Választható lehetőség) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 adatcsere követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | String | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | String | X12 üzenetet fogadó partner nevének. (Választható lehetőség) |
| senderQualifier | String | Partner minősítő küldése. (Kötelező) |
| senderIdentifier | String | Küldeni a partner azonosítója. (Kötelező) |
| receiverQualifier | String | Partner minősítő kapnak. (Kötelező) |
| receiverIdentifier | String | Partner azonosítót kap. (Kötelező) |
| agreementName | String | A X12 nevét, amelyhez az üzenetek elhárulnak szerződés. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | String | Adatcsere ellenőrzőszáma. (Választható lehetőség) |
| isaSegment | String | Az ISA-szegmens üzenet. (Választható lehetőség) |
| isTechnicalAcknowledgmentExpected | Logikai | A technikai nyugtázása a X12 beállított e szerződés. (Kötelező) |
| isMessageFailed | Logikai | -E a X12 üzenetet nem sikerült. (Kötelező) |
| isa09 | String | A dokumentum adatcsere X12 dátum. (Választható lehetőség) |
| isa10 | String | A dokumentum adatcsere idő X12. (Választható lehetőség) |
| isa11 | String | X12 adatcsere vezérlési szabványok azonosítója. (Választható lehetőség) |
| isa12 | String | X12 adatcsere-ellenőrző verziószám. (Választható lehetőség) |
| isa14 | String | X12 nyugtázási van szükség. (Választható lehetőség) |
| isa15 | String | A tesztelési és éles mutató. (Választható lehetőség) |
| isa16 | String | Adatelem-elválasztó. (Választható lehetőség) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 adatcsere nyugtázása követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | String | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | String | X12 üzenetet fogadó partner nevének. (Választható lehetőség) |
| senderQualifier | String | Partner minősítő küldése. (Kötelező) |
| senderIdentifier | String | Küldeni a partner azonosítója. (Kötelező) |
| receiverQualifier | String | Partner minősítő kapnak. (Kötelező) |
| receiverIdentifier | String | Partner azonosítót kap. (Kötelező) |
| agreementName | String | A X12 nevét, amelyhez az üzenetek elhárulnak szerződés. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | String | Adatcsere ellenőrzőszáma az műszaki átvételi partnerek érkezett. (Választható lehetőség) |
| isaSegment | String | Az ISA-szegmens a műszaki nyugtázási partnerek érkezett. (Választható lehetőség) |
| respondingInterchangeControlNumber |String | Adatcsere ellenőrzőszáma a partnertől kapott a műszaki nyugtázás. (Választható lehetőség) |
| isMessageFailed | Logikai | -E a X12 üzenetet nem sikerült. (Kötelező) |
| statusCode | Enum | A nyugta állapotkódjának adatcsere. Engedélyezett értékek a következők **elfogadva**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | Nyugtázási állapot. Engedélyezett értékek a következők **fogadott**, **Generated**, és **elküldött**. (Kötelező) |
| ta102 | String | Adatcsere dátum. (Választható lehetőség) |
| ta103 | String | Adatcsere idő. (Választható lehetőség) |
| ta105 | String | Adatcsere Megjegyzés kódot. (Választható lehetőség) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funkcionális csoport követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | String | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | String | X12 üzenetet fogadó partner nevének. (Választható lehetőség) |
| senderQualifier | String | Partner minősítő küldése. (Kötelező) |
| senderIdentifier | String | Küldeni a partner azonosítója. (Kötelező) |
| receiverQualifier | String | Partner minősítő kapnak. (Kötelező) |
| receiverIdentifier | String | Partner azonosítót kap. (Kötelező) |
| agreementName | String | A X12 nevét, amelyhez az üzenetek elhárulnak szerződés. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | String | Adatcsere ellenőrzőszáma. (Választható lehetőség) |
| functionalGroupControlNumber | String | Funkcionális ellenőrző szám. (Választható lehetőség) |
| gsSegment | String | Üzenet GS szegmens. (Választható lehetőség) |
| isTechnicalAcknowledgmentExpected | Logikai | A technikai nyugtázása a X12 beállított e szerződés. (Kötelező) |
| isFunctionalAcknowledgmentExpected | Logikai | A működési nyugtázása a X12 beállított e szerződés. (Kötelező) |
| isMessageFailed | Logikai | -E a X12 üzenetet nem sikerült. (Kötelező)|
| gs01 | String | Funkcionális azonosító kód. (Választható lehetőség) |
| gs02 | String | Alkalmazás küldő kód. (Választható lehetőség) |
| gs03 | String | Alkalmazás fogadó kódot. (Választható lehetőség) |
| gs04 | String | Funkcionális csoport dátuma. (Választható lehetőség) |
| gs05 | String | Funkcionális csoport ideje. (Választható lehetőség) |
| gs07 | String | Felelős ügynöksége-kód. (Választható lehetőség) |
| gs08 | String | Verzió és kiadás/iparági kód. (Választható lehetőség) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funkcionális csoport nyugtázása követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | String | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | String | X12 üzenetet fogadó partner nevének. (Választható lehetőség) |
| senderQualifier | String | Partner minősítő küldése. (Kötelező) |
| senderIdentifier | String | Küldeni a partner azonosítója. (Kötelező) |
| receiverQualifier | String | Partner minősítő kapnak. (Kötelező) |
| receiverIdentifier | String | Partner azonosítót kap. (Kötelező) |
| agreementName | String | A X12 nevét, amelyhez az üzenetek elhárulnak szerződés. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | String | Adatcsere ellenőrzőszáma, amely feltölti a küldési oldalon műszaki nyugtázási partnerek fogadásakor. (Választható lehetőség) |
| functionalGroupControlNumber | String | Funkcionális csoport ellenőrzőszám az műszaki átvételi, amely feltölti a küldési oldalon műszaki nyugtázási partnerek fogadásakor. (Választható lehetőség) |
| isaSegment | String | Ugyanaz, mint a adatcsere ellenőrzés száma, de csak bizonyos esetekben ki van töltve. (Választható lehetőség) |
| gsSegment | String | Ugyanaz, mint a funkcionális csoport szabályozhatja a száma, de csak bizonyos esetekben ki van töltve. (Választható lehetőség) |
| respondingfunctionalGroupControlNumber | String | Az eredeti funkcionális csoport-ellenőrzőszám. (Választható lehetőség) |
| respondingFunctionalGroupId | String | Funkcionális csoport nyugtázása AK101 a Maps-azonosító. (Választható lehetőség) |
| isMessageFailed | Logikai | -E a X12 üzenetet nem sikerült. (Kötelező) |
| statusCode | Enum | A nyugta állapotkódjának. Engedélyezett értékek a következők **elfogadva**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | A nyugtázási feldolgozási állapotát. Engedélyezett értékek a következők **fogadott**, **Generated**, és **elküldött**. (Kötelező) |
| ak903 | String | Fogadott tranzakciókészletek száma. (Választható lehetőség) |
| ak904 | String | Az azonosított funkcionális csoport elfogadott tranzakciókészletek száma. (Választható lehetőség) |
| ak9Segment | String | A funkcionális csoporttal, a AK1 szegmensben van elfogad vagy elutasít-e, és hogy miért. (Választható lehetőség) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokoll követési sémák

B2B-protokoll követési sémák kapcsolatos információkért lásd:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [B2B egyéni követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [B2B-üzenetek figyelési](logic-apps-monitor-b2b-message.md).
* Ismerje meg [az Azure Monitor naplóira B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
