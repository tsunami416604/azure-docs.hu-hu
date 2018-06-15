---
title: Tanúsítvány-létrehozási módszert
description: Hozzon létre egy tanúsítványt a Key Vault módjai.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058316"
---
# <a name="certificate-creation-methods"></a>Tanúsítvány-létrehozási módszert

 A Key Vault (KV) tanúsítvány vagy létrehozott vagy importált kulcstároló. KV tanúsítvány létrehozásakor, a titkos kulcsot a key vault létre és tanúsítvány tulajdonosa nem elérhető. Hozzon létre egy tanúsítványt a Key Vault módjai a következők:  

-   **Hozzon létre egy önaláírt tanúsítványt:** ezzel hozzon létre egy nyilvános-titkos kulcsból álló kulcspárt, és társíthatja egy tanúsítványt. A tanúsítvány a saját kulcs alá fogja írni.  

-    **Manuálisan hozzon létre egy új tanúsítványt:** ezzel hozzon létre egy nyilvános-titkos kulcsból álló kulcspárt és egy X.509 tanúsítvány-aláírási kérelem létrehozása. Az aláírási kérelem is alá fogja írni a regisztrációszolgáltató vagy a hitelesítésszolgáltató. A tanúsítvány a függőben lévő kulccsal egyesíthető aláírt x509 párosítsa a Key Vault KV tanúsítvány befejezéséhez. Bár ez a módszer a további lépéseket igényel, akkor adja meg nagyobb biztonságot, mert a titkos kulcs létrehozása, és korlátozva van Key Vault. Ennek az az alábbi ábrán a magyarázatát.  

![Tanúsítvány létrehozása a saját hitelesítésszolgáltató](media/certificate-authority-1.png)  

Az alábbi leírásokat felel meg a fenti ábrán a zöld betűkkel lépéseket.

1. A fenti ábrán az alkalmazás belső először létrehoz egy kulcs key vaultban lévő tanúsítványt hoz létre.
2. Key Vault és az alkalmazás ad vissza egy tanúsítvány-aláírási kérelem (CSR)
3. Az alkalmazás a választott hitelesítésszolgáltató a CSR továbbítja.
4. A választott hitelesítésszolgáltató válaszol, egy olyan X509 tanúsítványt.
5. Az alkalmazás befejezi az egy egyesülés következtében a X509 új tanúsítvány létrehozása a hitelesítésszolgáltató tanúsítványa.

-   **Hozzon létre egy tanúsítványt egy ismert kibocsátó szolgáltatónál:** ehhez a metódushoz kibocsátó objektum létrehozásának egyszeri feladatra. Egy kiállító objektum létrehozása után a kulcsot a tároló, a név lehet hivatkozni a KV tanúsítvány házirendje. Egy kérelem KV tanúsítvány létrehozása a tárolóban lévő kulcspár létrehoz és a kibocsátó szolgáltató szolgáltatás segítségével az adatokat a hivatkozott kibocsátó objektumban egy x509 kommunikálni tanúsítványt. A tanúsítvány a kibocsátó szolgáltatásból beolvasott, és a KV befejezéséhez kulcspár egyesített x509 tanúsítvány létrehozása.  

![Hozzon létre egy tanúsítványt a Key Vault közösen hitelesítésszolgáltatótól](media/certificate-authority-2.png)  

Az alábbi leírásokat felel meg a fenti ábrán a zöld betűkkel lépéseket.

1. A fenti ábrán az alkalmazás belső először létrehoz egy kulcs key vaultban lévő tanúsítványt hoz létre.
2. Key Vault küld és SSL tanúsítvány kérése a CA-hoz.
3. Az alkalmazás kérdezi le, egy hurok és várjon, amíg folyamatban a Key vault tanúsítvány befejezésére. A tanúsítvány létrehozása befejeződött, a hitelesítésszolgáltató response x509 fogadásakor a Key Vault tanúsítványt.
4. A hitelesítésszolgáltató válaszol-e a Key Vault SSL tanúsítvány igénylése egy X509 SSL-tanúsítvány.
5. Az új tanúsítvány létrehozása a X509 az egyesülés rendelkező befejezi a Hitelesítésszolgáltatóhoz tartozó tanúsítványt.

