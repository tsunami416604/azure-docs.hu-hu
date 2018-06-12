---
title: Követés sémák B2B X12 figyelési - Azure Logic Apps |} Microsoft Docs
description: Követés sémák X12 használatával figyelheti az Azure-integráció fiókban tranzakciók B2B üzeneteit.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 385ff73d780f62f19af2face591cd0b2291ef396
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300973"
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>A figyelő sikeres, a hibák és az üzenet tulajdonságai üzenetek kezdő vagy X12 követését engedélyezése
Ezek az Azure-integráció fiókban sémák követési X12 segítségével üzleti vállalatközi (B2B) tranzakciók figyeléséhez nyújt segítséget:

* X12 tranzakció követési séma beállítása
* X12 tranzakció séma követési nyugtázási beállítása
* X12 interchange követési séma
* X12 interchange nyugtázási séma nyomon követése
* Funkcionális X12 csoport követési séma
* Funkcionális X12 csoport nyugtázási séma nyomon követése

## <a name="x12-transaction-set-tracking-schema"></a>X12 tranzakció követési séma beállítása
````java

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
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | X12 üzenet címzett partner neve. (Választható lehetőség) |
| senderQualifier | Sztring | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Sztring | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Sztring | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Sztring | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Sztring | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Sztring | Interchange ellenőrző szám. (Választható lehetőség) |
| functionalGroupControlNumber | Sztring | Funkcionális ellenőrző szám. (Választható lehetőség) |
| transactionSetControlNumber | Sztring | Tranzakció ellenőrző szám beállítása. (Választható lehetőség) |
| correlationMessageId | Sztring | Korrelációs azonosító. {AgreementName} kombinációja {*GroupControlNumber*} {TransactionSetControlNumber}. (Választható lehetőség) |
| messageType | Sztring | A tranzakció állítsa be, vagy dokumentum típusa. (Választható lehetőség) |
| isMessageFailed | Logikai | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| isTechnicalAcknowledgmentExpected | Logikai | Hogy a műszaki nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isFunctionalAcknowledgmentExpected | Logikai | Hogy a működési nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| needAk2LoopForValidMessages | Logikai | Hogy a AK2 hurok kell egy érvényes üzenetet. (Kötelező) |
| segmentsCount | Egész szám | A X12 szegmenseinek száma tranzakció beállítása. (Választható lehetőség) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 tranzakció séma követési nyugtázási beállítása
````java

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
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | X12 üzenet címzett partner neve. (Választható lehetőség) |
| senderQualifier | Sztring | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Sztring | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Sztring | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Sztring | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Sztring | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Sztring | A működési nyugtázási vezérlő száma Interchange. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható lehetőség) |
| functionalGroupControlNumber | Sztring | A működési nyugtázási funkcionális csoport vezérlő száma. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható lehetőség) |
| isaSegment | Sztring | ISA-szegmens az üzenet. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható lehetőség) |
| gsSegment | Sztring | Az üzenet szegmens GS. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható lehetőség) |
| respondingfunctionalGroupControlNumber | Sztring | Interchange ellenőrző szám válaszol. (Választható lehetőség) |
| respondingFunctionalGroupId | Sztring | Funkcionális csoport azonosítója, amely AK101 a visszaigazolás válaszol. (Választható lehetőség) |
| respondingtransactionSetControlNumber | Sztring | Válaszol tranzakció ellenőrző szám beállítása. (Választható lehetőség) |
| respondingTransactionSetId | Sztring | Válaszol tranzakció azonosítója, amely AK201 a visszaigazolás beállítása. (Választható lehetőség) |
| állapotkód | Logikai | Tranzakció nyugtázási állapotkód beállítása. (Kötelező) |
| segmentsCount | Enum | Nyugtázási állapotkódot. Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | A nyugtázási feldolgozási állapotát. Két érték engedélyezett **fogadott**, **Generated**, és **küldött**. (Kötelező) |
| correlationMessageId | Sztring | Korrelációs azonosító. {AgreementName} kombinációja {*GroupControlNumber*} {TransactionSetControlNumber}. (Választható lehetőség) |
| isMessageFailed | Logikai | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| ak2Segment | Sztring | A tranzakció, állítsa be a kapott funkcionális csoporton belüli nyugtázási. (Választható lehetőség) |
| ak3Segment | Sztring | A jelentés egy data szegmenséhez hibáit. (Választható lehetőség) |
| ak5Segment | Sztring | A jelentés-e a tranzakciót, állítsa be a AK2 szegmensben lévő azonosított elfogadott vagy nem utasítható el, és ezért. (Választható lehetőség) |

