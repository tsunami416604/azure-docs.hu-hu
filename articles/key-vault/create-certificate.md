---
title: Tanúsítvány-létrehozási módszerek
description: Hozzon létre egy tanúsítványt a Key Vaultban módjai.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 50306ab4dee13f772c2c34b3c3a8f13d07861fc0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410264"
---
# <a name="certificate-creation-methods"></a>Tanúsítvány-létrehozási módszerek

 A Key Vault (KV) tanúsítvány vagy létrehozása vagy importálása egy kulcstartóba. Ha KV tanúsítvány jön létre a titkos kulcs hozhatók létre a key vault és sosem hagyja el a tanúsítvány tulajdonosának. Hozzon létre egy tanúsítványt a Key Vaultban módjai a következők:  

-   **Hozzon létre egy önaláírt tanúsítványt:** Ez egy nyilvános-titkos kulcspár létrehozása, és társíthatja azt egy tanúsítványt. A tanúsítvány a saját kulcs alá fogja írni.  

-    **Manuálisan hozzon létre egy új tanúsítványt:** Ez egy nyilvános-titkos kulcspár létrehozása, és hozzon létre egy X.509 tanúsítvány-aláírási kérelmet. Az aláírási kérelmet a regisztrációszolgáltató vagy hitelesítésszolgáltató által írhatók alá. A tanúsítvány egyesíthető a függőben lévő kulccsal aláírt x509 párosítsa a KV tanúsítványt a Key Vaultban végrehajtásához. Bár ez a módszer további lépéseket igényel, azt biztosítja a nagyobb biztonságot, mert a titkos kulcs létrehozott, és a Key Vault korlátozott. Ennek az az alábbi ábrán a magyarázatát.  

![A saját hitelesítésszolgáltató-tanúsítvány létrehozása](media/certificate-authority-1.png)  

Az alábbi leírásokat felel meg a fenti ábrán a zöld betűkkel lépéseket.

1. A fenti ábrán az alkalmazás létrehozása egy tanúsítványt, amely belsőleg megkezdi a hozzon létre egy kulcsot tárol a kulcstárolóban.
2. A Key Vault és az alkalmazás ad vissza egy tanúsítvány-aláírási kérelem (CSR)
3. Az alkalmazás a választott hitelesítésszolgáltató továbbítja a CSR-fájl.
4. A választott hitelesítésszolgáltató fűzi hozzá egy olyan X509 tanúsítványt.
5. Az alkalmazás befejezi az egyesülés a X509 új tanúsítvány létrehozása a hitelesítésszolgáltató tanúsítványa.

-   **Hozzon létre egy tanúsítványt egy ismert kibocsátó szolgáltatót:** Ennél a módszernél a kibocsátó objektum létrehozása egy egyszeri feladat. Kiállító objektum létrehozása után a key vault, a neve a házirendben a KV tanúsítvány lehet rá hivatkozni. KV tanúsítvány létrehozására irányuló kérelem fog kulcspár létrehozása a tárolóban, és a kommunikációt a kibocsátó szolgáltató szolgáltatással a hivatkozott kibocsátó objektum szereplő információk segítségével egy x509 beolvasni tanúsítványt. A tanúsítvány veszi át a kibocsátó szolgáltatás és a kulcspárt a KV végrehajtásához az egyesített x509 tanúsítványok létrehozásához.  

![Hozzon létre egy tanúsítványt a Key Vault termékekért felelős csoportokkal együttműködésben hitelesítésszolgáltató](media/certificate-authority-2.png)  

Az alábbi leírásokat felel meg a fenti ábrán a zöld betűkkel lépéseket.

1. A fenti ábrán az alkalmazás létrehozása egy tanúsítványt, amely belsőleg megkezdi a hozzon létre egy kulcsot tárol a kulcstárolóban.
2. A Key Vault küld, és SSL tanúsítvány kérelmet a hitelesítésszolgáltatónak.
3. Az alkalmazás lekérdezi, egy hurkot, és várja meg a folyamatban, a Key Vault tanúsítvány befejezésére. A tanúsítvány létrehozása befejeződött, amikor a Key Vault-x509 a CA-válasz fogadása tanúsítványt.
4. A hitelesítésszolgáltató reagáljon a Key Vault SSL tanúsítvány kérése egy X509 az SSL-tanúsítványt.
5. Az új tanúsítvány létrehozása befejeződik, a X509 az egyesülés az a Hitelesítésszolgáltatóhoz tartozó tanúsítványt.