## <a name="asynchronous-process"></a>Aszinkron folyamat
KV tanúsítvány létrehozása egy aszinkron folyamatban. Ezzel a művelettel KV tanúsítványkérelem létrehozása és egy HTTP-állapotkódot (elfogadható) 202 adja vissza. A kérelem állapotának hozta létre ezt a műveletet a függőben lévő objektum lekérdezésével követhető nyomon. A teljes URI-címe a függőben lévő objektum eredmény abban az esetben a HELYET megjelölő fejlécet.  

Hozzon létre egy KV tanúsítványt kérelem befejeződése után a függőben lévő objektumot állapotának módosul "kész" "inprogress"lehet, és a KV tanúsítvány új verzió jön létre. Ez lesz az aktuális verzióját.  

## <a name="first-creation"></a>Első létrehozása
 KV tanúsítványt első alkalommal hoz létre, amikor egy megcímezhető kulcs és a titkos kulcsot is létrejön, melynek a neve megegyezik, hogy a tanúsítvány. Ha a név már használatban van, akkor a művelet sikertelen lesz, és http-állapotkódot 409 (Ütközés).
A megcímezhető kulcsot és titkos kulcs beszerzése a KV tanúsítvány attribútumok attribútumaik. A megcímezhető kulcsot és titkos kulcsot, ilyen módon létrehozott felügyelt kulcsok és titkos, amelynek élettartama Key Vault kezeli van megjelölve. Felügyelt kulcsok és titkos írásvédettek. Megjegyzés: Ha KV tanúsítvány lejár, vagy le van tiltva, a megfelelő kulcsot és titkos kulcs válnak működésképtelenné válik.  

 Ha ez az első művelet KV tanúsítvány létrehozása egy házirendet is szükséges.  Egy házirend is tartalmazhatja az egymást követő műveletek lecseréli az szabályzat erőforrás létrehozása. Ha a házirend nincs megadva, a házirend az erőforráshoz a szolgáltatás használatos KV tanúsítvány tovább verziót hoz létre. Vegye figyelembe, hogy a következő verzió létrehozásához vonatkozó kérés van folyamatban, az aktuális KV tanúsítványt, és a megfelelő megcímezhető kulcs és a titkos kulcsot, miközben továbbra is változatlan marad.  

## <a name="self-issued-certificate"></a>Önálló kiállított tanúsítványt
 Hozzon létre egy önálló kiállított tanúsítványt, állítsa be a kibocsátó neve, mint a "Self" a tanúsítvány-házirend a tanúsítási szabályzat a következő példában látható módon.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Ha a kibocsátó neve nincs megadva, a kibocsátó neve "Ismeretlen" van beállítva. Ha kibocsátó "Ismeretlen", a tanúsítvány tulajdonosának kell manuálisan lekérni egy x509 a kibocsátó képes a kiválasztott tanúsítványt, majd a nyilvános x509 egyesítési tanúsítvány függőben lévő tanúsítvány létrehozásának befejezéséhez objektum kulcstároló tanúsítvánnyal.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Hitelesítésszolgáltató közösen szolgáltatók
Tanúsítvány létrehozása lehet manuálisan befejezett vagy használatával a "Self" kibocsátó. Key Vault is partnereknek bizonyos kibocsátó szolgáltatók tanúsítványok létrehozását. A következő típusú tanúsítványt a partner kibocsátó szolgáltatókkal kulcstároló is rendelhető.  

|Szolgáltató|Tanúsítvány típusa|  
|--------------|----------------------|  
|DigiCert|Key Vault kínál a DigiCert OV vagy EV SSL tanúsítvány|
|GlobalCert|Key Vault kínál GlobalSign OV vagy EV SSL tanúsítványok|

 További információ, beleértve a kibocsátó szolgáltatók földrajzi rendelkezésre állását: [tanúsítványkiállítók](/rest/api/keyvault/certificate-issuers.md).

Vegye figyelembe, hogy ha sorrendben helyezkedik el, a kibocsátó szolgáltatónál, azt is figyelembe veszi, vagy felülbírálás a x509 a kiegészítő és a tanúsítvány érvényességi időtartama tanúsítvány típusa alapján.  

 Engedélyezés: A tanúsítványok/létrehozása engedélyre van szüksége.

 ## <a name="see-also"></a>Lásd még:
 - [Kulcsok, a titkos kulcsok és a tanúsítványok](about-keys-secrets-and-certificates.md)
 - [Megfigyelés és kezelés tanúsítvány létrehozása](create-certificate-scenarios.md)
