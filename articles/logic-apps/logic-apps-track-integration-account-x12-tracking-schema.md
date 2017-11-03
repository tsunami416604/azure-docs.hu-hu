---
title: "Követés sémák B2B X12 figyelési - Azure Logic Apps |} Microsoft Docs"
description: "Követés sémák X12 használatával figyelheti az Azure-integráció fiókban tranzakciók B2B üzeneteit."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3782c0a76ea8728a146b3d73774f74c31187cbfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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
| senderPartnerName | Karakterlánc | X12 üzenetet küldő partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | X12 üzenet címzett partner neve. (Választható) |
| senderQualifier | Karakterlánc | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Karakterlánc | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Karakterlánc | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Karakterlánc | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Karakterlánc | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható) |
| Iránya | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Karakterlánc | Interchange ellenőrző szám. (Választható) |
| functionalGroupControlNumber | Karakterlánc | Funkcionális ellenőrző szám. (Választható) |
| transactionSetControlNumber | Karakterlánc | Tranzakció ellenőrző szám beállítása. (Választható) |
| correlationMessageId | Karakterlánc | Korrelációs azonosító. {AgreementName} kombinációja {*GroupControlNumber*} {TransactionSetControlNumber}. (Választható) |
| messageType | Karakterlánc | A tranzakció állítsa be, vagy dokumentum típusa. (Választható) |
| isMessageFailed | Logikai érték | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| isTechnicalAcknowledgmentExpected | Logikai érték | Hogy a műszaki nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isFunctionalAcknowledgmentExpected | Logikai érték | Hogy a működési nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| needAk2LoopForValidMessages | Logikai érték | Hogy a AK2 hurok kell egy érvényes üzenetet. (Kötelező) |
| segmentsCount | Egész szám | A X12 szegmenseinek száma tranzakció beállítása. (Választható) |

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
| senderPartnerName | Karakterlánc | X12 üzenetet küldő partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | X12 üzenet címzett partner neve. (Választható) |
| senderQualifier | Karakterlánc | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Karakterlánc | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Karakterlánc | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Karakterlánc | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Karakterlánc | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható) |
| Iránya | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Karakterlánc | A működési nyugtázási vezérlő száma Interchange. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható) |
| functionalGroupControlNumber | Karakterlánc | A működési nyugtázási funkcionális csoport vezérlő száma. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható) |
| isaSegment | Karakterlánc | ISA-szegmens az üzenet. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható) |
| gsSegment | Karakterlánc | Az üzenet szegmens GS. Értéke csak a küldő oldalon, ahol működési nyugtázása a küldött üzenet érkezett a tölti fel a partner. (Választható) |
| respondingfunctionalGroupControlNumber | Karakterlánc | Interchange ellenőrző szám válaszol. (Választható) |
| respondingFunctionalGroupId | Karakterlánc | Funkcionális csoport azonosítója, amely AK101 a visszaigazolás válaszol. (Választható) |
| respondingtransactionSetControlNumber | Karakterlánc | Válaszol tranzakció ellenőrző szám beállítása. (Választható) |
| respondingTransactionSetId | Karakterlánc | Válaszol tranzakció azonosítója, amely AK201 a visszaigazolás beállítása. (Választható) |
| statusCode | Logikai érték | Tranzakció nyugtázási állapotkód beállítása. (Kötelező) |
| segmentsCount | Enum | Nyugtázási állapotkódot. Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | A nyugtázási feldolgozási állapotát. Két érték engedélyezett **fogadott**, **Generated**, és **küldött**. (Kötelező) |
| correlationMessageId | Karakterlánc | Korrelációs azonosító. {AgreementName} kombinációja {*GroupControlNumber*} {TransactionSetControlNumber}. (Választható) |
| isMessageFailed | Logikai érték | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| ak2Segment | Karakterlánc | A tranzakció, állítsa be a kapott funkcionális csoporton belüli nyugtázási. (Választható) |
| ak3Segment | Karakterlánc | A jelentés egy data szegmenséhez hibáit. (Választható) |
| ak5Segment | Karakterlánc | A jelentés-e a tranzakciót, állítsa be a AK2 szegmensben lévő azonosított elfogadott vagy nem utasítható el, és ezért. (Választható) |

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
| senderPartnerName | Karakterlánc | X12 üzenetet küldő partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | X12 üzenet címzett partner neve. (Választható) |
| senderQualifier | Karakterlánc | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Karakterlánc | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Karakterlánc | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Karakterlánc | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Karakterlánc | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható) |
| Iránya | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Karakterlánc | Interchange ellenőrző szám. (Választható) |
| isaSegment | Karakterlánc | Üzenet ISA szegmens. (Választható) |
| isTechnicalAcknowledgmentExpected | Logikai érték | Hogy a műszaki nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isMessageFailed | Logikai érték | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| isa09 | Karakterlánc | X12 dokumentum cseréje dátum. (Választható) |
| isa10 | Karakterlánc | A dokumentum interchange idő X12. (Választható) |
| isa11 | Karakterlánc | X12 interchange vezérlő szabványok azonosítója. (Választható) |
| isa12 | Karakterlánc | X12 interchange vezérlő verziószáma. (Választható) |
| isa14 | Karakterlánc | X12 nyugtázási van szükség. (Választható) |
| isa15 | Karakterlánc | A teszt- vagy éles mutató. (Választható) |
| isa16 | Karakterlánc | Elem elválasztó. (Választható) |

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
| senderPartnerName | Karakterlánc | X12 üzenetet küldő partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | X12 üzenet címzett partner neve. (Választható) |
| senderQualifier | Karakterlánc | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Karakterlánc | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Karakterlánc | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Karakterlánc | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Karakterlánc | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható) |
| Iránya | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Karakterlánc | Interchange résztvevőitől kapott a műszaki nyugtázási ellenőrzési számát. (Választható) |
| isaSegment | Karakterlánc | ISA szegmense, amely résztvevőitől kapott a műszaki nyugtázási. (Választható) |
| respondingInterchangeControlNumber |Karakterlánc | A partnerek-tól kapott a műszaki nyugtázási ellenőrző szám Interchange. (Választható) |
| isMessageFailed | Logikai érték | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| statusCode | Enum | Interchange nyugtázási állapotkódot. Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | Nyugtázási állapota. Két érték engedélyezett **fogadott**, **Generated**, és **küldött**. (Kötelező) |
| TA102 | Karakterlánc | Interchange dátum. (Választható) |
| ta103 | Karakterlánc | Interchange idő. (Választható) |
| ta105 | Karakterlánc | Megjegyzés: kód Interchange. (Választható) |

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
| senderPartnerName | Karakterlánc | X12 üzenetet küldő partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | X12 üzenet címzett partner neve. (Választható) |
| senderQualifier | Karakterlánc | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Karakterlánc | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Karakterlánc | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Karakterlánc | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Karakterlánc | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható) |
| Iránya | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Karakterlánc | Interchange ellenőrző szám. (Választható) |
| functionalGroupControlNumber | Karakterlánc | Funkcionális ellenőrző szám. (Választható) |
| gsSegment | Karakterlánc | Üzenet GS szegmens. (Választható) |
| isTechnicalAcknowledgmentExpected | Logikai érték | Hogy a műszaki nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isFunctionalAcknowledgmentExpected | Logikai érték | Hogy a működési nyugtázási konfigurálva van a X12 szerződést. (Kötelező) |
| isMessageFailed | Logikai érték | Hogy a X12 üzenetet nem sikerült. (Kötelező)|
| gs01 | Karakterlánc | Funkcionális kódját. (Választható) |
| gs02 | Karakterlánc | Alkalmazás feladó kódot. (Választható) |
| gs03 | Karakterlánc | Alkalmazás fogadó kódot. (Választható) |
| gs04 | Karakterlánc | Funkcionális csoport dátuma. (Választható) |
| gs05 | Karakterlánc | Funkcionális csoport idő. (Választható) |
| gs07 | Karakterlánc | Felelős ügynökség kódot. (Választható) |
| gs08 | Karakterlánc | Verzió/kiadás/iparági kódját. (Választható) |

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
| senderPartnerName | Karakterlánc | X12 üzenetet küldő partner neve. (Választható) |
| receiverPartnerName | Karakterlánc | X12 üzenet címzett partner neve. (Választható) |
| senderQualifier | Karakterlánc | A minősítő erőforráspartner küldése. (Kötelező) |
| senderIdentifier | Karakterlánc | Partner azonosítót elküldése. (Kötelező) |
| receiverQualifier | Karakterlánc | A minősítő erőforráspartner kapnak. (Kötelező) |
| receiverIdentifier | Karakterlánc | Partner azonosítót kapnak. (Kötelező) |
| agreementName | Karakterlánc | A X12 nevét, amelyhez az üzenetek fakadó problémák megoldásával szerződést. (Választható) |
| Iránya | Enum | Az üzenet folyamat iránya küld és fogad. (Kötelező) |
| interchangeControlNumber | Karakterlánc | Interchange vezérlő száma, amely feltölti a küldési oldalon műszaki nyugtázási partnertől fogadásakor. (Választható) |
| functionalGroupControlNumber | Karakterlánc | A műszaki nyugtázása, amely tölti fel a küldési oldalon, a műszaki nyugtázási partnertől fogadásakor funkcionális csoport vezérlő száma. (Választható) |
| isaSegment | Karakterlánc | Interchange azonos szabályozza, hogy a számot, de csak bizonyos esetekben ki van töltve. (Választható) |
| gsSegment | Karakterlánc | Ugyanaz, mint funkcionális csoport szabályozza, hogy a számot, de csak bizonyos esetekben ki van töltve. (Választható) |
| respondingfunctionalGroupControlNumber | Karakterlánc | Az eredeti funkcionális csoport számát szabályozza. (Választható) |
| respondingFunctionalGroupId | Karakterlánc | A nyugtázási funkcionális csoport AK101 leképezve azonosítóját. (Választható) |
| isMessageFailed | Logikai érték | Hogy a X12 üzenetet nem sikerült. (Kötelező) |
| statusCode | Enum | Nyugtázási állapotkódot. Két érték engedélyezett **elfogadott**, **elutasítva**, és **AcceptedWithErrors**. (Kötelező) |
| processingStatus | Enum | A nyugtázási feldolgozási állapotát. Két érték engedélyezett **fogadott**, **Generated**, és **küldött**. (Kötelező) |
| ak903 | Karakterlánc | Beérkezett tranzakció halmazok számát. (Választható) |
| ak904 | Karakterlánc | Tranzakció karakterkészleteket elfogadják az azonosított funkcionális csoport. (Választható) |
| ak9Segment | Karakterlánc | -E a funkcionális csoporttal, a AK1 szegmensben lévő elfogadott vagy nem utasítható el, és ezért. (Választható) |

## <a name="next-steps"></a>Következő lépések
* További információ [B2B üzenetek figyelése](logic-apps-monitor-b2b-message.md).
* További információ [AS2 követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* További információ [egyéni sémák követési B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* További tudnivalók [az Operations Management Suite-portálon B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* További információ a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md).  
