---
title: Tanúsítvány-létrehozási módszerek
description: Tanúsítványok létrehozása a Key Vaultban.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: c27cde85952ca6d982accddad59eceae76e3f1e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194457"
---
# <a name="certificate-creation-methods"></a>Tanúsítvány-létrehozási módszerek

 A Key Vault (KV) tanúsítvány létrehozható vagy importálható egy key vaultba. KV-tanúsítvány létrehozásakor a személyes kulcs a key vaulton belül jön létre, és soha nem érhető el a tanúsítvány tulajdonosa. A tanúsítvány létrehozása a Key Vaultban az alábbi módszereket követi:  

-   **Önaláírt tanúsítvány létrehozása:** Ezzel létrehoz egy nyilvános-titkos kulcspárt, és társítja azt egy tanúsítványhoz. A tanúsítványt saját kulccsal kell aláírni.  

-    **Hozzon létre új tanúsítványt manuálisan:** Ez létrehoz egy nyilvános és titkos kulcspárt, és létrehoz egy X.509 tanúsítványaláíró kérelmet. Az aláírási kérelmet a regisztrációs hatóság vagy a hitelesítésszolgáltató is aláírhatja. Az aláírt x509-es tanúsítvány egyesíthető a függőben lévő kulcspárral a Key Vaultkókk kv-tanúsítványának befejezéséhez. Bár ez a módszer további lépéseket igényel, nagyobb biztonságot nyújt, mert a személyes kulcs a Key Vaultban jön létre, és csak a Key Vaultban van létrehozva. Ezt az alábbi ábra ismerteti.  

![Tanúsítvány létrehozása saját hitelesítésszolgáltatóval](media/certificate-authority-1.png)  

A következő leírások az előző ábrán látható zöld betűkkel írt lépéseknek felelnek meg.

1. A fenti ábrán az alkalmazás tanúsítványt hoz létre, ez pedig belül azzal kezdődik, hogy kulcsot hoz létre a kulcstartóban.
2. A Key Vault visszatér az alkalmazáshoz egy tanúsítvány-aláíró kérelem (CSR)
3. Az alkalmazás a választott CA-nak adja tovább a CSR-t.
4. A kiválasztott hitelesítésszolgáltató X509 tanúsítvánnyal válaszol.
5. Az alkalmazás befejezi az új tanúsítvány létrehozását a hitelesítésszolgáltató X509 tanúsítványának egyesítésével.

-   **Tanúsítvány létrehozása ismert kiállítószolgáltatóval:** Ehhez a módszerhez egyszeri feladatot kell végeznie egy kibocsátó objektum létrehozásához. Miután létrehozott egy kiállítóobjektumot a key vaultban, a neve hivatkozhat a KV-tanúsítvány házirendjében. Egy ilyen KV-tanúsítvány létrehozására irányuló kérelem létrehoz egy kulcspárt a tárolóban, és a hivatkozott kiállítóobjektumban szereplő információk használatával kommunikál a kibocsátó szolgáltatójával az x509-es tanúsítvány lekéréséhez. Az x509-es tanúsítvány t a kiállítószolgáltatásból olvassa be, és a kv-tanúsítvány létrehozásának befejezéséhez egyesíti a kulcspárral.  

![Tanúsítvány létrehozása Key Vault-partnerhitelesítésszolgáltatóval](media/certificate-authority-2.png)  

A következő leírások az előző ábrán látható zöld betűkkel írt lépéseknek felelnek meg.

1. A fenti ábrán az alkalmazás tanúsítványt hoz létre, ez pedig belül azzal kezdődik, hogy kulcsot hoz létre a kulcstartóban.
2. A Key Vault Egy TLS/SSL tanúsítványkérelmet küld a hitelesítésszolgáltatónak.
3. Az alkalmazás ciklikus lekérdezéseket végezve várja meg, hogy a kulcstartó elkészítse a tanúsítványt. A tanúsítvány létrehozása akkor ér véget, amikor a kulcstartó megkapja a CA válaszát az X.509-tanúsítvánnyal.
4. A hitelesítésszolgáltató TLS/SSL X.509 tanúsítvánnyal válaszol a Key Vault TLS/SSL tanúsítványkérelmére.
5. Az új tanúsítvány létrehozása a Hitelesítésszolgáltató TLS/SSL X.509 tanúsítványának egyesítésével fejeződik be.

