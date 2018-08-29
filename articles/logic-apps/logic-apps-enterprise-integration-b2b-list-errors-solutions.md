---
title: Hibák és megoldások B2B-forgatókönyvekhez – Azure Logic Apps |} A Microsoft Docs
description: Hibák és megoldások keresése az Azure Logic Apps B2B-forgatókönyvekhez
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 11fbec81e88eec6c7daa9136eb5421387b79d71c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124334"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-hibák és megoldások az Azure Logic Apps

Ez a cikk segítséget nyújt a hibaelhárítás, amely akkor fordulhat elő a Logic Apps B2B-forgatókönyvekhez, és ezek a hibák javítása a megfelelő műveleteket javasol.

## <a name="agreement-resolution"></a>Egyezményt

### <a name="no-agreement-found"></a>Nem található egyezmény 

|   |   |  
|---|---|
| Hiba leírása | Nem található a megállapodás feloldása paraméterekkel szerződés. | 
| Felhasználói művelet | A szerződés a megállapodás szerinti üzleti identitások rendelkező integrációs fiókban lehet hozzáadni. </br>Az üzleti identitások egyeznie kell a bemeneti üzenet azonosítók. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nincs identitású egyezmény

|   |   | 
|---|---|
| Hiba leírása | Nincs identitású egyezmény: "AS2Identity":: "Partner1" és 'AS2Identity':: "Partner3" | 
| Felhasználói művelet | Érvénytelen AS2-a vagy AS2-megállapodás a konfigurált. </br>Javítsa ki az AS2-üzenet "AS2-a" vagy "AS2-a" fejlécek vagy a szerződés az AS2-azonosítók az AS2 üzenetfejlécekben szerződés konfigurációval megfelelően. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>AS2-üzenet fejléc hiányzik  

|   |   |  
|---|---|
| Hiba leírása | Érvénytelen AS2-fejléceket. Egyik a "AS2-a" vagy "AS2-a" fejlécek az üres. | 
| Felhasználói művelet | Olyan AS2-üzenet érkezett, amely nem tartalmaz AS2-a vagy AS2-, vagy mindkét fejlécek. </br> Ellenőrizze az AS2-üzenet AS2-, és az AS2-fejlécek és helyes-e őket szerződés konfigurációja alapján. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Hiányzó AS2-üzenet szövege és a fejlécek    

|   |   |  
|---|---|
| Hiba leírása | A kérelem tartalma nullértékű vagy üres. | 
| Felhasználói művelet | Olyan AS2-üzenet érkezett, amely az üzenet törzse nem tartalmaz. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>AS2-üzenet sikertelen visszafejtés

|   |   | 
|---|---|
| Hiba leírása |  [feldolgozott/hiba: a visszafejtés sikertelen] | 
| Felhasználói művelet | Adjon hozzá @base64ToBinary AS2Message küldése előtt a partnernek. |
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

### <a name="mdn-decryption-failure"></a>MDN sikertelen visszafejtés

|   |   | 
|---|---|
| Hiba leírása |  [feldolgozott/hiba: a visszafejtés sikertelen] | 
| Felhasználói művelet | Adjon hozzá @base64ToBinary MDN küldése előtt a partnernek. | 
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

### <a name="missing-signing-certificate"></a>Aláíró tanúsítvány hiányzik

|   |   |  
|---|---|
| Hiba leírása| Az aláíró tanúsítvány nincs konfigurálva az AS2-fél. </br>AS2-származó: partner1 AS2-történő: partner2 | 
| Felhasználói művelet | AS2-megállapodás beállításait konfigurálja aláírás megfelelő tanúsítványt. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 és EDIFACT

### <a name="leading-or-trailing-space-found"></a>Felesleges kezdő vagy záró szóköz    
    
|   |   | 
|---|---|
| Hiba leírása | Hiba történt az elemzés közben. Az EDIFACT-tranzakciókészlet beállítása ID "123456"tartalmazott (csoport) nélküli adatcsere azonosítója "987654',"Partner1"feladó azonosítója, fogadó azonosítója"Partner2"jelenleg fel van függesztve a következő hibák miatt: <p>"Hátsó elválasztó található vezető" |
| Felhasználói művelet | A szerződés beállításokat kell konfigurálni, hogy a kezdő és záró terület. </br>Szerződés beállításszerkesztési, hogy a kezdő és záró szóközzel. |
|   |   |

![hely engedélyezése](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Ismétlődő ellenőrzés engedélyezve van a szerződés

|   |   | 
|---|---| 
| Hiba leírása | Ismétlődő ellenőrzőszám |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a fogadott üzenethez ismétlődő ellenőrzőszámok rendelkezik. </br>Javítsa ki az ellenőrzőszám, és elküldeni az üzenetet. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>A szerződés hiányzó séma

|   |   | 
|---|---| 
| Hiba leírása | Hiba történt az elemzés közben. A tranzakciókészlet-azonosítójú "564220001' szereplő funkcionális csoport azonosítója '56422', '000056422', ' 12345678 feladóazonosító azonosítójú adatcsere', ' 87654321 Címzettazonosító" jelenleg fel van függesztve a következő hibák miatt X12: <p>"Az üzenet típusa ismeretlen dokumentum és nem tudta feloldani az egyezményben konfigurált meglévő sémák egyikével sem" |
| Felhasználói művelet | A szerződés beállításaiban-séma konfigurálásához.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Helytelen a séma a szerződés

|   |   | 
|---|---| 
| Hiba leírása | Az üzenet dokumentumtípusa ismeretlen, és az egyezményben konfigurált meglévő sémák egyikével sem sikerült feloldani. |
| Felhasználói művelet | Adja meg a szerződés beállítások megfelelő sémát. |
|   |   |

## <a name="flat-file"></a>Egybesimított fájl

### <a name="input-message-with-no-body"></a>A törzs bemeneti üzenet

|   |   | 
|---|---|
| Hiba leírása | InvalidTemplate. Folyamat Sablonnyelv-kifejezéseinek oszlopában "1" sor "1902" művelet "Flat_File_Decoding" bemenetei között nem lehet: "szükséges"content"tulajdonságot vár, hogy egy érték azonban van null értékű. Elérési út ''. ". |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a bemeneti üzenet nem tartalmazza a szervezet. |
|   |   | 

