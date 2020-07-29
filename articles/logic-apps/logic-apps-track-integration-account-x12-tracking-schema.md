---
title: VÁLLALATKÖZI üzenetek X12 követési sémái
description: Nyomkövetési sémák létrehozása a X12-üzenetek figyeléséhez Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905306"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Sémák létrehozása a X12-üzenetek követéséhez Azure Logic Apps

Ha segítségre van szüksége a vállalatok közötti (B2B) tranzakciók sikerességének, hibáinak és üzenetének figyeléséhez, a következő X12-követési sémákat használhatja az integrációs fiókjában:

* X12-tranzakció készletének követési sémája
* X12 tranzakciós készletének visszaigazolási követési sémája
* X12-csere követési sémája
* X12 adatcsere-visszaigazolási követési sémája
* X12 funkcionális csoport követési sémája
* X12 funkcionális csoport nyugtázási követési sémája

## <a name="x12-transaction-set-tracking-schema"></a>X12-tranzakció készletének követési sémája

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

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | No | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Yes | Sztring | Partner minősítő küldése |
| senderIdentifier | Yes | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Yes | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Yes | Sztring | Fogadási partner azonosítója |
| agreementName | No | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy`send` |
| interchangeControlNumber | No | Sztring | Adatcsere-vezérlési szám |
| functionalGroupControlNumber | No | Sztring | Funkcionális vezérlési szám |
| transactionSetControlNumber | No | Sztring | Tranzakciónapló-vezérlő száma |
| CorrelationMessageId | No | Sztring | Korrelációs üzenet azonosítója ({AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}) kombinációja |
| messageType | No | Sztring | Tranzakciótípus vagy bizonylattípus |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| isTechnicalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben |
| isFunctionalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a funkcionális nyugtázás konfigurálva van-e az X12-szerződésben |
| needAk2LoopForValidMessages | Yes | Logikai | Azt jelzi, hogy az AK2 hurok szükséges-e egy érvényes üzenethez |
| segmentsCount | No | Egész szám | A X12-tranzakció készletében lévő szegmensek száma |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 tranzakciós készletének visszaigazolási követési sémája

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

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | No | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Yes | Sztring | Partner minősítő küldése |
| senderIdentifier | Yes | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Yes | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Yes | Sztring | Fogadási partner azonosítója |
| agreementName | No | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy`send` |
| interchangeControlNumber | No | Sztring | A funkcionális visszaigazoláshoz tartozó adatcsere-vezérlési szám. Az érték csak arra a küldési oldalra töltődik fel, ahol a partnernek küldött üzenetekhez a funkcionális visszaigazolás érkezik. |
| functionalGroupControlNumber | No | Sztring | A funkcionális visszaigazolás funkcionális csoportjának vezérlési száma. Az érték csak azt a küldési oldalt tölti fel, ahol a partnernek küldött üzenetekhez a funkcionális nyugtázás érkezik |
| isaSegment | No | Sztring | Az üzenet ISA-szegmense. Az érték csak azt a küldési oldalt tölti fel, ahol a partnernek küldött üzenetekhez a funkcionális nyugtázás érkezik |
| gsSegment | No | Sztring | Az üzenet GS-szegmense. Az érték csak azt a küldési oldalt tölti fel, ahol a partnernek küldött üzenetekhez a funkcionális nyugtázás érkezik |
| respondingfunctionalGroupControlNumber | No | Sztring | A válaszoló adatcsere-vezérlő száma |
| respondingFunctionalGroupId | No | Sztring | A válaszoló funkcionális csoport azonosítója, amely a AK101 leképezi a nyugtát |
| respondingtransactionSetControlNumber | No | Sztring | A válaszoló tranzakciónapló-vezérlő száma |
| respondingTransactionSetId | No | Sztring | A válaszoló tranzakció-készlet azonosítója, amely a nyugtán lévő AK201 képezi le. |
| statusCode | Yes | Logikai | A készlettranzakció-készlet nyugtázási állapotának kódja |
| segmentsCount | Yes | Enumeráció | A nyugtázási állapotkód a következő megengedett értékekkel: `Accepted` , `Rejected` , és`AcceptedWithErrors` |
| processingStatus | Yes | Enumeráció | A visszaigazolás feldolgozási állapota a következő megengedett értékekkel: `Received` , `Generated` és`Sent` |
| CorrelationMessageId | No | Sztring | Korrelációs üzenet azonosítója ({AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}) kombinációja |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| ak2Segment | No | Sztring | A fogadott funkcionális csoporton belüli tranzakcióhoz tartozó visszaigazolás |
| ak3Segment | No | Sztring | Adatszegmensben lévő hibák jelentése |
| ak5Segment | No | Sztring | Azt jelzi, hogy elfogadták vagy elutasították-e a AK2-szegmensben azonosított tranzakciós készletet, és miért |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12-csere követési sémája

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

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | No | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Yes | Sztring | Partner minősítő küldése |
| senderIdentifier | Yes | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Yes | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Yes | Sztring | Fogadási partner azonosítója |
| agreementName | No | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy`send` |
| interchangeControlNumber | No | Sztring | Adatcsere-vezérlési szám |
| isaSegment | No | Sztring | Üzenet ISA-szegmense |
| isTechnicalAcknowledgmentExpected | Logikai | Azt jelzi, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben  |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| isa09 | No | Sztring | X12 dokumentum-csere dátuma |
| isa10 | No | Sztring | X12 dokumentum-átváltási idő |
| isa11 | No | Sztring | X12-adatcsere-vezérlési szabvány azonosítója |
| isa12 | No | Sztring | X12-vezérlő verziószáma |
| isa14 | No | Sztring | X12-visszaigazolás szükséges |
| isa15 | No | Sztring | Tesztelési vagy éles kijelző |
| isa16 | No | Sztring | Elem elválasztója |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 adatcsere-visszaigazolási követési sémája

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

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | No | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Yes | Sztring | Partner minősítő küldése |
| senderIdentifier | Yes | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Yes | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Yes | Sztring | Fogadási partner azonosítója |
| agreementName | No | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy`send` |
| interchangeControlNumber | No | Sztring | A partnerektől kapott technikai elismerések átirányítási száma |
| isaSegment | No | Sztring | A partnerektől kapott technikai nyugtázás ISA-szegmense |
| respondingInterchangeControlNumber | No | Sztring | A partnerektől kapott technikai nyugtázás adatcsere-vezérlési száma |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| statusCode | Yes | Enumeráció | Az adatcsere-visszaigazolás állapotkód a következő megengedett értékekkel: `Accepted` , `Rejected` , és`AcceptedWithErrors` |
| processingStatus | Yes | Enumeráció | A visszaigazolás állapota a következő megengedett értékekkel: `Received` , `Generated` , és`Sent` |
| TA102 | No | Sztring | Bankközi dátum |
| ta103 | No | Sztring | Bankközi idő |
| ta105 | No | Sztring | Adatcsere-Megjegyzés kódja |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funkcionális csoport követési sémája

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

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | No | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Yes | Sztring | Partner minősítő küldése |
| senderIdentifier | Yes | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Yes | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Yes | Sztring | Fogadási partner azonosítója |
| agreementName | No | Sztring | Annak a X12-szerződésnek a neve, amelyre az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, fogadás vagy küldés |
| interchangeControlNumber | No | Sztring | Adatcsere-vezérlési szám |
| functionalGroupControlNumber | No | Sztring | Funkcionális vezérlési szám |
| gsSegment | No | Sztring | Message GS-szegmens |
| isTechnicalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben |
| isFunctionalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a funkcionális nyugtázás konfigurálva van-e az X12-szerződésben |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| gs01 | No | Sztring | Funkcionális azonosító kódja |
| gs02 | No | Sztring | Az alkalmazás feladójának kódja |
| gs03 | No | Sztring | Alkalmazás fogadójának kódja |
| gs04 | No | Sztring | Funkcionális csoport dátuma |
| gs05 | No | Sztring | Működési csoport ideje |
| gs07 | No | Sztring | Felelős Ügynökség kódja |
| gs08 | No | Sztring | Azonosító kód a verzióhoz, a kiadáshoz vagy az iparághoz |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 funkcionális csoport nyugtázási követési sémája

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

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | No | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | No | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Yes | Sztring | Partner minősítő küldése |
| senderIdentifier | Yes | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Yes | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Yes | Sztring | Fogadási partner azonosítója |
| agreementName | No | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy`send` |
| interchangeControlNumber | No | Sztring | Az adatcsere-vezérlési szám, amely a küldési oldalra van feltöltve, amikor a partnerektől kapott technikai nyugtát |
| functionalGroupControlNumber | No | Sztring | A technikai visszaigazolás funkcionális csoportjának vezérlési száma, amely a küldési oldal feltöltésére szolgál, ha a partnerektől kapott technikai nyugtát |
| isaSegment | No | Sztring | Ugyanaz, mint az adatcsere-vezérlőelem száma, de csak bizonyos esetekben van feltöltve |
| gsSegment | No | Sztring | Ugyanaz, mint a funkcionális csoport vezérlőelem száma, de csak bizonyos esetekben van feltöltve |
| respondingfunctionalGroupControlNumber | No | Sztring | Az eredeti funkcionális csoport vezérlési száma |
| respondingFunctionalGroupId | No | Sztring | A visszaigazolási funkcionális csoport AZONOSÍTÓjában lévő AK101 leképezése |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| statusCode | Yes | Enumeráció | A nyugtázási állapotkód a következő megengedett értékekkel: `Accepted` , `Rejected` , és`AcceptedWithErrors` |
| processingStatus | Yes | Enumeráció | A visszaigazolás feldolgozási állapota a következő megengedett értékekkel: `Received` , `Generated` és`Sent` |
| ak903 | No | Sztring | Fogadott tranzakciónaplók száma |
| ak904 | No | Sztring | Az azonosított funkcionális csoportban elfogadott tranzakciótípusok száma |
| ak9Segment | No | Sztring | Azt határozza meg, hogy a AK1-szegmensben azonosított funkcionális csoport elfogadva vagy elutasítva, és miért |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Egyéni B2B-követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)