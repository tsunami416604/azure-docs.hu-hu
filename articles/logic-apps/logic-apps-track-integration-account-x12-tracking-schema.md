---
title: VÁLLALATKÖZI üzenetek X12 követési sémái
description: X12-követési sémák létrehozása, amelyek az Azure Logic Apps integrációs fiókjaiban található B2B-üzeneteket figyelik Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791714"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Sémák létrehozása a X12-üzenetek nyomon követéséhez Azure Logic Apps integrációs fiókjaiban

Ha segítségre van szüksége a vállalatok közötti (B2B) tranzakciók sikerességének, hibáinak és üzenetének figyeléséhez, a következő X12-követési sémákat használhatja az integrációs fiókjában:

* X12-tranzakció készletének követési sémája
* X12 tranzakciós készlet nyugtázási követési sémája
* X12-csere követési sémája
* X12 Interchange visszaigazolás-követési séma
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
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | A X12-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | X12-üzenet fogadójának partnere neve. Választható |
| senderQualifier | Sztring | Partner minősítő küldése. Kötelező |
| senderIdentifier | Sztring | Partner azonosítójának küldése Kötelező |
| receiverQualifier | Sztring | Fogadási partner minősítője. Kötelező |
| receiverIdentifier | Sztring | Fogadási partner azonosítója. Kötelező |
| agreementName | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Felsorolás | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| interchangeControlNumber | Sztring | Az adatcsere-vezérlési szám. Választható |
| functionalGroupControlNumber | Sztring | Funkcionális vezérlési szám. Választható |
| transactionSetControlNumber | Sztring | Tranzakciónapló-vezérlő száma Választható |
| CorrelationMessageId | Sztring | Korrelációs üzenet azonosítója. {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} kombinációja. Választható |
| messageType | Sztring | Készlettranzakció vagy bizonylattípus. Választható |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12 üzenet sikertelen-e. Kötelező |
| isTechnicalAcknowledgmentExpected | Logikai | Azt határozza meg, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben. Kötelező |
| isFunctionalAcknowledgmentExpected | Logikai | Azt határozza meg, hogy a funkcionális nyugtázás konfigurálva van-e a X12-szerződésben. Kötelező |
| needAk2LoopForValidMessages | Logikai | Azt jelzi, hogy az AK2 hurok szükséges-e egy érvényes üzenethez. Kötelező |
| segmentsCount | Egész szám | A X12-tranzakció készletében lévő szegmensek száma. Választható |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 tranzakciós készlet nyugtázási követési sémája

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | A X12-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | X12-üzenet fogadójának partnere neve. Választható |
| senderQualifier | Sztring | Partner minősítő küldése. Kötelező |
| senderIdentifier | Sztring | Partner azonosítójának küldése Kötelező |
| receiverQualifier | Sztring | Fogadási partner minősítője. Kötelező |
| receiverIdentifier | Sztring | Fogadási partner azonosítója. Kötelező |
| agreementName | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Felsorolás | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| interchangeControlNumber | Sztring | A funkcionális visszaigazolás átirányítási számának száma. Az érték csak a Send (küldés) oldalnál töltődik fel, ahol a partnernek küldött üzenetek esetében a funkcionális visszaigazolás érkezett. Választható |
| functionalGroupControlNumber | Sztring | Funkcionális csoport vezérlési számának ellenőrzése. Az érték csak a Send (küldés) oldalnál töltődik fel, ahol a partnernek küldött üzenetek esetében a funkcionális visszaigazolás érkezett. Választható |
| isaSegment | Sztring | Az üzenet ISA-szegmense. Az érték csak a Send (küldés) oldalnál töltődik fel, ahol a partnernek küldött üzenetek esetében a funkcionális visszaigazolás érkezett. Választható |
| gsSegment | Sztring | Az üzenet GS-szegmense. Az érték csak a Send (küldés) oldalnál töltődik fel, ahol a partnernek küldött üzenetek esetében a funkcionális visszaigazolás érkezett. Választható |
| respondingfunctionalGroupControlNumber | Sztring | Az adatcsere-vezérlési számra válaszol. Választható |
| respondingFunctionalGroupId | Sztring | Funkcionális csoport AZONOSÍTÓjának válaszolása, amely a nyugtán lévő AK101 képezi le. Választható |
| respondingtransactionSetControlNumber | Sztring | A tranzakció-set vezérlő számának válaszolása. Választható |
| respondingTransactionSetId | Sztring | A tranzakció-készlet AZONOSÍTÓjának válaszolása, amely a nyugtán lévő AK201 képezi le. Választható |
| StatusCode | Logikai | A tranzakció beállított nyugtázási állapotkódot. Kötelező |
| segmentsCount | Felsorolás | Nyugtázási állapotkód. Az engedélyezett értékek **elfogadása**, **elutasítása**és **AcceptedWithErrors**. Kötelező |
| processingStatus | Felsorolás | A visszaigazolás feldolgozási állapota. Az engedélyezett értékek **fogadása**, **generálása**és **elküldése**. Kötelező |
| CorrelationMessageId | Sztring | Korrelációs üzenet azonosítója. {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} kombinációja. Választható |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12 üzenet sikertelen-e. Kötelező |
| ak2Segment | Sztring | A fogadott funkcionális csoporton belüli tranzakcióhoz tartozó nyugtázási érték. Választható |
| ak3Segment | Sztring | Hibát jelez egy adatszegmensben. Választható |
| ak5Segment | Sztring | Azt jelenti, hogy a AK2-szegmensben azonosított tranzakciótípusok elfogadva vagy visszautasítva lettek-e, és miért. Választható |
||||

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | A X12-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | X12-üzenet fogadójának partnere neve. Választható |
| senderQualifier | Sztring | Partner minősítő küldése. Kötelező |
| senderIdentifier | Sztring | Partner azonosítójának küldése Kötelező |
| receiverQualifier | Sztring | Fogadási partner minősítője. Kötelező |
| receiverIdentifier | Sztring | Fogadási partner azonosítója. Kötelező |
| agreementName | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Felsorolás | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| interchangeControlNumber | Sztring | Az adatcsere-vezérlési szám. Választható |
| isaSegment | Sztring | Üzenet ISA-szegmense. Választható |
| isTechnicalAcknowledgmentExpected | Logikai | Azt határozza meg, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben. Kötelező |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12 üzenet sikertelen-e. Kötelező |
| isa09 | Sztring | X12 dokumentum-csere dátuma. Választható |
| isa10 | Sztring | X12 dokumentum-átváltási idő. Választható |
| isa11 | Sztring | A X12 Interchange Control szabványainak azonosítója. Választható |
| isa12 | Sztring | X12-vezérlő verziószáma. Választható |
| isa14 | Sztring | A rendszer X12 nyugtát kér. Választható |
| isa15 | Sztring | Tesztelési vagy éles üzemi kijelző. Választható |
| isa16 | Sztring | Elem elválasztója Választható |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 Interchange visszaigazolás-követési séma

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | A X12-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | X12-üzenet fogadójának partnere neve. Választható |
| senderQualifier | Sztring | Partner minősítő küldése. Kötelező |
| senderIdentifier | Sztring | Partner azonosítójának küldése Kötelező |
| receiverQualifier | Sztring | Fogadási partner minősítője. Kötelező |
| receiverIdentifier | Sztring | Fogadási partner azonosítója. Kötelező |
| agreementName | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Felsorolás | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| interchangeControlNumber | Sztring | A partnerektől kapott technikai nyugtázási adatcsere-vezérlési száma. Választható |
| isaSegment | Sztring | A partnerektől kapott technikai nyugtázás ISA-szegmense. Választható |
| respondingInterchangeControlNumber |Sztring | A partnerektől kapott technikai Köszönetnyilvánítás esetében az adatcsere-vezérlési szám. Választható |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12 üzenet sikertelen-e. Kötelező |
| StatusCode | Felsorolás | Az adatcsere nyugtázási állapotának kódja. Az engedélyezett értékek **elfogadása**, **elutasítása**és **AcceptedWithErrors**. Kötelező |
| processingStatus | Felsorolás | Nyugtázási állapot. Az engedélyezett értékek **fogadása**, **generálása**és **elküldése**. Kötelező |
| TA102 | Sztring | Az adatcsere dátuma. Választható |
| ta103 | Sztring | Adatcsere ideje. Választható |
| ta105 | Sztring | Adatcsere-Megjegyzés kódja Választható |
||||

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | A X12-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | X12-üzenet fogadójának partnere neve. Választható |
| senderQualifier | Sztring | Partner minősítő küldése. Kötelező |
| senderIdentifier | Sztring | Partner azonosítójának küldése Kötelező |
| receiverQualifier | Sztring | Fogadási partner minősítője. Kötelező |
| receiverIdentifier | Sztring | Fogadási partner azonosítója. Kötelező |
| agreementName | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Felsorolás | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| interchangeControlNumber | Sztring | Az adatcsere-vezérlési szám. Választható |
| functionalGroupControlNumber | Sztring | Funkcionális vezérlési szám. Választható |
| gsSegment | Sztring | Message GS-szegmens Választható |
| isTechnicalAcknowledgmentExpected | Logikai | Azt határozza meg, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben. Kötelező |
| isFunctionalAcknowledgmentExpected | Logikai | Azt határozza meg, hogy a funkcionális nyugtázás konfigurálva van-e a X12-szerződésben. Kötelező |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12 üzenet sikertelen-e. Kötelező|
| gs01 | Sztring | Funkcionális azonosító kódja. Választható |
| gs02 | Sztring | Az alkalmazás feladójának kódja. Választható |
| gs03 | Sztring | Az alkalmazás fogadójának kódja. Választható |
| gs04 | Sztring | Működési csoport dátuma. Választható |
| gs05 | Sztring | Működési csoport ideje. Választható |
| gs07 | Sztring | Felelős Ügynökség kódja. Választható |
| gs08 | Sztring | Verzió/kiadás/iparági azonosító kódja. Választható |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funkcionális csoport nyugtázási követési sémája

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

