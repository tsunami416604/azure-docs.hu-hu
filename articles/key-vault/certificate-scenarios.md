---
title: Ismerkedés a Key Vault-tanúsítványok
description: A következő esetekben felvázolják a Key Vault tanúsítvány management szolgáltatás többek között a további lépéseket az első tanúsítvány létrehozásához a key vaultban lévő szükséges elsődleges használatokban.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 73a09a99b1dd275b20bcd389e9cc391f638e8bdd
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404263"
---
# <a name="get-started-with-key-vault-certificates"></a>Ismerkedés a Key Vault-tanúsítványok
A következő esetekben felvázolják a Key Vault tanúsítvány management szolgáltatás többek között a további lépéseket az első tanúsítvány létrehozásához a key vaultban lévő szükséges elsődleges használatokban.

Az alábbi eljárásokat:
- Az első a Key Vault-tanúsítvány létrehozása
- Tanúsítvány létrehozása a hitelesítésszolgáltató, amely a Key Vault van közreműködésével
- A Key Vault létrehozása egy tanúsítványt a hitelesítésszolgáltatótól, amely a nem közreműködésével
- Tanúsítvány importálása

## <a name="certificates-are-complex-objects"></a>Tanúsítványok olyan összetett objektumok
Tanúsítványok épülnek fel, a Key Vault-tanúsítvánnyal; egymáshoz kapcsolódó három egymáshoz erőforrások a tanúsítvány metaadatait, a kulcs és a egy titkos kulcsot.