## <a name="asynchronous-process"></a>Aszinkron folyamat
A KV-tanúsítvány létrehozása aszinkron folyamat. Ez a művelet létrehoz egy KV tanúsítványkérelmet, és 202-es (Elfogadva) http-állapotkódot ad vissza. A kérelem állapota nyomon követhető a művelet által létrehozott függőben lévő objektum lekérdezésével. A függőben lévő objektum teljes URI-ja a LOCATION fejlécben kerül visszaadásra.  

Amikor a KV-tanúsítvány létrehozására irányuló kérelem befejeződik, a függőben lévő objektum állapota "befejezett" lesz a "folyamatban", és a KV-tanúsítvány új verziója jön létre. Ez lesz az aktuális verzió.  

## <a name="first-creation"></a>Első alkotás
 KV-tanúsítvány első létrehozásakor egy címezhető kulcs és titkos kulcs is létrejön a tanúsítvány nevével megegyező névvel. Ha a név már használatban van, akkor a művelet sikertelen lesz egy 409-es http-állapotkóddal (ütközés).
A címezhető kulcs és a titkos kulcs a KV-tanúsítvány attribútumaiból kapja meg az attribútumaikat. A címezhető kulcs és titkos kulcs így létrehozott vannak megjelölve felügyelt kulcsok és titkos kulcsok, amelyek élettartama a Key Vault által kezelt. A felügyelt kulcsok és titkos kulcsok írásvédettek. Megjegyzés: Ha egy KV-tanúsítvány lejár vagy le van tiltva, a megfelelő kulcs és titkos kulcs működésképtelenné válik.  

 Ha ez az első művelet, amely kv-tanúsítványt hoz létre, akkor házirendre van szükség.  A házirend ekként is eladhatók a házirend-erőforrás tleülésére irányuló egymást követő létrehozási műveletek. Ha egy házirend nincs megadva, majd a szolgáltatás házirend-erőforrása a KV-tanúsítvány következő verziójának létrehozásához használatos. Vegye figyelembe, hogy amíg a következő verzió létrehozására irányuló kérelem folyamatban van, az aktuális KV-tanúsítvány, valamint a megfelelő címezhető kulcs és titkos kulcs változatlan marad.  

## <a name="self-issued-certificate"></a>Saját kiadású tanúsítvány
 Saját kiadású tanúsítvány létrehozásához állítsa a kiállító nevét "Self" értékre a tanúsítványházirendben, ahogy az a tanúsítványházirend következő kódrészletében látható.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Ha a kibocsátó neve nincs megadva, akkor a kibocsátó neve "Ismeretlen" értékre van állítva. Ha a kiállító "Ismeretlen", a tanúsítvány tulajdonosának manuálisan kell beszereznie egy x509-es tanúsítványt a választott kiállítótól, majd egyesítenie kell a nyilvános x509-es tanúsítványt a függőben lévő kulcstartó-tanúsítvánnyal a tanúsítvány létrehozásának befejezéséhez.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partneri hitelesítésszolgáltató
A tanúsítvány létrehozása manuálisan vagy "Saját" kiállítóval is elvégezhető. A Key Vault bizonyos kibocsátó szolgáltatókkal is együttműködik a tanúsítványok létrehozásának egyszerűsítése érdekében. A következő típusú tanúsítványok rendelhetők a kulcstartóhoz ezekkel a partnerkibocsátó szolgáltatókkal.  

|Szolgáltató|Tanúsítvány típusa|  
|--------------|----------------------|  
|DigiCert|A Key Vault OV vagy EV SSL tanúsítványokat kínál a DigiCert-tel|
|Globalsign|A Key Vault OV vagy EV SSL tanúsítványokat kínál a GlobalSign segítségével|

 A tanúsítvány kiállítója az Azure Key Vaultban (KV) tanúsítványkiállító-erőforrásként képviselt entitás. A kv-tanúsítvány forrására vonatkozó információk szolgáltatására szolgál; kiállító neve, szolgáltatója, hitelesítő adatai és egyéb felügyeleti adatai.

Ne feledje, hogy amikor egy megrendelést a kibocsátó szolgáltatónál ad le, akkor a tanúsítvány típusa alapján tiszteletben tarthatja vagy felülbírálhatja az x509-es tanúsítványkiterjesztéseket és a tanúsítvány érvényességi időtartamát.  

 Engedélyezés: Tanúsítványok/létrehozási engedély szükséges.

## <a name="see-also"></a>Lásd még:
 - [Kulcsok, titkos kulcsok és tanúsítványok –](about-keys-secrets-and-certificates.md)
 - [Tanúsítvány-létrehozás monitorozása és kezelése](create-certificate-scenarios.md)