## <a name="x12-interchange-tracking-schema"></a>X12 interchange követési séma
````java

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
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | X12 üzenet címzett partner neve. (Választható lehetőség) |
| senderQualifier | Sztring | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Sztring | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Sztring | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Sztring | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Sztring | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Sztring | Interchange ellenőrző szám. (Választható lehetőség) |
| isaSegment | Sztring | Üzenet ISA szegmens. (Választható lehetőség) |
| isTechnicalAcknowledgmentExpected | Logikai | Hogy a műszaki nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isMessageFailed | Logikai | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| isa09 | Sztring | X12 dokumentum cseréje dátum. (Választható lehetőség) |
| isa10 | Sztring | A dokumentum interchange idő X12. (Választható lehetőség) |
| isa11 | Sztring | X12 interchange vezérlő szabványok azonosítója. (Választható lehetőség) |
| isa12 | Sztring | X12 interchange vezérlő verziószáma. (Választható lehetőség) |
| isa14 | Sztring | X12 nyugtázási van szükség. (Választható lehetőség) |
| isa15 | Sztring | A teszt- vagy éles mutató. (Választható lehetőség) |
| isa16 | Sztring | Elem elválasztó. (Választható lehetőség) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 interchange nyugtázási séma nyomon követése
````java
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
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | X12 üzenet címzett partner neve. (Választható lehetőség) |
| senderQualifier | Sztring | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Sztring | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Sztring | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Sztring | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Sztring | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Sztring | Interchange résztvevőitől kapott a műszaki nyugtázási ellenőrzési számát. (Választható lehetőség) |
| isaSegment | Sztring | ISA szegmense, amely résztvevőitől kapott a műszaki nyugtázási. (Választható lehetőség) |
| respondingInterchangeControlNumber |Sztring | A partnerek-tól kapott a műszaki nyugtázási ellenőrző szám Interchange. (Választható lehetőség) |
| isMessageFailed | Logikai | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| állapotkód | Enum | Interchange nyugtázási állapotkódot. Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | Nyugtázási állapota. Két érték engedélyezett **fogadott**, **Generated**, és **küldött**. (Kötelező) |
| TA102 | Sztring | Interchange dátum. (Választható lehetőség) |
| ta103 | Sztring | Interchange idő. (Választható lehetőség) |
| ta105 | Sztring | Megjegyzés: kód Interchange. (Választható lehetőség) |

## <a name="x12-functional-group-tracking-schema"></a>Funkcionális X12 csoport követési séma
````java

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
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | X12 üzenet címzett partner neve. (Választható lehetőség) |
| senderQualifier | Sztring | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Sztring | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Sztring | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Sztring | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Sztring | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Sztring | Interchange ellenőrző szám. (Választható lehetőség) |
| functionalGroupControlNumber | Sztring | Funkcionális ellenőrző szám. (Választható lehetőség) |
| gsSegment | Sztring | Üzenet GS szegmens. (Választható lehetőség) |
| isTechnicalAcknowledgmentExpected | Logikai | Hogy a műszaki nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isFunctionalAcknowledgmentExpected | Logikai | Hogy a működési nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isMessageFailed | Logikai | Hogy a X12 üzenetet nem sikerült. (Kötelező)|
| gs01 | Sztring | Funkcionális kódját. (Választható lehetőség) |
| gs02 | Sztring | Alkalmazás feladó kódot. (Választható lehetőség) |
| gs03 | Sztring | Alkalmazás fogadó kódot. (Választható lehetőség) |
| gs04 | Sztring | Funkcionális csoport dátuma. (Választható lehetőség) |
| gs05 | Sztring | Funkcionális csoport idő. (Választható lehetőség) |
| gs07 | Sztring | Felelős ügynökség kódot. (Választható lehetőség) |
| gs08 | Sztring | Verzió/kiadás/iparági kódját. (Választható lehetőség) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Funkcionális X12 csoport nyugtázási séma nyomon követése
````java
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
````

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| senderPartnerName | Sztring | X12 üzenetet küldő partner neve. (Választható lehetőség) |
| receiverPartnerName | Sztring | X12 üzenet címzett partner neve. (Választható lehetőség) |
| senderQualifier | Sztring | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Sztring | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Sztring | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Sztring | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Sztring | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható lehetőség) |
| irány | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Sztring | Interchange vezérlő száma, amely feltölti a küldési oldalon műszaki nyugtázási partnertől fogadásakor. (Választható lehetőség) |
| functionalGroupControlNumber | Sztring | A műszaki nyugtázása, amely tölti fel a küldési oldalon, a műszaki nyugtázási partnertől fogadásakor funkcionális csoport vezérlő száma. (Választható lehetőség) |
| isaSegment | Sztring | Interchange azonos szabályozza, hogy a számot, de csak bizonyos esetekben ki van töltve. (Választható lehetőség) |
| gsSegment | Sztring | Ugyanaz, mint funkcionális csoport szabályozza, hogy a számot, de csak bizonyos esetekben ki van töltve. (Választható lehetőség) |
| respondingfunctionalGroupControlNumber | Sztring | Az eredeti funkcionális csoport számát szabályozza. (Választható lehetőség) |
| respondingFunctionalGroupId | Sztring | A nyugtázási funkcionális csoport AK101 leképezve azonosítóját. (Választható lehetőség) |
| isMessageFailed | Logikai | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| állapotkód | Enum | Nyugtázási állapotkódot. Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | A nyugtázási feldolgozási állapotát. Két érték engedélyezett **fogadott**, **Generated**, és **küldött**. (Kötelező) |
| ak903 | Sztring | Beérkezett tranzakció halmazok számát. (Választható lehetőség) |
| ak904 | Sztring | Tranzakció karakterkészleteket elfogadják az azonosított funkcionális csoport. (Választható lehetőség) |
| ak9Segment | Sztring | -E a funkcionális csoporttal, a AK1 szegmensben lévő elfogadott vagy nem utasítható el, és ezért. (Választható lehetőség) |

## <a name="next-steps"></a>További lépések
* További információ [B2B üzenetek figyelése](logic-apps-monitor-b2b-message.md).
* További információ [AS2 követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* További információ [egyéni sémák követési B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* További tudnivalók [Naplóelemzési a B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* További információ a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md).  
