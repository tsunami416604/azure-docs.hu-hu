---
title: Ismerkedés a Key Vault-tanúsítványok használatába
description: A következő forgatókönyvek felvázolják az Key Vault tanúsítványkezelő szolgáltatásának számos elsődleges használatát, beleértve az első tanúsítvány a kulcstartóban való létrehozásához szükséges további lépéseket.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 32a453678fe3702fcb4b77f0b04a8ed5c889ef59
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197623"
---
# <a name="get-started-with-key-vault-certificates"></a>Ismerkedés a Key Vault-tanúsítványok használatába
A következő forgatókönyvek felvázolják az Key Vault tanúsítványkezelő szolgáltatásának számos elsődleges használatát, beleértve az első tanúsítvány a kulcstartóban való létrehozásához szükséges további lépéseket.

A következő lépéseket ismerteti:
- Az első Key Vault-tanúsítvány létrehozása
- Tanúsítvány létrehozása Key Vault-partnerrel együttműködő hitelesítésszolgáltatóval
- Tanúsítvány létrehozása olyan hitelesítésszolgáltatóval, amely nem kapcsolódik Key Vault
- Tanúsítvány importálása

## <a name="certificates-are-complex-objects"></a>A tanúsítványok összetett objektumok
A tanúsítványok három egymáshoz kapcsolódó erőforrásból állnak, amelyek Key Vault tanúsítványként vannak társítva. tanúsítvány metaadatai, kulcs és titkos kód.


