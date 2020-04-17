---
title: A TLS megszüntetése az Azure Key Vault-tanúsítványokkal
description: Megtudhatja, hogyan integrálhatja az Azure Application Gateway-t a Key Vault-tal a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványokhoz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: be558c3e3a68ce6c194dcf98d8f5ff92c4c14edb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457824"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-megszüntetés Key Vault-tanúsítványokkal

[Az Azure Key Vault](../key-vault/general/overview.md) egy platform által felügyelt titkos tároló, amely segítségével titkos kulcsok, kulcsok és TLS/SSL-tanúsítványok védelme. Az Azure Application Gateway támogatja a Key Vaultdal való integrációt a HTTPS-kompatibilis figyelőkhöz csatlakoztatott kiszolgálói tanúsítványok esetében. Ez a támogatás az Application Gateway v2 Termékváltozatára korlátozódik.

A Key Vault-integráció két modellt kínál a TLS-megszüntetéshez:

- Explicit módon a figyelőhöz csatolt TLS/SSL-tanúsítványokat is megadhat. Ez a modell a hagyományos módja annak, hogy tls/Ssl tanúsítványokat adjaát át az Application Gateway tls-végződéshez.
- Adott esetben megadhat egy meglévő Key Vault-tanúsítványra vagy titkos kulcsra mutató hivatkozást, amikor https-kompatibilis figyelőt hoz létre.

Az Application Gateway és a Key Vault integrációja számos előnnyel jár, többek között:

- Nagyobb biztonság, mivel a TLS/SSL-tanúsítványokat közvetlenül nem kezeli az alkalmazásfejlesztő csapat. Az integráció lehetővé teszi, hogy egy külön biztonsági csapat:
  * Alkalmazásátjárók beállítása.
  * Az alkalmazásátjáró életciklusának szabályozása.
  * Engedélyeket adhat a kijelölt alkalmazásátjáróknak a kulcstartóban tárolt tanúsítványok eléréséhez.
- Meglévő tanúsítványok importálásának támogatása a kulcstartóba. Vagy használja a Key Vault API-kat, hogy hozzon létre és kezelje az új tanúsítványok bármelyik megbízható Key Vault-partnerekkel.
- A kulcstartóban tárolt tanúsítványok automatikus megújításának támogatása.

Az Application Gateway jelenleg csak a szoftveráltal ellenőrzött tanúsítványokat támogatja. A hardveres biztonsági modul (HSM) által ellenőrzött tanúsítványok nem támogatottak. Miután az Application Gateway úgy van beállítva, hogy használja a Key Vault-tanúsítványokat, a példányok lekérik a tanúsítványt a Key Vaultból, és helyileg telepítik őket a TLS-végződéshez. A példányok is lekérdezéskey Vault 24 órás időközönként a tanúsítvány megújult verziójának lekéréséhez, ha létezik. Ha frissített tanúsítványt talál, a HTTPS-figyelőhöz jelenleg társított TLS/SSL-tanúsítvány automatikusan elfordul.

> [!NOTE]
> Az Azure Portal csak a KeyVault-tanúsítványokat támogatja, a titkos kulcsokat nem. Az Application Gateway továbbra is támogatja a KeyVault titkos kulcsainak hivatkozását, de csak nem portálon keresztül, például PowerShell, CLI, API, ARM sablonok stb. 

## <a name="how-integration-works"></a>Az integráció működése

Az Application Gateway és a Key Vault integrációja háromlépéses konfigurációs folyamatot igényel:

1. **Felhasználó által hozzárendelt felügyelt identitás létrehozása**

   Hozzon létre vagy használjon fel újra egy meglévő, felhasználó által hozzárendelt felügyelt identitást, amelyet az Application Gateway a nevében a Key Vaultból származó tanúsítványok lekéréséhez használ. További információ: [Mi az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md) Ez a lépés új identitást hoz létre az Azure Active Directory-bérlőben. Az identitás megbízható az identitás létrehozásához használt előfizetés.

1. **A kulcstartó konfigurálása**

   Ezután importálhat egy meglévő tanúsítványt, vagy hozzon létre egy újat a key vaultban. A tanúsítványt az alkalmazásátjárón keresztül futó alkalmazások fogják használni. Ebben a lépésben is használhatja a key vault titkos, amely jelszó nélküli, alap 64 kódolású PFX-fájlként tárolt. Azt javasoljuk, hogy a tanúsítványtípus használata miatt az automatikus megújítási képesség, amely elérhető a tanúsítvány típus objektumok a key vaultban. Miután létrehozott egy tanúsítványt vagy egy titkos kulcsot, hozzáférési szabályzatokat definiál a key vaultban, hogy az identitás hozzáférést *kapjon* a titkos kulcshoz.

1. **Az Application Gateway konfigurálása**

   Miután elvégezte az előző két lépést, beállíthat vagy módosíthat egy meglévő alkalmazásátjárót a felhasználó által hozzárendelt felügyelt identitás használatához. A HTTP-figyelő TLS/SSL-tanúsítványát is beállíthatja úgy, hogy a Key Vault-tanúsítvány vagy titkos azonosító teljes URI-jára mutasson.

   ![Kulcstartó-tanúsítványok](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>További lépések

[A TLS-megszüntetés konfigurálása Key Vault-tanúsítványokkal az Azure PowerShell használatával](configure-keyvault-ps.md)
