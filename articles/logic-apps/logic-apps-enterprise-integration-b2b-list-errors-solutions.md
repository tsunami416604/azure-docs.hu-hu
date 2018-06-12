---
title: 'Logic Apps B2B hibák és listája megoldások: Azure App Service szolgáltatásban |} Microsoft Docs'
description: Logic Apps B2B hibák és listája megoldások
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 8503792d57dd7ed64434d5a306afce850ced77db
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298338"
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Logic Apps B2B hibák és listája megoldások  
Ez a cikk segítséget nyújt a Logic Apps B2B esetekben fordulhat elő, és ezek a hibák kijavítása megfelelő műveleteket javasol előforduló hibák elhárítása.


## <a name="agreement-resolution"></a>A szerződés felbontás

### <a name="no-agreement-found"></a>* Nem található megállapodás 

|   |   |  
|---|---|
| Hiba leírása: | Nem felel meg a szerződés feloldási paraméterek megállapodás|    
| Felhasználói művelet | Üzleti megállapodás szerinti azonosítókkal való integráció fiókhoz hozzá kell adni a szerződést.</br> Az üzleti identitások meg kell egyeznie a bemeneti üzenet azonosítókra|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* Nem felel meg a identitások megállapodás

|   |   | 
|---|---|
| Hiba leírása: | Nem található olyan identitásokkal szerződés: 'AS2Identity':: "Partner1" és "AS2Identity":: "Partner3"| 
| Felhasználói művelet | Érvénytelen AS2-a vagy AS2-szerződés a konfigurált. </br> Megfelelő AS2 üzenet AS2-a vagy AS2-fejlécek vagy szerződés AS2-azonosítóknak az AS2 üzenet-megállapodás konfigurációjú fejlécek |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Hiányzó AS2 üzenetfejlécek  

|   |   |  
|---|---|
| Hiba leírása:| Érvénytelen AS2-fejléceket. Egy "AS2-a" vagy "AS2-a" fejléc üres| 
| Felhasználói művelet | Az AS2-üzenet érkezett, amely nem tartalmazta a AS2-a vagy AS2-való vagy mindkét fejléceket. </br> Ellenőrizze az AS2 üzenet AS2-származó és AS2-fejlécek és helyes-e azokat a szerződés konfigurációtól függően |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Hiányzó AS2 üzenettörzs és fejlécek    

|   |   |  
|---|---|
| Hiba leírása:| A kérelem tartalma null vagy üres | 
| Felhasználói művelet | Egy AS2-üzenet érkezett, az üzenet törzse nem tartalmazó |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* AS2 üzenet visszafejtés sikertelen

|   |   | 
|---|---|
| Hiba leírása: |  [feldolgozott/hiba: a visszafejtés sikertelen] | 
| Felhasználói művelet | Adja hozzá @base64ToBinary történő elküldése előtt AS2Message partnernek 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* MDN visszafejtés sikertelen

|   |   | 
|---|---|
| Hiba leírása: |  [feldolgozott/hiba: a visszafejtés sikertelen] | 
| Felhasználói művelet | Adja hozzá @base64ToBinary történő elküldése előtt MDN partnernek 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Hiányzó aláíró tanúsítvány

|   |   |  
|---|---|
| Hiba leírása:| Az aláíró tanúsítvány nincs beállítva a AS2 fél számára. </br> AS2-származó: partner1 AS2-való: partner2 | 
| Felhasználói művelet | Az aláírás megfelelő tanúsítvánnyal rendelkező AS2 megállapodás beállításainak konfigurálása |
|  |  | 

## <a name="x12-and-edifact"></a>X12 és EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Kezdő vagy záró található terület    
    
|   |   | 
|---|---|
| Hiba leírása: | Hiba történt az elemzés során. A Edifact tranzakció "123456 azonosítójú"szereplő "987654 azonosítójú (csoport) nélkül interchange", "Partner1" azonosítójú küldő beállításához fogadó azonosítója "Partner2" felfüggeszti a következő hibákkal: vezető záró elválasztó található |
| Felhasználói művelet | A szerződés beállításokat konfigurálni, hogy engedélyezze a kezdő és záró terület. </br> Szerződés beállítások lehetővé teszik a kezdő és záró hely szerkesztése |
|   |   |

![hely engedélyezése](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* A szerződésben ismétlődő ellenőrzése engedélyezve

|   |   | 
|---|---| 
| Hiba leírása: | Ismétlődő ellenőrzőszám |
| Felhasználói művelet | Ez a hiba azt jelzi, a fogadott üzenet ismétlődő vezérlő számokat. </br> Javítsa a vezérlő számot, és küldje el újra az üzenetet |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* A szerződésben hiányzó séma

|   |   | 
|---|---| 
| Hiba leírása: | Hiba történt az elemzés során. A X12 azonosítójú "564220001' funkcionális csoportban szereplő azonosítójú"56422', "000056422" azonosítóval rendelkező Küldőazonosító "12345678 adatcsere a", "87654321 fogadó azonosítója" tranzakció beállítása folyamatban van a következő hibák miatt felfüggesztve, "az üzenet típusa ismeretlen dokumentum és nem tudta feloldani a meglévő sémákat konfigurálva az egyezményben bármelyikéhez" |
| Felhasználói művelet | A szerződés beállításaiban séma konfigurálása  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* A szerződésben hibás sémát

|   |   | 
|---|---| 
| Hiba leírása: | Az üzenet dokumentumtípusa ismeretlen, és az egyezményben konfigurált meglévő sémák egyikével sem sikerült feloldani. |
| Felhasználói művelet | Konfigurálja a megfelelő sémát megállapodás beállításai  |
|   |   |

## <a name="flat-file"></a>Egybesimított fájl

### <a name="-input-message-with-no-body"></a>* Bemeneti üzenet nem a szervezethez

|   |   | 
|---|---|
| Hiba leírása: | InvalidTemplate. Folyamat sablonnyelvi kifejezéseket a művelet "Flat_File_Decoding" bemeneti sor "1" és "1902" oszlop nem: "tulajdonság"tartalom"vár egy értéke azonban null kapott szükséges. Elérési út ".". |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a bemeneti üzenet nem tartalmaz egy szervezet |
|   |   | 

## <a name="learn-more"></a>Részletek
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md)