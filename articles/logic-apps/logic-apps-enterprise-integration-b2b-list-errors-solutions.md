---
title: Gyakori hibák és problémák megoldása a B2B-forgatókönyvekben
description: Gyakori hibákkal és problémákkal kapcsolatos megoldások keresése a B2B-forgatókönyvek hibaelhárításakor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666686"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-hibák és-megoldások a Azure Logic Apps

Ez a cikk segítséget nyújt a Logic Apps B2B forgatókönyvekben előforduló hibák elhárításában, és a hibák kijavításához szükséges lépéseket javasolja.

## <a name="agreement-resolution"></a>Szerződés feloldása

### <a name="no-agreement-found"></a>Nem található szerződés 

|   |   |  
|---|---|
| Hibaleírás | Nem található szerződés-feloldási paraméterekkel rendelkező megállapodás. | 
| Felhasználói művelet | A megállapodást fel kell venni az integrációs fiókba az elfogadott üzleti identitásokkal. </br>Az üzleti identitásoknak egyezniük kell a bemeneti üzenet azonosítókkal. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nem található identitással rendelkező szerződés

|   |   | 
|---|---|
| Hibaleírás | Nem található a következő identitásokkal rendelkező szerződés: "AS2Identity":: "Partner1" and'AS2Identity "::" Partner3 " | 
| Felhasználói művelet | Érvénytelen AS2-from vagy AS2 – a szerződéshez való konfiguráláshoz. </br>Javítsa ki az AS2-üzenet "AS2-from" vagy "AS2-to" fejléceit, vagy a szerződést, hogy az AS2-azonosítók megfeleljenek az AS2-üzeneteknek a szerződés konfigurációjának megfelelően. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Hiányzó AS2-üzenetek fejlécei  

|   |   |  
|---|---|
| Hibaleírás | Érvénytelen AS2-fejlécek. A "AS2-to" vagy az "AS2-from" fejlécek egyike üres. | 
| Felhasználói művelet | Olyan AS2-üzenet érkezett, amely nem tartalmaz AS2-from vagy AS2-to vagy both fejléceket. </br> Győződjön meg arról, hogy az AS2-üzenet AS2-from és AS2-to fejlécek, és javítsa azokat a szerződés konfigurálása alapján. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Hiányzó AS2-üzenet törzse és fejlécei    

|   |   |  
|---|---|
| Hibaleírás | A kérelem tartalma null értékű vagy üres. | 
| Felhasználói művelet | Olyan AS2-üzenet érkezett, amely nem tartalmazza az üzenet törzsét. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>AS2-üzenetek visszafejtésének hibája

|   |   | 
|---|---|
| Hibaleírás |  [feldolgozott/hiba: visszafejtés – sikertelen] | 
| Felhasználói művelet | A @base64ToBinary partnernek való küldés előtt adja hozzá a AS2Message-hez. |
|||

Például:

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
| Hibaleírás |  [feldolgozott/hiba: visszafejtés – sikertelen] | 
| Felhasználói művelet | A @base64ToBinary partnernek való küldés előtt adja hozzá a MDN-hez. | 
|||

Például:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Hiányzó aláíró tanúsítvány

|   |   |  
|---|---|
| Hibaleírás| Az aláíró tanúsítvány nincs konfigurálva az AS2-fél számára. </br>AS2-from: Partner1 AS2-to: Partner2 | 
| Felhasználói művelet | Az AS2-szerződés beállításainak konfigurálása megfelelő tanúsítvánnyal az aláíráshoz. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 és EDIFACT

### <a name="leading-or-trailing-space-found"></a>A kezdő vagy záró terület található    
    
|   |   | 
|---|---|
| Hibaleírás | Hiba történt az elemzés során. A (z) "123456" azonosítójú EDIFACT-tranzakció a (z) "987654" AZONOSÍTÓJÚ, a (z) "Partner1" azonosítójú, "Partner2" fogadó-azonosítóval rendelkező csomópontban a következő hibákkal lesz felfüggesztve: <p>"A vezető záró elválasztó található" |
| Felhasználói művelet | A bevezető és záró terület engedélyezéséhez konfigurálni kívánt szerződési beállítások. </br>A szerződés beállításainak szerkesztése a kezdő és a záró terület engedélyezéséhez. |
|   |   |

![szóköz engedélyezése](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Az ismétlődő ellenőrzési funkció engedélyezve van a szerződésben

|   |   | 
|---|---| 
| Hibaleírás | Ismétlődő vezérlőelem száma |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a fogadott üzenet duplikált számú vezérlőelemet tartalmaz. </br>Javítsa ki a vezérlő számát, és küldje el újra az üzenetet. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Hiányzó séma a szerződésben

|   |   | 
|---|---| 
| Hibaleírás | Hiba történt az elemzés során. A (z) "56422" AZONOSÍTÓJÚ funkcionális csoportban található "564220001" azonosítójú X12-tranzakció a (z) "000056422" azonosítójú, a (z) "12345678" azonosítójú azonosítóval, a (z) "87654321" fogadó azonosítója a következő hibákkal lesz felfüggesztve: <p>"Az üzenet ismeretlen dokumentumtípust tartalmaz, és nem oldotta meg a szerződésben konfigurált meglévő sémák egyikét sem" |
| Felhasználói művelet | Konfigurálja a sémát a szerződés beállításai között.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Helytelen séma a szerződésben

|   |   | 
|---|---| 
| Hibaleírás | Az üzenet ismeretlen dokumentumtípust tartalmaz, és nem oldotta meg a szerződésben konfigurált meglévő sémákat. |
| Felhasználói művelet | Konfigurálja a helyes sémát a szerződés beállításaiban. |
|   |   |

## <a name="flat-file"></a>Egybesimított fájl

### <a name="input-message-with-no-body"></a>Szövegtörzs nélküli bemeneti üzenet

|   |   | 
|---|---|
| Hibaleírás | InvalidTemplate. Nem lehet feldolgozni a sablon nyelvi kifejezéseit a (z) "Flat_File_Decoding" bemenetekben az "1" sor "1902" oszlopában: "a kötelező tulajdonság tartalma" értéknek null értékűnek kell lennie. Elérési út: "". |
| Felhasználói művelet | Ez a hiba azt jelzi, hogy a bemeneti üzenet nem tartalmaz törzset. |
|   |   | 

