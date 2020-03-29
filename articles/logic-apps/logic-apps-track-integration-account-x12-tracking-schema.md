---
title: X12 követési sémák B2B üzenetekhez
description: Követési sémák létrehozása az Azure Logic Apps X12-es üzenetek figyeléséhez
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905306"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Sémák létrehozása x12-es üzenetek követéséhez az Azure Logic Apps alkalmazásban

A vállalkozások közötti (B2B) tranzakciók sikeres, hibáiés üzenettulajdonságainak figyelése érdekében az integrációs fiókban használhatja az X12-követési sémákat:

* X12 tranzakciókészlet-követési séma
* X12 tranzakciókészlet-visszaigazolás-követési séma
* X12-es bankközi követési séma
* X12-es átcserélő nyugta-követési séma
* X12 funkcionális csoportkövetési séma
* X12 funkcionális csoportnyugta követési séma

## <a name="x12-transaction-set-tracking-schema"></a>X12 tranzakciókészlet-követési séma

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | Az X12-üzenetfogadó partnerének neve |
| senderQualifier | Igen | Sztring | Partnerminősítő küldése |
| senderIdentifier | Igen | Sztring | Partnerazonosító küldése |
| receiverQualifier | Igen | Sztring | Partner-minősítő fogadása |
| receiverIdentifier | Igen | Sztring | Partnerazonosító fogadása |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az X12-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Felsorolás | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| interchangeControlNumber | Nem | Sztring | Bankközi vezérlés száma |
| functionalGroupControlNumber | Nem | Sztring | Funkcionális vezérlőszám |
| transactionSetControlNumber | Nem | Sztring | Tranzakciókészlet vezérlőszáma |
| CorrelationMessageId | Nem | Sztring | Korrelációs üzenet azonosítója, amely a(z) {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} kombinációja |
| messageType (üzenettípusa) | Nem | Sztring | Tranzakciókészlet vagy bizonylattípus |
| isMessageFailed | Igen | Logikai | Az X12 üzenet sikertelen volt-e |
| isTechnicalNyugtmentExpected | Igen | Logikai | Azt jelzi, hogy a technikai nyugtázás be van-e állítva az X12-megállapodásban |
| isFunctionalAcknowledgmentExpected | Igen | Logikai | Azt jelzi, hogy a funkcionális nyugtázás be van-e állítva az X12-megállapodásban |
| needAk2LoopForValidMessages | Igen | Logikai | Azt jelzi, hogy az AK2 hurok szükséges-e egy érvényes üzenethez |
| szegmensekszáma | Nem | Egész szám | Az X12 tranzakciókészlet szegmenseinek száma |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 tranzakciókészlet-visszaigazolás-követési séma

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

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | Az X12-üzenetfogadó partnerének neve |
| senderQualifier | Igen | Sztring | Partnerminősítő küldése |
| senderIdentifier | Igen | Sztring | Partnerazonosító küldése |
| receiverQualifier | Igen | Sztring | Partner-minősítő fogadása |
| receiverIdentifier | Igen | Sztring | Partnerazonosító fogadása |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az X12-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Felsorolás | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| interchangeControlNumber | Nem | Sztring | A funkcionális nyugtázás bankközi vezérlőszáma. Az érték csak arra a küldési oldalra történik, ahol funkcionális nyugtázás érkezik a partnernek küldött üzenetekhez. |
| functionalGroupControlNumber | Nem | Sztring | A funkcionális nyugtázás funkcionális csoportvezérlő száma. Az érték csak arra a küldési oldalra történik, ahol funkcionális nyugtázás érkezik a partnernek küldött üzenetekhez. |
| isaSegment | Nem | Sztring | az üzenet ISA szegmensét. Az érték csak arra a küldési oldalra történik, ahol funkcionális nyugtázás érkezik a partnernek küldött üzenetekhez. |
| gsSegment | Nem | Sztring | az üzenet GS-szegmensében. Az érték csak arra a küldési oldalra történik, ahol funkcionális nyugtázás érkezik a partnernek küldött üzenetekhez. |
| respondingfunctionalGroupControlNumber | Nem | Sztring | A válaszoló bankközi vezérlő száma |
| respondingFunctionalGroupId | Nem | Sztring | A válaszoló funkcionális csoport azonosítója, amely leképezi az AK101-et a nyugtázásban |
| respondingtransactionSetControlNumber | Nem | Sztring | A válaszoló tranzakciókészlet vezérlőszáma |
| válaszoló TransactionSetId | Nem | Sztring | A válaszoló tranzakciókészlet azonosítója, amely leképezi az AK201-et a nyugtázásban |
| statusCode | Igen | Logikai | Tranzakciókészlet nyugtázási állapotkódja |
| szegmensekszáma | Igen | Felsorolás | Nyugtázási állapotkód az alábbi `Accepted` `Rejected`megengedett értékekkel: , , és`AcceptedWithErrors` |
| processingStatus | Igen | Felsorolás | Az elismervény feldolgozási állapota az `Received`alábbi `Generated`megengedett értékekkel: , , és`Sent` |
| CorrelationMessageId | Nem | Sztring | Korrelációs üzenet azonosítója, amely a(z) {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} kombinációja |
| isMessageFailed | Igen | Logikai | Az X12 üzenet sikertelen volt-e |
| ak2Szegmens | Nem | Sztring | A fogadott funkcionális csoporton belüli tranzakciókészlet nyugtázása |
| ak3Szegmens | Nem | Sztring | Hibák jelentése egy adatszegmensben |
| ak5Szegmens | Nem | Sztring | Arról számol be, hogy az AK2 szegmensben azonosított tranzakciókészletet elfogadják vagy elutasítják- e, és hogy miért |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12-es bankközi követési séma

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

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | Az X12-üzenetfogadó partnerének neve |
| senderQualifier | Igen | Sztring | Partnerminősítő küldése |
| senderIdentifier | Igen | Sztring | Partnerazonosító küldése |
| receiverQualifier | Igen | Sztring | Partner-minősítő fogadása |
| receiverIdentifier | Igen | Sztring | Partnerazonosító fogadása |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az X12-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Felsorolás | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| interchangeControlNumber | Nem | Sztring | Bankközi vezérlés száma |
| isaSegment | Nem | Sztring | Üzenet ISA szegmense |
| isTechnicalNyugtmentExpected | Logikai | Azt jelzi, hogy a technikai nyugtázás be van-e állítva az X12-megállapodásban  |
| isMessageFailed | Igen | Logikai | Az X12 üzenet sikertelen volt-e |
| isa09 | Nem | Sztring | X12 dokumentumcsere dátuma |
| isa10 | Nem | Sztring | X12 dokumentumcsere ideje |
| isa11 | Nem | Sztring | X12 bankközi ellenőrzési szabványazonosító |
| isa12 | Nem | Sztring | X12 csomópont-ellenőrzési verziószáma |
| isa14 | Nem | Sztring | X12 nyugtázást kérnek |
| isa15 | Nem | Sztring | A vizsgálat vagy a gyártás mutatója |
| isa16 | Nem | Sztring | Elemelválasztó |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12-es átcserélő nyugta-követési séma

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

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | Az X12-üzenetfogadó partnerének neve |
| senderQualifier | Igen | Sztring | Partnerminősítő küldése |
| senderIdentifier | Igen | Sztring | Partnerazonosító küldése |
| receiverQualifier | Igen | Sztring | Partner-minősítő fogadása |
| receiverIdentifier | Igen | Sztring | Partnerazonosító fogadása |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az X12-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Felsorolás | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| interchangeControlNumber | Nem | Sztring | A partnerektől kapott technikai nyugták bankközi ellenőrző száma |
| isaSegment | Nem | Sztring | ISA szegmens a partnerektől kapott technikai nyugtázáshoz |
| respondingInterchangeControlNumber | Nem | Sztring | A partnerektől kapott technikai nyugtázás bankközi vezérlőszáma |
| isMessageFailed | Igen | Logikai | Az X12 üzenet sikertelen volt-e |
| statusCode | Igen | Felsorolás | Banknyugta állapotkód váltása az `Accepted`alábbi `Rejected`megengedett értékekkel: , , és`AcceptedWithErrors` |
| processingStatus | Igen | Felsorolás | Nyugtázási állapot az alábbi `Received` `Generated`megengedett értékekkel: , , és`Sent` |
| ta102 | Nem | Sztring | Váltás dátuma |
| ta103 között | Nem | Sztring | Váltási idő |
| ta105 | Nem | Sztring | Bankjegykód |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funkcionális csoportkövetési séma

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

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | Az X12-üzenetfogadó partnerének neve |
| senderQualifier | Igen | Sztring | Partnerminősítő küldése |
| senderIdentifier | Igen | Sztring | Partnerazonosító küldése |
| receiverQualifier | Igen | Sztring | Partner-minősítő fogadása |
| receiverIdentifier | Igen | Sztring | Partnerazonosító fogadása |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az X12-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Felsorolás | Az üzenetáramlás iránya, fogadás vagy küldés |
| interchangeControlNumber | Nem | Sztring | Bankközi vezérlés száma |
| functionalGroupControlNumber | Nem | Sztring | Funkcionális vezérlőszám |
| gsSegment | Nem | Sztring | Üzenet GS szegmens |
| isTechnicalNyugtmentExpected | Igen | Logikai | Azt jelzi, hogy a technikai nyugtázás be van-e állítva az X12-megállapodásban |
| isFunctionalAcknowledgmentExpected | Igen | Logikai | Azt jelzi, hogy a funkcionális nyugtázás be van-e állítva az X12-megállapodásban |
| isMessageFailed | Igen | Logikai | Az X12 üzenet sikertelen volt-e |
| gs01 | Nem | Sztring | Funkcionális azonosító kódja |
| gs02 | Nem | Sztring | Alkalmazás feladójának kódja |
| gs03 | Nem | Sztring | Alkalmazásfogadó kódja |
| gs04 | Nem | Sztring | Funkcionális csoport dátuma |
| gs05 | Nem | Sztring | Funkcionális csoportidő |
| gs07 | Nem | Sztring | Felelős ügynökségi kód |
| gs08 | Nem | Sztring | A verzió, kiadás vagy iparág azonosítókódja |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 funkcionális csoportnyugta követési séma

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