![A tanúsítványok összetettek](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Az első Key Vault-tanúsítvány létrehozása  
 Ahhoz, hogy egy tanúsítvány egy Key Vaultban (KV) legyen létrehozva, az 1. és a 2. előfeltételt sikeresen el kell végezni, és léteznie kell egy kulcstárolónak ehhez a felhasználóhoz/szervezethez.  

**1. lépés** – hitelesítésszolgáltatói szolgáltatók  
-   Az adott vállalat számára az informatikai rendszergazdaként, PKI-rendszergazdaként vagy a hitelesítésszolgáltatókkal rendelkező fiókok kezelésével foglalkozó felhasználó (pl. A contoso) Key Vault tanúsítványok használatának előfeltétele.  
    A következő hitelesítésszolgáltatók az aktuális partneri szolgáltatók Key Vault:  
    -   A DigiCert-Key Vault OV TLS/SSL-tanúsítványokat kínál a DigiCert.  
    -   A GlobalSign-Key Vault OV TLS/SSL-tanúsítványokat kínál a GlobalSign.  

**2. lépés** – a hitelesítésszolgáltató-szolgáltatóhoz tartozó fiók rendszergazdája létrehozza a hitelesítő adatokat, amelyeket a Key Vault a TLS/SSL-tanúsítványok Key Vault használatával történő regisztrálásához, megújításához és használatához használhat.

**3. lépés** – a contoso rendszergazdája, valamint a tanúsítványokat birtokló contoso-alkalmazott (Key Vault felhasználó) a hitelesítésszolgáltatótól függően beszerezhet egy tanúsítványt a rendszergazdától, vagy közvetlenül a fiókból a hitelesítésszolgáltatótól.  

- A [tanúsítvány kiállítói erőforrásának beállításával](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) kezdjen hozzá egy hitelesítő adat hozzáadása a kulcstartóhoz. A tanúsítvány kiállítója Azure Key Vault (KV) CertificateIssuer erőforrásként jelölt entitás. A rendszer a KV-tanúsítvány forrására vonatkozó információk megadására szolgál. kiállító neve, szolgáltatója, hitelesítő adatai és egyéb rendszergazdai részletek.
  - Pl. MyDigiCertIssuer  
    -   Szolgáltató  
    -   Hitelesítő adatok – HITELESÍTÉSSZOLGÁLTATÓI fiók hitelesítő adatai. Minden HITELESÍTÉSSZOLGÁLTATÓ saját konkrét adattal rendelkezik.  

    A CA-szolgáltatókkal való fiókok létrehozásával kapcsolatos további információkért tekintse meg a kapcsolódó bejegyzést a [Key Vault blogon](https://aka.ms/kvcertsblog).  

**3,1. lépés** – [tanúsítvány-névjegyek](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) beállítása értesítésekhez. Ez a Key Vault felhasználó kapcsolattartója. A Key Vault nem kényszeríti ki ezt a lépést.  

Megjegyzés: Ez a folyamat az 3,1-es lépéssel egy egyszeri művelet.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Tanúsítvány létrehozása a Key Vault-mel rendelkező HITELESÍTÉSSZOLGÁLTATÓval

![Tanúsítvány létrehozása Key Vault partnerrel rendelkező hitelesítésszolgáltatóval](media/certificate-authority-2.png)

**4. lépés** – az alábbi leírások az előző ábrán látható zöld számú lépéseknek felelnek meg.  
  (1) – a fenti ábrán az alkalmazás olyan tanúsítványt hoz létre, amely belsőleg kezdődik a Key vaultban lévő kulcs létrehozásával.  
  (2) – Key Vault TLS/SSL-tanúsítványkérelem küldését küldi a HITELESÍTÉSSZOLGÁLTATÓNAK.  
  (3) – az alkalmazás egy hurokban és várakozási folyamatban kérdezi le a Key Vault a tanúsítványok befejezéséhez. A tanúsítvány létrehozása akkor fejeződik be, amikor Key Vault megkapja a HITELESÍTÉSSZOLGÁLTATÓ válaszát az x509-tanúsítvánnyal.  
  (4) – a HITELESÍTÉSSZOLGÁLTATÓ a Key Vault TLS/SSL-tanúsítványkérelem X509 TLS/SSL-tanúsítvánnyal való kérelmére válaszol.  
  (5) – az új tanúsítvány létrehozása a HITELESÍTÉSSZOLGÁLTATÓ X509-tanúsítványának egyesítésével fejeződik be.  

  Key Vault felhasználó – tanúsítvány létrehozása egy házirend megadásával

  -   Szükség esetén ismételje meg a műveletet  
  -   Házirend-korlátozások  
      -   X509 tulajdonságai  
      -   Kulcs tulajdonságai  
      -   Szolgáltatói hivatkozás – > ex. MyDigiCertIssure  
      -   Megújítási információ – > ex. 90 nappal a lejárat előtt  

  - A tanúsítvány-létrehozási folyamat általában egy aszinkron folyamat, és a tanúsítvány létrehozása művelet állapotára vonatkozó lekérdezéssel kérdezi le a kulcstartót.  
[Tanúsítvány lekérése művelet](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Állapot: befejezve, hiba miatt sikertelen, vagy megszakítva  
      -   A létrehozás késleltetése miatt elindítható egy megszakítási művelet. Előfordulhat, hogy a Mégse nem érvényes.  

## <a name="import-a-certificate"></a>Tanúsítvány importálása  
 Másik lehetőségként – egy tanúsítvány importálható Key Vault – PFX vagy PEM rendszerbe.  

 A PEM formátumával kapcsolatos további információkért tekintse meg a [kulcsok, titkos kódok és tanúsítványok](about-keys-secrets-and-certificates.md)című témakör tanúsítványok szakaszát.  

 Importálási tanúsítvány – egy PEM-vagy PFX-fájlnak kell lennie a lemezen, és rendelkeznie kell titkos kulccsal. 
-   Meg kell adnia a következőt: tár neve és tanúsítvány neve (a szabályzat nem kötelező)

-   A PEM/PFX-fájlok olyan attribútumokat tartalmaznak, amelyeket a KV képes elemezni és használni a tanúsítvány-házirend feltöltéséhez. Ha már meg van adva egy tanúsítvány-házirend, a KV-ban a PFX/PEM-fájlból származó adatok egyeztetésére kerül.  

-   Ha az importálás végleges, a későbbi műveletek az új szabályzatot fogják használni (új verziók).  

-   Ha nincs további művelet, akkor a Key Vault elküld egy lejárati értesítést. 

-   A felhasználó szerkesztheti a szabályzatot is, amely az importálás időpontjában működik, de olyan alapértékeket tartalmaz, amelyekben nincs megadva információ az importáláskor. Pl. nincs kiállítói információ  

### <a name="formats-of-import-we-support"></a>A támogatott importálási formátumok
A következő típusú importálási típust támogatjuk a PEM-fájlformátum esetében:. Egyetlen PEM-kódolású tanúsítvány, valamint egy PKCS # 8 kódolt, titkosítatlan kulcs, amely a következőkkel rendelkezik

-----A TANÚSÍTVÁNY----------A ZÁRÓ TANÚSÍTVÁNY MEGKEZDÉSE-----

-----A TITKOS KULCS MEGKEZDÉSE----------A TITKOS KULCS BEFEJEZÉSE-----

A tanúsítvány egyesítése során 2 PEM-alapú formátumot támogatunk. Egyesítheti egyetlen PKCS # 8 kódolású tanúsítványt vagy egy Base64 kódolású P7B-fájlt. -----A TANÚSÍTVÁNY----------A ZÁRÓ TANÚSÍTVÁNY MEGKEZDÉSE-----

Jelenleg nem támogatottak az EC-kulcsok PEM formátumban.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Tanúsítvány létrehozása olyan HITELESÍTÉSSZOLGÁLTATÓval, amely nem partneri kapcsolatban áll a Key Vault  
 Ez a módszer lehetővé teszi a más CAs-k használatát, mint a Key Vault partneri szolgáltatói, ami azt jelenti, hogy a szervezete egy tetszőleges HITELESÍTÉSSZOLGÁLTATÓval működik.  

![Tanúsítvány létrehozása saját hitelesítésszolgáltatóval](media/certificate-authority-1.png)  

 A következő lépésekben szereplő leírások az előző ábrán látható zöld betűs lépéseknek felelnek meg.  

  (1) – a fenti ábrán az alkalmazás létrehoz egy tanúsítványt, amely belsőleg kezdődik a Key vaultban lévő kulcs létrehozásával.  

  (2) – Key Vault visszaadja az alkalmazásnak egy tanúsítvány-aláírási kérelmet (CSR).  

  (3) – az alkalmazás továbbítja a CSR-t a kiválasztott HITELESÍTÉSSZOLGÁLTATÓhoz.  

  (4) – a kiválasztott HITELESÍTÉSSZOLGÁLTATÓ válaszol egy X509-tanúsítvánnyal.  

  (5) – az alkalmazás befejezi az új tanúsítvány létrehozását a HITELESÍTÉSSZOLGÁLTATÓTÓL származó X509-tanúsítvány egyesítésével.

## <a name="see-also"></a>Lásd még:

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