![Tanúsítványok olyan összetett](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Az első a Key Vault-tanúsítvány létrehozása  
 Mielőtt egy tanúsítványt a egy Key Vault (KV) hozható létre, 1. és 2 előfeltételként felsorolt lépéseket kell sikeresen elvégezte, és key vault léteznie kell a felhasználó és szervezet.  

**1. lépés** -szolgáltatók a hitelesítésszolgáltató (CA) tanúsítvány  
-   Az előkészítési a rendszergazda, nyilvános kulcsokra épülő infrastruktúra-rendszergazda, vagy mindenki számára egy adott vállalat (például a hitelesítésszolgáltatók és a fiókok kezelése Contoso) a Key Vault-tanúsítványok használatának előfeltétele.  
    A következő CAs a Key Vault használata az aktuális partnered szolgáltatók:  
    -   A DigiCert – Key Vault kínál OV-SSL DigiCert tanúsítványokat.  
    -   GlobalSign – Key Vault kínál OV-SSL GlobalSign rendelkező tanúsítványokat  
    -   WoSign – Key Vault ajánlatok OV-SSL vagy a Bővített SSL-tanúsítványok WoSign az ügyfél WoSign-fiókjukban az WoSign portálon konfigurált beállítás alapján.  

**2. lépés** -rendszergazdai fiók egy hitelesítésszolgáltató-szolgáltató regisztrálásához, a Key Vault által használandó hitelesítő adatokat hoz létre a újítsa meg és SSL-tanúsítványokat bocsát ki a Key Vault használata.

**3. lépés** – A Contoso-rendszergazda, és a egy Contoso-alkalmazott (Key Vault-felhasználó) függően a CA-tanúsítványok birtokló tanúsítvány beszerzéséhez a rendszergazda vagy közvetlenül a fiók a hitelesítésszolgáltatóhoz.  

-   A key vault Hozzáadás hitelesítő adatok a művelet megkezdése [tanúsítványának kibocsátója beállítás](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) erőforrás. A tanúsítvány kiállítója jelöli az Azure Key Vault (KV) CertificateIssuer erőforrásként entitás. A forrás-KV tanúsítvány; kapcsolatos információk megadására szolgál kiállító neve, szolgáltató, hitelesítő adatok és egyéb felügyeleti adatait.
    -   Pl. MyDigiCertIssuer  
        -   Szolgáltató  
        -   Hitelesítő adatok – a Hitelesítésszolgáltatói fiók hitelesítő adatait. Minden CA a saját konkrét adatokat tartalmaz.  

     Hitelesítésszolgáltató szolgáltatók fiókok létrehozásával kapcsolatos további információkért tekintse meg a kapcsolódó bejegyzését a [Key Vault blog](https://aka.ms/kvcertsblog).  

**3.1. lépés** -beállítása [tanúsítványt névjegyeket](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) az értesítésekhez. Ez az az ügyfél a Key Vault-felhasználó számára. A Key Vault nem kényszeríti ki ezt a lépést.  

Megjegyzés: ezt a folyamatot, 3.1. lépés egy olyan egyszeri művelet.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Key Vault közreműködésével CA-tanúsítvány létrehozása

![Hozzon létre egy tanúsítványt a Key Vault termékekért felelős csoportokkal együttműködésben hitelesítésszolgáltató](media/certificate-authority-2.png)

**4. lépés** – az alábbi leírásokat felel meg a fenti ábrán a zöld számozott lépéseket.  
  (1) – a fenti ábrán az alkalmazást hoz létre egy tanúsítványt, amely belsőleg megkezdi a hozzon létre egy kulcsot tárol a kulcstárolóban.  
  (2) – a Key Vault küld egy SSL tanúsítvány kérelmet a hitelesítésszolgáltatónak.  
  (3) – az alkalmazás lekérdezi, egy hurkot, és várja meg a folyamatban, a Key Vault tanúsítvány befejezésére. A tanúsítvány létrehozása befejeződött, amikor a Key Vault-x509 a CA-válasz fogadása tanúsítványt.  
  (4) – a hitelesítésszolgáltató reagáljon a Key Vault SSL tanúsítvány kérése egy X509 az SSL-tanúsítványt.  
  (5) – az új tanúsítvány létrehozása befejeződik, a X509 az egyesülés az a Hitelesítésszolgáltatóhoz tartozó tanúsítványt.  

  A Key Vault felhasználó számára – létrehoz egy tanúsítványt egy házirend

  -   Ismételje meg igény szerint  
  -   A házirend-korlátozások  
      -   X509 tulajdonságai  
      -   Kulcs tulajdonságai  
      -   Szolgáltató referencia - > ex. MyDigiCertIssure  
      -   Megújítási információkat - > ex. a lejárat előtt 90 nappal  

  - Egy tanúsítvány-létrehozási folyamata általában egy aszinkron folyamat, amely magában foglalja a key vault létrehozása tanúsítványművelet állapotának lekérdezése.  
[Tanúsítványművelet beolvasása](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Állapot: befejezett, sikertelen, hiba adataival vagy, a visszavont  
      -   A késleltetés hozhat létre, mert a művelet megszakítása kezdeményezhetők. Előfordulhat, hogy a Mégse gombra, vagy nem lehet érvényes.  

## <a name="import-a-certificate"></a>Tanúsítvány importálása  
 Másik lehetőségként – egy tanúsítványt a Key Vault – a PFX- vagy PEM be importálhatók.  

 Tanúsítványok című szakaszában talál további tájékoztatást a PEM-formátumú [kapcsolatos kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-and-certificates.md).  

 Tanúsítvány importálása – egy PEM vagy PFX lemezen lehetnek, és a titkos kulcs szükséges. 
-   Meg kell adnia: a tároló nevét és a tanúsítvány neve (házirend nem kötelező)

-   PEM / PFX-fájlokat tartalmazza, az attribútumokat, amelyek KV elemzése, és töltse fel a tanúsítvány-házirend használatával. A tanúsítási szabályzat már meg van adva, KV megpróbálja adategyeztetéshez PFX / PEM-fájlt.  

-   Az importálás után végső későbbi műveletek során fogja használni az új házirend (az új verzió).  

-   Ha nincsenek további műveletek, a Key Vault végzi, először is, egy lejárati értesítés küldése. 

-   Emellett a felhasználó szerkesztheti a szabályzatot, amely importálás idején már működik esetén, de tartalmazza, ahol nem adatok importálása során meghatározott alapértelmezett. Pl. Nincs kibocsátó adatai  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Key Vault nem közreműködésével CA-tanúsítvány létrehozása  
 Ez a módszer lehetővé teszi, hogy más hitelesítésszolgáltatók, mint a Key Vault termékekért felelős csoportokkal együttműködésben szolgáltatók, tehát a szervezet saját maga által kiválasztott hitelesítésszolgáltató együttműködve dolgozhat.  

![A saját hitelesítésszolgáltató-tanúsítvány létrehozása](media/certificate-authority-1.png)  

 A következő lépésben leírások felel meg a fenti ábrán a zöld betűkkel lépéseket.  

  (1) – a fenti ábrán az alkalmazást hoz létre egy tanúsítványt, amely belsőleg megkezdi a hozzon létre egy kulcsot tárol a kulcstárolóban.  

  (2) – a Key Vault és az alkalmazás ad vissza egy tanúsítvány-aláírási kérelem (CSR).  

  (3) – az alkalmazás a választott hitelesítésszolgáltató továbbítja a CSR-fájl.  

  (4) – egy X509 fűzi hozzá a választott Hitelesítésszolgáltatói tanúsítvány.  

  (5) – az alkalmazás befejezi az egyesülés a X509 új tanúsítvány létrehozása a hitelesítésszolgáltató tanúsítványa.

## <a name="see-also"></a>Lásd még:

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
