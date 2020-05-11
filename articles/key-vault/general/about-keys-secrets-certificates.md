---
title: Tudnivalók Azure Key Vault kulcsokról, titkokról és tanúsítványokról – Azure Key Vault
description: A kulcsok, titkok és tanúsítványok Azure Key Vault REST-felületének és fejlesztői adatainak áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 627dfee81cb10e4e442b3cefb10d786d87d5c81d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005876"
---
# <a name="about-keys-secrets-and-certificates"></a>A kulcsok, titkos kódok és tanúsítványok ismertetése

A Azure Key Vault lehetővé teszi Microsoft Azure alkalmazások és felhasználók számára, hogy több típusú titkos/kulcsos adatot tároljanak és használjanak:

- Titkosítási kulcsok: több kulcs típust és algoritmust is támogat, és lehetővé teszi a hardveres biztonsági modulok (HSM) használatát a nagy értékű kulcsokhoz. További információ: [a kulcsok ismertetése](../keys/about-keys.md).
- Titkok: biztonságos tárhelyet biztosít a titkos kulcsokhoz, például jelszavakhoz és adatbázis-kapcsolatok karakterláncokhoz. További információ: [About Secrets](../secrets/about-secrets.md).
- Tanúsítványok: a kulcsokra és titkokra épülő tanúsítványokat támogatja, és egy automatikus megújítási funkciót ad hozzá. További információ: [Tudnivalók a tanúsítványokról](../certificates/about-certificates.md).
- Azure Storage: felügyelheti az Azure Storage-fiók kulcsait. Belsőleg Key Vault a kulcsokat egy Azure Storage-fiókkal listázhatja (szinkronizálhatja), és rendszeresen újragenerálhatja (elforgathatja) a kulcsokat. További információ: [a Storage-fiók kulcsainak kezelése a Key Vault](../secrets/overview-storage-keys.md).

További általános információk a Key Vaultről: [about Azure Key Vault](overview.md).

## <a name="data-types"></a>Adattípusok

Tekintse meg a következő témakört: a kulcsok, a titkosítás és az aláírás releváns adattípusaihoz tartozó JOSE-specifikációk.  

-   **algoritmus** – a kulcs műveletének támogatott algoritmusa, például RSA1_5  
-   **rejtjelezett-érték** -titkosítatlan szöveges oktettek, Base64URL használatával kódolva  
-   **kivonatoló érték** – a Base64URL használatával kódolt kivonatoló algoritmus kimenete  
-   **kulcs-típus** – az egyik támogatott kulcs típusa, például RSA (Rivest-a-Adleman).  
-   **egyszerű szöveges érték** – egyszerű szöveges oktettek, Base64URL használatával kódolva  
-   **aláírás-érték** – az Base64URL használatával kódolt aláírási algoritmus kimenete  
-   **base64URL** – a BASE64URL [RFC4648] kódolású bináris érték  
-   **logikai** – igaz vagy hamis  
-   **Identity (identitás** ) – Azure Active Directory identitása (HRE).  
-   **IntDate** – egy JSON decimális érték, amely az 1970-01-01T0:0: 0z UTC és a megadott UTC dátum/idő közötti másodpercek számát jelöli. A dátum-és időpontokra vonatkozó részletekért, valamint az általános és az UTC RFC3339 lásd:.  

## <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A Key Vaultban tárolt objektumok verziószámozást kapnak, amikor egy objektum új példánya jön létre. Minden verzióhoz egyedi azonosító és URL-cím tartozik. Egy objektum első létrehozásakor a rendszer egyedi verzióazonosító-azonosítót kap, és az objektum aktuális verziójaként van megjelölve. Egy új példány ugyanazzal az objektummal való létrehozása lehetővé teszi az új objektum egyedi verziószámát, ami azt eredményezi, hogy az aktuális verzió lesz.  

A Key Vaultban lévő objektumok a specifing egy verziójának vagy az objektum aktuális verziójában lévő műveletek verziójának kihagyásával kezelhetők. Például, ha egy kulcs a névvel `MasterKey`van megadva, és specifing nélkül végez műveleteket, a rendszer a legújabb elérhető verziót használja. A verzió-specifikus azonosítóval végzett műveletek végrehajtása azt eredményezi, hogy a rendszer az objektum adott verzióját használja.  

Az objektumok egyedileg azonosíthatók a Key Vaulton belül egy URL-cím használatával. A rendszeren nincs két objektum ugyanazzal az URL-címmel, a földrajzi helytől függetlenül. Az objektum teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely a Key Vault, az Objektumtípus, a felhasználó által megadott objektumnév és az objektum verziószámát azonosítja. Az Objektumnév megkülönbözteti a kis-és nagybetűket, és nem változtathatók meg. Az objektum verziószámát nem tartalmazó azonosítókat alapazonosítóknak nevezzük.  

További információ: [hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító a következő általános formátumú:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Az elemek magyarázata:  

|||  
|-|-|  
|`keyvault-name`|A Microsoft Azure Key Vault szolgáltatásban található kulcstartó neve.<br /><br /> A felhasználók a Key Vault neveket választják, és globálisan egyediek.<br /><br /> Key Vault neve csak 0-9, a-z, A-Z és-. karakterláncot tartalmazó 3-24 karakterből állhat.|  
|`object-type`|Az objektum típusa, "kulcsok", "titkok" vagy "tanúsítványok".|  
|`object-name`|`object-name` Az a felhasználó által megadott név, és egyedinek kell lennie egy Key Vaulton belül. A névnek 1-127 karakterből álló karakterláncnak kell lennie, betűvel kell kezdődnie, és csak 0-9, a-z, A-Z és-.|  
|`object-version`|Az `object-version` egy rendszer által generált, 32 karakterből álló karakterlánc-azonosító, amely egy objektum egyedi verziójának kezelésére szolgál.|  

## <a name="next-steps"></a>További lépések

- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
