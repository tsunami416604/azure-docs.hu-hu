---
title: Ismerkedés a Key Vault tanúsítványok
description: A következő esetekben felvázolják a elsődleges Key Vault-tanúsítvány kezelő szolgáltatás, így a key vaultban lévő az első tanúsítvány létrehozásához szükséges további lépéseket is érvényesek.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 665365e06ef02698c10259628f6bf2442ab8d612
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333513"
---
# <a name="get-started-with-key-vault-certificates"></a>Ismerkedés a Key Vault tanúsítványok
A következő esetekben felvázolják a elsődleges Key Vault-tanúsítvány kezelő szolgáltatás, így a key vaultban lévő az első tanúsítvány létrehozásához szükséges további lépéseket is érvényesek.

A következő témakör ismerteti:
- Az első Key Vault-tanúsítvány létrehozása
- A Key Vault hoz létre egy tanúsítványt a hitelesítésszolgáltatótól, amely van közösen
- A tanúsítvány létrehozása a hitelesítésszolgáltató, amely nem közösen az Key Vault
- Tanúsítvány importálása

## <a name="certificates-are-complex-objects"></a>Tanúsítványok olyan összetett objektumok
Három egymáshoz erőforrások, mint a kulcstároló tanúsítványt, egymáshoz kapcsolódó álló tanúsítványok tanúsítvány metaadatait, kulcsok és titkos kulcs.


![Tanúsítványok összetettek](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Az első Key Vault-tanúsítvány létrehozása  
 Tanúsítvány létrehozása a egy Key Vault (KV), előtt előzetesen szükséges lépések leírását, 1 és 2 sikeresen jellegétől és kulcstároló léteznie kell a felhasználó / szervezet.  

**1. lépés** -szolgáltatók hitelesítésszolgáltató-tanúsítvány  
-   Előkészítésének a rendszergazda, nyilvános kulcsokra épülő infrastruktúra vagy bárki, aki a hitelesítésszolgáltatók és a fiókok kezelése egy adott vállalat (például) Contoso) feltétele a Key Vault-tanúsítványok használatával.  
    A következő hitelesítésszolgáltatók az Key Vault partnered szolgáltatókat:  
    -   DigiCert - Key Vault kínál OV SSL DigiCert rendelkező tanúsítványokat.  
    -   GlobalSign - Key Vault kínál OV SSL GlobalSign tanúsítványok  
    -   WoSign - Key Vault ajánlatok OV SSL vagy a WoSign EV SSL-tanúsítványokat az WoSign Portal WoSign fiókjuk az ügyfél által konfigurált beállítás alapján.  

**2. lépés** -fiók rendszergazdája a CA-szolgáltató regisztrálásához Key Vault által használandó hitelesítő adatokat hoz létre újítsa meg, és SSL-tanúsítvány kulcstároló keresztül.

**3. lépés** -A Contoso rendszergazda, és a Contoso alkalmazott (Key Vault felhasználói) tanúsítványokat, attól függően, hogy a hitelesítésszolgáltató birtokló tanúsítvány beszerzése a rendszergazda vagy közvetlenül a fiók a hitelesítésszolgáltatóhoz.  

