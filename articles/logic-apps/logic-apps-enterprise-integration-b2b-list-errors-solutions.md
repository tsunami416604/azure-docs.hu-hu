---
title: Megoldások a B2B-forgatókönyvek gyakori hibáira és problémáira
description: Megoldások az Azure Logic Apps b2B-forgatókönyvek hibaelhárításakor előforduló gyakori hibákra és problémákra
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666686"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B hibák és megoldások az Azure Logic Apps

Ez a cikk segít a Logic Apps B2B-forgatókönyvekben előforduló hibák elhárításában, és a hibák kijavításához megfelelő műveleteket javasol.

## <a name="agreement-resolution"></a>A megállapodás felbontása

### <a name="no-agreement-found"></a>Nem található megállapodás 

|   |   |  
|---|---|
| Hibaleírás | Nem található megállapodás a megállapodás feloldási paramétereivel. | 
| Felhasználói művelet | A megállapodást hozzá kell adni az integrációs fiókhoz, amelynek egyeztetett üzleti identitása van. </br>Az üzleti identitások egyeznek meg a bemeneti üzenet azonosítóival. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nem található megállapodás a személyazonossággal

|   |   | 
|---|---|
| Hibaleírás | Nem található megállapodás az azonosítókkal: 'AS2Identity'::'Partner1' és'AS2Identity'::'Partner3' | 
| Felhasználói művelet | Érvénytelen AS2-tól vagy AS2-to konfigurálva a megállapodáshoz. </br>Javítsa ki az AS2-üzenet "AS2-from" vagy "AS2-To" fejléceit, vagy a megállapodás, hogy az AS2 üzenetfejlécekben lévő AS2-azonosítók egyeztetése a megállapodás konfigurációkkal. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Hiányzó AS2-üzenetfejlécek  

|   |   |  
|---|---|
| Hibaleírás | Érvénytelen AS2 fejlécek. Az egyik "AS2-To" vagy "AS2-From" fejléc üres. | 
| Felhasználói művelet | Olyan AS2-üzenet érkezett, amely nem tartalmazta az AS2-From vagy AS2-To vagy mindkét fejlécet. </br> Ellenőrizze az AS2-üzenet AS2-from és AS2-To fejlécek és javítsa ki őket a megállapodás konfigurációja alapján. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Hiányzó AS2-üzenettörzs és fejlécek    

|   |   |  
|---|---|
| Hibaleírás | A kérelem tartalma null értékű vagy üres. | 
| Felhasználói művelet | Olyan AS2-üzenet érkezett, amely nem tartalmazta az üzenet törzsét. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>AS2 üzenetvisszafejtési hiba

|   |   | 
|---|---|
| Hibaleírás |  [processed/Error: decryption-failed] | 
| Felhasználói művelet | Adja @base64ToBinary hozzá az AS2Message-hez, mielőtt elküldene a partnernek. |
|||

Példa:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN-visszafejtési hiba

|   |   | 
|---|---|
| Hibaleírás |  [processed/Error: decryption-failed] | 
| Felhasználói művelet | Adja @base64ToBinary hozzá az MDN-hez, mielőtt elküldene a partnernek. | 
|||

Példa:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Hiányzik az aláíró tanúsítvány

|   |   |  
|---|---|
| Hibaleírás| Az aláíró tanúsítvány nincs beállítva az AS2-félhez. </br>AS2-From: partner1 AS2-To: partner2 | 
| Felhasználói művelet | Konfigurálja az AS2-megállapodás beállításait a megfelelő aláírási tanúsítvánnyal. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 és EDIFACT

### <a name="leading-or-trailing-space-found"></a>Vezető vagy záró terület található    
    
|   |   | 
|---|---|
| Hibaleírás | Az elemzés során hiba történt. A "987654" azonosítóval ellátott "123456" azonosítóval ellátott EDIFACT tranzakciókészleta a "987654" azonosítóval ellátott csomópontban (csoport nélkül) található, a feladó azonosítójával "Partner1", "Partner2" címzettazonosítóval a következő hibákkal függünk fel: <p>"Vezető záró elválasztó található" |
| Felhasználói művelet | A kezdő és záró terület engedélyezéséhez konfigurálandó szerződésbeállítások. </br>A szerződés beállításainak szerkesztése a kezdő és záró tér lehetővé teszi. |
|   |   |

![hely lehetővé tévő](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>A duplikált ellenőrzés engedélyezve van a megállapodásban

|   |   | 
|---|---| 
| Hibaleírás | Duplikált vezérlőszám |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a fogadott üzenet ismétlődő vezérlőszámokkal rendelkezik. </br>Javítsa ki a vezérlőszámát, és küldje el újra az üzenetet. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Hiányzó séma a megállapodásból

|   |   | 
|---|---| 
| Hibaleírás | Az elemzés során hiba történt. Az "564220001" azonosítóval ellátott X12-es tranzakciókészletet az "56422" azonosítóval ellátott funkcionális csoportban, a "000056422" azonosítóval rendelkező csomópontban, a feladó "12345678" azonosítójával, a "87654321" címzettazonosítóval a következő hibákkal függesztik fel: <p>"Az üzenet ismeretlen dokumentumtípussal rendelkezik, és nem oldotta fel a megállapodásban konfigurált meglévő sémákat" |
| Felhasználói művelet | A séma konfigurálása a szerződés beállításaiban.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Helytelen séma a megállapodásban

|   |   | 
|---|---| 
| Hibaleírás | Az üzenet ismeretlen dokumentumtípussal rendelkezik, és nem oldotta fel a szerződésben konfigurált sémákegyikét sem. |
| Felhasználói művelet | Állítsa be a megfelelő sémát a szerződés beállításaiban. |
|   |   |

## <a name="flat-file"></a>Egybesimított fájl

### <a name="input-message-with-no-body"></a>Szövegtörzs nélküli beviteli üzenet

|   |   | 
|---|---|
| Hibaleírás | ÉrvénytelenSablon. Nem lehet feldolgozni a sablon nyelvi kifejezéseit működés közben a "Flat_File_Decoding" bemenetek ordiójában az "1" sorban és az "1902" oszlopban: a "Kötelező tulajdonság" "tartalom" értéket vár, de null értéket kapott. Elérési út". |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a bemeneti üzenet nem tartalmaz törzset. |
|   |   | 