## <a name="asynchronous-process"></a>Aszinkron folyamat
KV tanúsítvány létrehozása a egy aszinkron folyamat. Ez a művelet KV tanúsítványkérelem létrehozására, és a egy 202 (elfogadva) HTTP-állapotkódot adja vissza. A kérés állapotát nyomon követhetők a függőben lévő, ez a művelet által létrehozott objektum lekérdezésével. A teljes URI-ját a függőben lévő objektum a LOCATION fejlécet adja vissza.  

KV tanúsítvány létrehozására irányuló kérelem befejeződése után a függőben lévő objektum állapota változik "kész" a "inprogress"lehet, és a egy új verziója a KV tanúsítvány jön létre. Ez lesz a jelenlegi verziót.  

## <a name="first-creation"></a>Első létrehozása
 KV tanúsítványt első alkalommal hoz létre, amikor egy címezhető kulcs és titkos kulcs is létrejön a neve megegyezik a tanúsítvány, amely. Ha a név már használatban van, majd a művelet sikertelen lesz, és egy http-állapotkód: 409 (Ütközés).
A megcímezhető kulcs és titkos kulcs lekérése a KV tanúsítvány attribútumok attribútumaik. A megcímezhető kulcsot és titkos kulcsot, így létrehozott felügyelt kulcsok és titkos kódokat, amelynek élettartamát a Key Vault kezeli vannak megjelölve. Felügyelt kulcsok és titkos kulcsok írásvédettek. Megjegyzés: Ha KV tanúsítvány lejár, vagy le van tiltva, a megfelelő kulcsot és titkos kulcs lesz működésképtelenné válik.  

 Ha ez az első művelet KV tanúsítvány létrehozása egy házirend szükség.  Egy házirend is is meg kell adni az egymást követő műveletek cserélje le a házirend-erőforrás létrehozásához. Ha a házirend nincs megadva, a szolgáltatásban a szabályzat erőforrás használatos KV tanúsítvány következő verzióját. Vegye figyelembe, hogy közben a következő verzió létrehozására irányuló kérelem folyamatát, az aktuális KV tanúsítványt, és a megfelelő címmel rendelkező kulcs és titkos kulcsot, továbbra is változatlan marad.  

## <a name="self-issued-certificate"></a>Önálló kiállított tanúsítvány
 Hozzon létre egy önálló kiállított tanúsítványt, állítsa be a kibocsátó neve szerint "Self", a tanúsítvány házirendben tanúsítási szabályzat az alábbi kódrészletben látható módon.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Ha a kiállító neve nincs megadva, a kiállító neve "Ismeretlen" van beállítva. Ha kibocsátó "Ismeretlen", a tanúsítvány tulajdonosának kell manuálisan beolvasása egy x509 a kibocsátó eltávolíthatják a választott tanúsítvány, akkor a nyilvános x509 egyesítési tanúsítvány függőben lévő tanúsítvány létrehozásának befejezéséhez objektum a key vault-tanúsítvánnyal.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Termékekért felelős csoportokkal együttműködésben CA-szolgáltatók
Tanúsítvány létrehozása manuálisan befejezett vagy használatát a "Self" kibocsátó is lehet. A Key Vault partnereknek bizonyos kibocsátó szolgáltatók tanúsítványok létrehozását is. A következő típusú tanúsítványt a key vaulthoz rendelhető ezen partner kibocsátó szolgáltatók.  

|Szolgáltató|Tanúsítvány típusa|  
|--------------|----------------------|  
|DigiCert|Key Vault kínál a DigiCert a OV vagy Bővített SSL-tanúsítványokkal|
|GlobalCert|Key Vault kínál a GlobalSign a OV vagy Bővített SSL-tanúsítványokkal|

 A tanúsítvány kiállítója jelöli az Azure Key Vault (KV) CertificateIssuer erőforrásként entitás. A forrás-KV tanúsítvány; kapcsolatos információk megadására szolgál kiállító neve, szolgáltató, hitelesítő adatok és egyéb felügyeleti adatait.

Vegye figyelembe, hogy amikor egy megrendeléskor kibocsátó-szolgáltatóval, előfordulhat, hogy tartsa tiszteletben, vagy felülbírálás a x509 Tanúsítványmezők és a tanúsítvány érvényességi időtartama alapján a tanúsítvány típusát.  

 Hitelesítés: A tanúsítványok/létrehozás engedélyt igényel.

## <a name="see-also"></a>Lásd még:
 - [Kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-and-certificates.md)
 - [Tanúsítvány-létrehozás monitorozása és kezelése](create-certificate-scenarios.md)