| Tulajdonság | Kötelező | Típus | Leírás |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12 üzenet feladójának partnerneve |
| vevőPartnerName | Nem | Sztring | Az X12-üzenetfogadó partnerének neve |
| senderQualifier | Igen | Sztring | Partnerminősítő küldése |
| senderIdentifier | Igen | Sztring | Partnerazonosító küldése |
| receiverQualifier | Igen | Sztring | Partner-minősítő fogadása |
| receiverIdentifier | Igen | Sztring | Partnerazonosító fogadása |
| agreementName (megállapodásneve) | Nem | Sztring | Annak az X12-megállapodásnak a neve, amelyre az üzeneteket feloldották |
| irány | Igen | Felsorolás | Az üzenetáramlás iránya, `receive` amely vagy`send` |
| interchangeControlNumber | Nem | Sztring | Bankközi vezérlőszám, amely feltölti a küldési oldalt, amikor technikai nyugtázás érkezik a partnerektől |
| functionalGroupControlNumber | Nem | Sztring | A technikai nyugtázás funkcionális csoportvezérlő száma, amely akkor érkezik a küldési oldalra, amikor a partnerektől technikai nyugtázás érkezik |
| isaSegment | Nem | Sztring | Megegyezik a bankközi ellenőrzés számával, de csak bizonyos esetekben van feltöltve |
| gsSegment | Nem | Sztring | Megegyezik a funkcionális csoport vezérlőszámával, de csak bizonyos esetekben van feltöltve |
| respondingfunctionalGroupControlNumber | Nem | Sztring | Az eredeti funkcionális csoport vezérlőszáma |
| respondingFunctionalGroupId | Nem | Sztring | Leképezések az AK101-hez a nyugtázási funkcionális csoport azonosítójában |
| isMessageFailed | Logikai | Az X12 üzenet sikertelen volt-e |
| statusCode | Igen | Felsorolás | Nyugtázási állapotkód az alábbi `Accepted` `Rejected`megengedett értékekkel: , , és`AcceptedWithErrors` |
| processingStatus | Igen | Felsorolás | Az elismervény feldolgozási állapota az `Received`alábbi `Generated`megengedett értékekkel: , , és`Sent` |
| ak903 | Nem | Sztring | Fogadott tranzakciókészletek száma |
| ak904 | Nem | Sztring | Az azonosított funkcionális csoportban elfogadott tranzakciókészletek száma |
| ak9Szegmens | Nem | Sztring | Elfogadják vagy elutasítják-e az AK1 szegmensben azonosított funkcionális csoportot, és miért |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokollkövetési sémák

A B2B protokollkövetési sémákról a következő témakörökben talál további információt:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B egyéni követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)