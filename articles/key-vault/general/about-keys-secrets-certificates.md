---
title: Az Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok – Azure Key Vault
description: Az Azure Key Vault REST-felületének áttekintése és a kulcsok, titkos kulcsok és tanúsítványok fejlesztői adatai.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 241efab246dc903981da570a4191f93cc744bca7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726444"
---
# <a name="about-keys-secrets-and-certificates"></a>Kulcsok, titkos kulcsok és tanúsítványok –

Az Azure Key Vault lehetővé teszi a Microsoft Azure-alkalmazások és felhasználók számára, hogy többféle titkos/kulcsadatot tároljanak és használjanak:

- Kriptográfiai kulcsok: Több kulcstípust és algoritmust támogat, és lehetővé teszi a hardveres biztonsági modulok (HSM) használatát a nagy értékű kulcsokhoz. További információt a [Kulcsok – betekintés című témakörben talál.](../keys/about-keys.md)
- Titkos kulcsok: Biztonságos tárolást biztosít a titkos kulcsok, például a jelszavak és az adatbázis-kapcsolat ihúrjai. További információ: [Of secrets](../secrets/about-secrets.md).
- Tanúsítványok: Támogatja a kulcsokra és titkos kulcsokra épülő tanúsítványokat, és automatikus megújítási szolgáltatást ad hozzá. További információt a Tanúsítványok – további információ [című témakörben talál.](../certificates/about-certificates.md)
- Azure Storage: Az Azure Storage-fiók kulcsait kezelheti. Belsőleg a Key Vault listázhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeres időközönként újragenerálhatja (elforgatja) a kulcsokat. További információt a [Tárfiók kulcsainak kezelése a Key Vault segítségével című témakörben talál.](../secrets/overview-storage-keys.md)

A Key Vaultról további általános tudnivalókat az [Azure Key Vault – tudnivalók.](overview.md)

## <a name="data-types"></a>Adattípusok

A kulcsok, a titkosítás és az aláírás megfelelő adattípusait a JOSE specifikációiban olvassa el.  

-   **algoritmus** - egy támogatott algoritmus egy kulcsművelethez, például RSA1_5  
-   **rejtjelszöveg-érték** - titkosítási szöveg oktett, Base64URL használatával kódolva  
-   **emészthető érték** - a kimenetegy kivonatoló algoritmus, kódolt segítségével Base64URL  
-   **kulcs-típus** - az egyik támogatott kulcstípus, például az RSA (Rivest-Shamir-Adleman).  
-   **egyszerű szöveg-érték** - egyszerű szöveges oktett, Base64URL használatával kódolva  
-   **aláírás-érték** - egy aláírási algoritmus kimenete, Base64URL használatával kódolva  
-   **base64URL** - base64URL [RFC4648] kódolt bináris érték  
-   **logikai -** igaz vagy hamis  
-   **Identitás** – az Azure Active Directory (AAD) identitása.  
-   **IntDate** - JSON decimális érték, amely az 1970-01-01T0:0:0Z UTC és a megadott UTC dátum/idő között eltelt másodpercek számát jelöli. Lásd az RFC3339-et a dátumra/időpontokra vonatkozó részletekért, általában és különösen az UTC-t illetően.  

## <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A Key Vaultban tárolt objektumok verziószámmal rendelkeznek, amikor egy objektum új példányát hozják létre. Minden verzió hoz egy egyedi azonosítót és URL-címet. Amikor egy objektumot először hoznak létre, egyedi verzióazonosítót kap, és az objektum aktuális verziójaként van megjelölve. Az azonos objektumnévvel rendelkező új példány létrehozása egyedi verzióazonosítót ad az új objektumnak, így az lesz az aktuális verzió.  

A Key Vault objektumai az aktuális azonosító vagy egy verzióspecifikus azonosító használatával címezhetők. Ha például egy kulcsot `MasterKey`ad meg a nevével, az aktuális azonosítóval végzett műveletek végrehajtása a rendszer a legújabb elérhető verziót használja. Ha a verzióspecifikus azonosítóval hajt végre műveleteket, a rendszer az objektum adott verzióját használja.  

Az objektumok egyedileg azonosíthatók a Key Vaultban egy URL-cím használatával. A rendszerben nincs két objektum azonos URL-címe, függetlenül a földrajzi helytől. Az objektum teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely azonosítja a Key Vaultot, az objektumtípust, a felhasználó által megadott objektumnevet és az objektumverziót. Az objektumneve nem i. és nem módosítható. Az objektumverziót nem tartalmazó azonosítókat alapazonosítóknak nevezzük.  

További információ: [Hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító általános formátuma a következő:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatás egyik kulcstartójának neve.<br /><br /> A Key Vault-neveket a felhasználó választja ki, és globálisan egyediek.<br /><br /> A Key Vault nevének 3-24 karakterből álló karakterláncnak kell lennie, amely csak 0-9, a-z, A-Z és -.|  
|`object-type`|Az objektum típusa, "kulcsok", "titkos kulcsok" vagy "tanúsítványok".|  
|`object-name`|Az `object-name` egy felhasználó által megadott nevet, és egyedinek kell lennie a Key Vault.An is a user provided name for and must be unique within a Key Vault. A névnek 1-127 karakterből kell lennie, amely csak 0-9, a-z, A-Z és -.|  
|`object-version`|A `object-version` rendszer által létrehozott, 32 karakteres karakterlánc-azonosító, amely opcionálisan egy objektum egyedi verziójának címzésére szolgál.|  

## <a name="next-steps"></a>További lépések

- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)