| Tulajdonság | Type (Típus) | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | A X12-üzenet küldője partnerének neve. Választható |
| receiverPartnerName | Sztring | X12-üzenet fogadójának partnere neve. Választható |
| senderQualifier | Sztring | Partner minősítő küldése. Kötelező |
| senderIdentifier | Sztring | Partner azonosítójának küldése Kötelező |
| receiverQualifier | Sztring | Fogadási partner minősítője. Kötelező |
| receiverIdentifier | Sztring | Fogadási partner azonosítója. Kötelező |
| agreementName | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva. Választható |
| irány | Felsorolás | Az üzenet folyamatának, fogadásának vagy küldésének iránya. Kötelező |
| interchangeControlNumber | Sztring | Az adatcsere-vezérlési szám, amely a küldési oldalra töltődik fel, ha a partnerektől technikai nyugtázás érkezik. Választható |
| functionalGroupControlNumber | Sztring | A technikai nyugtázás funkcionális csoport általi vezérlésének száma, amely a küldési oldal feltöltésére szolgál, ha a partnerektől technikai nyugtázás érkezik. Választható |
| isaSegment | Sztring | Ugyanaz, mint az adatcsere-vezérlőelem száma, de csak bizonyos esetekben van feltöltve. Választható |
| gsSegment | Sztring | Ugyanaz, mint a funkcionális csoport vezérlőelem száma, de csak bizonyos esetekben van feltöltve. Választható |
| respondingfunctionalGroupControlNumber | Sztring | Az eredeti funkcionális csoport vezérlési száma. Választható |
| respondingFunctionalGroupId | Sztring | A AK101 leképezése a nyugtázási funkcionális csoport azonosítója alapján. Választható |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12 üzenet sikertelen-e. Kötelező |
| StatusCode | Felsorolás | Nyugtázási állapotkód. Az engedélyezett értékek **elfogadása**, **elutasítása**és **AcceptedWithErrors**. Kötelező |
| processingStatus | Felsorolás | A visszaigazolás feldolgozási állapota. Az engedélyezett értékek **fogadása**, **generálása**és **elküldése**. Kötelező |
| ak903 | Sztring | A fogadott tranzakciónaplók száma. Választható |
| ak904 | Sztring | Az azonosított funkcionális csoportban elfogadott tranzakciótípusok száma. Választható |
| ak9Segment | Sztring | Azt határozza meg, hogy a AK1-szegmensben azonosított funkcionális csoport el van-e fogadva vagy elutasítva, és miért. Választható |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Egyéni B2B-követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* További információ a [B2B-üzenetek monitorozásáról](logic-apps-monitor-b2b-message.md).
* Ismerje meg [, hogyan követheti nyomon a B2B-üzeneteket Azure monitor naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