-   Kulcstároló hozzáadása a hitelesítő adatok művelet először hozzon létre egy [tanúsítvány kiállítója](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers) erőforrás. 
    -   Példa MyDigiCertIssuer  
        -   Szolgáltató  
        -   Hitelesítő adatok – CA fiók hitelesítő adatait. Minden CA saját egyedi adatokat tartalmaz.  

     A CA-szolgáltatókkal fiókokat hozhat létre további információkért tekintse meg a kapcsolódó bejegyzését a [Key Vault blog](http://aka.ms/kvcertsblog).  

**3.1 lépés** – állítsa be a következőt [névjegyek tanúsítvány](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts) értesítéseket. Ez az az ügyfél a Key Vault felhasználó. Key Vault nem kényszeríti ki ezt a lépést.  

Megjegyzés: Ez a folyamat-lépéseket 3.1, egy olyan alkalommal művelet.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>A tanúsítvány egy hitelesítésszolgáltatóhoz együttműködve kulcstároló létrehozása

![Hozzon létre egy tanúsítványt a Key Vault közösen hitelesítésszolgáltatótól](media/certificate-authority-2.png)

**4. lépés** -zöld számozott hajtsa végre a fenti ábrán a megfelelnek az alábbi leírásokat.  
  (1) – a fenti ábrán az alkalmazás belső először létrehoz egy kulcs key vaultban lévő tanúsítványt hoz létre.  
  (2) - Key Vault küld egy SSL tanúsítvány kérelmet a Hitelesítésszolgáltatónak.  
  (3) – az alkalmazás kérdezi le, egy hurok és várjon, amíg folyamatban a Key vault tanúsítvány befejezésére. A tanúsítvány létrehozása befejeződött, a hitelesítésszolgáltató response x509 fogadásakor a Key Vault tanúsítványt.  
  (4) – a hitelesítésszolgáltató válaszol-e a Key Vault SSL tanúsítvány igénylése egy X509 SSL-tanúsítvány.  
  [5] - a a X509 az egyesülés befejezi az új tanúsítvány létrehozása a Hitelesítésszolgáltatóhoz tartozó tanúsítványt.  

  Key Vault felhasználó-tanúsítványt hoz létre egy házirend

  -   Szükség szerint ismételje meg  
  -   Házirend-megkötésekről  
      -   X509 tulajdonságai  
      -   Kulcstulajdonságok  
      -   Szolgáltató referencia - > ex. MyDigiCertIssure  
      -   Megújítási információkat - > ex. 90 nappal a lejáratuk előtt  

  - A tanúsítvány-létrehozási folyamat általában egy aszinkron folyamat, és magában foglalja a kulcstartót a létrehozási tanúsítvány művelet állapotában lekérdezési.  
[Tanúsítvány művelet](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation)  
      -   Állapot:, sikertelen, hiba adatokkal vagy befejeződött, megszakított  
      -   A késleltetés létrehozásához, mert a művelet megszakítása kezdeményezhető. A Mégse gombra a mennyiség vagy vagy nem hatékony.  

## <a name="import-a-certificate"></a>Tanúsítvány importálása  
 A tanúsítvány alternatív – Key Vault – PFX vagy PEM importálhassa.  

 További információ a PEM-formátumba című rész a tanúsítványok [kulcsokat, a titkos kulcsok és a tanúsítványok](about-keys-secrets-and-certificates.md).  

 Tanúsítvány importálása – egy PEM vagy PFX lemezen állnia, és a titkos kulcs szükséges. 
-   Meg kell adnia: tároló neve és a tanúsítvány neve (házirend nem kötelező)

-   PEM / PFX-fájlok KV elemezni, és töltse fel adatokkal a tanúsítási szabályzat attribútumokat tartalmaz. A tanúsítási szabályzat már meg van adva, KV megpróbálja felel meg a PFX adatait / PEM-fájl.  

-   Ha az importálás végső, a további műveletek fogja használni az új házirend (új verzió).  

-   Ha nincsenek további műveletek, az elsőként a Key Vault végzi egy lejárati értesítést küldeni. 

-   Emellett a felhasználó szerkesztheti a házirendet, amely működőképességét az importálás idején, de ha nem áll rendelkezésre információ importálása során meghatározott alapértelmezett tartalmaz. Példa Nincs kibocsátó információ  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>A tanúsítvány egy hitelesítésszolgáltatóhoz nem közösen az Key Vault létrehozása  
 Ez a módszer lehetővé teszi, hogy a többi hitelesítésszolgáltató mint Key Vault közösen szolgáltatók, tehát a szervezet képes együttműködni a választása szerinti hitelesítésszolgáltató használata.  

![Tanúsítvány létrehozása a saját hitelesítésszolgáltató](media/certificate-authority-1.png)  

 A következő lépés leírások felel meg a fenti ábrán a zöld betűkkel lépéseket.  

  (1) – a fenti ábrán az alkalmazás olyan tanúsítványt, amely belső először létrehoz egy kulcs key vaultban lévő hoz létre.  

  (2) - Key Vault és az alkalmazás ad vissza egy tanúsítvány-aláírási kérelem (CSR).  

  (3) – az alkalmazás a választott hitelesítésszolgáltató a CSR továbbítja.  

  (4) – egy X509 válaszol, a választott Hitelesítésszolgáltatói tanúsítvány.  

  [5] – az alkalmazás befejezi az egy egyesülés következtében a X509 új tanúsítvány létrehozása a hitelesítésszolgáltató tanúsítványa.

## <a name="see-also"></a>Lásd még:
- [Műveletek](/rest/api/keyvault/certificate-operations.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
