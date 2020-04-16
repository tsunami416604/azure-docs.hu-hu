---
title: Ismerkedés a Key Vault-tanúsítványok használatával
description: A következő forgatókönyvek ismertetik a Key Vault tanúsítványkezelési szolgáltatásának számos elsődleges használatát, beleértve az első tanúsítvány létrehozásához szükséges további lépéseket a kulcstartóban.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 5881314f0d3c62e7d6181ebd7bb27a5e0e87729a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431942"
---
# <a name="get-started-with-key-vault-certificates"></a>Ismerkedés a Key Vault-tanúsítványok használatával
A következő forgatókönyvek ismertetik a Key Vault tanúsítványkezelési szolgáltatásának számos elsődleges használatát, beleértve az első tanúsítvány létrehozásához szükséges további lépéseket a kulcstartóban.

A következőket ismertetik:
- Az első Key Vault-tanúsítvány létrehozása
- Tanúsítvány létrehozása a Key Vaultpartnerrel társként rendelkező hitelesítésszolgáltatóval
- Tanúsítvány létrehozása olyan hitelesítésszolgáltatóval, amely nem áll kapcsolatban a Key Vaultszolgáltatással
- Tanúsítvány importálása

## <a name="certificates-are-complex-objects"></a>A tanúsítványok összetett objektumok
A tanúsítványok három, egymással összefüggő erőforrásból állnak, amelyek et key vault-tanúsítványként kapcsolnak össze; tanúsítvány metaadatai, egy kulcs és egy titkos adat.


![A tanúsítványok összetettek](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Az első Key Vault-tanúsítvány létrehozása  
 Mielőtt egy tanúsítványt létre lehetne hozni egy Key Vaultban (KV), az 1.  

**1. lépés** – Hitelesítésszolgáltatói (CA) szolgáltatók  
-   Beszállás rendszergazdaként, PKI-rendszergazdaként vagy a hitelesítésszolgáltatóval rendelkező fiókokat kezelő felhasználóként egy adott vállalatnál (pl. Contoso) a Key Vault-tanúsítványok használatának előfeltétele.  
    A következő hitelesítésszolgáltatók a Key Vault jelenlegi partnerszolgáltatói:  
    -   DigiCert - Key Vault kínál OV TLS / SSL tanúsítványok DigiCert.  
    -   GlobalSign - Key Vault kínál OV TLS / SSL tanúsítványok GlobalSign.  

**2. lépés** : A hitelesítésszolgáltató-szolgáltató fiókrendszergazdája hitelesítő adatokat hoz létre a Key Vault számára a TLS/SSL-tanúsítványok key vaulton keresztüli igényléséhez, megújításához és használatához.

**3. lépés** – A Contoso-rendszergazda, valamint egy Contoso-alkalmazott (Key Vault-felhasználó), aki a hitelesítésszolgáltatótól függően rendelkezik tanúsítványokkal, tanúsítványt kaphat a rendszergazdától vagy közvetlenül a hitelesítésszolgáltatóval rendelkező fiókból.  

- A tanúsítványkiállító ierőforrás [beállításával](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) megkezdheti a hitelesítő adatok hozzáadása műveletet a kulcstárolóhoz. A tanúsítvány kiállítója az Azure Key Vaultban (KV) tanúsítványkiállító-erőforrásként képviselt entitás. A kv-tanúsítvány forrására vonatkozó információk szolgáltatására szolgál; kiállító neve, szolgáltatója, hitelesítő adatai és egyéb felügyeleti adatai.
  - Pl. MyDigiCertKibocsátó  
    -   Szolgáltató  
    -   Hitelesítő adatok – hitelesítésszolgáltatói fiók hitelesítő adatai. Minden hitelesítés-hitelesítési rendszer saját adatokkal rendelkezik.  

    A hitelesítésszolgáltatói szolgáltatókkal való fiókok létrehozásáról a [Key Vault blogon](https://aka.ms/kvcertsblog)található kapcsolódó bejegyzésben talál további információt.  

**3.1. lépés** – [Tanúsítványkapcsolattartók](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) beállítása az értesítésekhez. Ez a key vault-felhasználó kapcsolattartója. A Key Vault nem kényszeríti ki ezt a lépést.  

Megjegyzés - Ez a folyamat a 3.1 lépéssel egyszeri művelet.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Tanúsítvány létrehozása a Key Vaulttal társként rendelkező hitelesítésszolgáltatóval

![Tanúsítvány létrehozása Key Vault-partnerhitelesítésszolgáltatóval](../media/certificate-authority-2.png)

**4. lépés** - A következő leírások megfelelnek az előző ábrán a zöld számozott lépéseknek.  
  (1) - A fenti ábrán az alkalmazás egy tanúsítványt hoz létre, amely belsőleg kezdődik egy kulcs létrehozásával a key vaultban.  
  (2) - A Key Vault TLS/SSL tanúsítványkérelmet küld a hitelesítésszolgáltatónak.  
  (3) - Az alkalmazás lekérdezi, egy hurok és várakozási folyamat, a Key Vault a tanúsítvány befejezéséhez. A tanúsítvány létrehozása akkor ér véget, amikor a kulcstartó megkapja a CA válaszát az X.509-tanúsítvánnyal.  
  (4) - A hitelesítésszolgáltató x509 TLS/SSL-tanúsítvánnyal válaszol a Key Vault TLS/SSL tanúsítványkérelmére.  
  (5) - Az új tanúsítvány létrehozása az X509 tanúsítvány ca összevonásával fejeződik be.  

  Key Vault-felhasználó – tanúsítványt hoz létre egy házirend megadásával

  -   Szükség szerint ismételje meg a műveletet  
  -   Házirend-megkötések  
      -   X509 tulajdonságai  
      -   Fő tulajdonságok  
      -   Szolgáltató hivatkozása - > ex. MyDigiCertIssure  
      -   Megújítási információk - > ex. 90 nappal a lejárat előtt  

  - A tanúsítvány-létrehozási folyamat általában egy aszinkron folyamat, és magában foglalja a lekérdezés a key vault a create tanúsítvány művelet állapotát.  
[Tanúsítványművelet beszereznie](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Állapot: befejeződött, hibainformációkkal nem jártak el, vagy megszakítva  
      -   A létrehozandó késleltetés miatt megszakítási művelet kezdeményezhető. A lemondás lehet, hogy nem hatékony.  

## <a name="import-a-certificate"></a>Tanúsítvány importálása  
 Alternatív megoldásként - a cert importálható Key Vault - PFX vagy PEM.  

 Importtanúsítvány – pem-et vagy PFX-et kell a lemezen lennie, és személyes kulccsal kell rendelkeznie. 
-   Meg kell adnia: a tároló neve és a tanúsítvány neve (a házirend megadása nem kötelező)

-   PEM / PFX fájlokat tartalmaz attribútumokat, hogy a KV tudja elemezni, és használja, hogy feltölti a tanúsítvány házirendet. Ha a tanúsítványházirend már meg van adva, a KV megpróbálja egyeztetni a PFX / PEM fájlból származó adatokat.  

-   Ha az importálás végleges, a további műveletek az új házirendet (új verziókat) fogják használni.  

-   Ha nincsenek további műveletek, az első dolog, amit a Key Vault nem küld egy lejárati értesítést. 

-   Emellett a felhasználó szerkesztheti a házirendet, amely az importálás időpontjában működőképes, de olyan alapértelmezett értékeket is tartalmaz, amelyekben az importáláskor nem adott meg információt. Pl. nincs kibocsátói információ  

### <a name="formats-of-import-we-support"></a>Az általunk támogatott importformátumok
A következő típusú Import for PEM fájlformátumot támogatjuk. Egyetlen PEM kódolású tanúsítvány, valamint egy PKCS#8 kódolású, titkosítatlan kulcs, amely a következő

-----KEZDŐDIK TANÚSÍTVÁNY----- -----BEFEJEZÉSI TANÚSÍTVÁNY-----

-----KEY----- -----END TITKOS----- KULCSÁNAK

A tanúsítványegyesítéskor 2 PEM alapú formátumot támogatunk. Egyesíthet egyetlen PKCS#8 kódolású tanúsítványt vagy egy base64 kódolású P7B fájlt. -----KEZDŐDIK TANÚSÍTVÁNY----- -----BEFEJEZÉSI TANÚSÍTVÁNY-----

Jelenleg nem támogatjuk az EK-kulcsokat PEM formátumban.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Tanúsítvány létrehozása olyan hitelesítésszolgáltatóval, amely nem áll kapcsolatban a Key Vaultmal  
 Ez a módszer lehetővé teszi a Key Vault partnerszolgáltatóitól eltérő hitelesítésszolgáltatókkal való munkát, ami azt jelenti, hogy a szervezet az általa választott hitelesítésszolgáltatóval dolgozhat.  

![Tanúsítvány létrehozása saját hitelesítésszolgáltatóval](../media/certificate-authority-1.png)  

 A következő lépésleírások az előző ábrán látható zöld betűkkel írt lépéseknek felelnek meg.  

  (1) - A fenti ábrán az alkalmazás egy tanúsítványt hoz létre, amely belsőleg kezdődik egy kulcs létrehozásával a key vaultban.  

  (2) - Key Vault visszatér az alkalmazás egy tanúsítvány-aláíró kérelem (CSR).  

  (3) - Az alkalmazás továbbítja a CSR a kiválasztott hitelesítésnél.  

  (4) - A kiválasztott hitelesítésszolgáltató x509-es tanúsítvánnyal válaszol.  

  (5) - Az alkalmazás befejezi az új tanúsítvány létrehozása egy egyesülés a X509 tanúsítvány a CA.

