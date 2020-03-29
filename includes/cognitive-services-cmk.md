---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372193"
---
### <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok tárolásához az Azure Key Vault használatával kell tárolnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services-erőforrás és a key vault kell lennie ugyanabban a régióban és ugyanabban az Azure Active Directory (Azure AD) bérlőben, de lehetnek különböző előfizetések. Az Azure Key Vaultról a [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Egy új Cognitive Services-erőforrás mindig titkosítva van a Microsoft által felügyelt kulcsok használatával. Az erőforrás létrehozásakor nem lehet engedélyezni az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcsok az Azure Key Vaultban tárolódnak, és a key vaultot olyan hozzáférési szabályzatokkal kell kiépíteni, amelyek kulcsengedélyeket adnak a Cognitive Services-erőforráshoz társított felügyelt identitáshoz. A felügyelt identitás csak a CMK tarifacsomag használatával történő létrehozása után érhető el.

Az ügyfél által felügyelt kulcsok azure Key Vault for Cognitive Services titkosítással való használatáról a következő témakört olvashatja:

- [Ügyfél által felügyelt kulcsok konfigurálása a Cognitive Services key vaulttitkosításával az Azure Portalról](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Az ügyfél által kezelt kulcsok engedélyezése lehetővé teszi a rendszer hez rendelt felügyelt identitás, az Azure AD egyik szolgáltatása. Miután a rendszer hez rendelt felügyelt identitás engedélyezve van, ez az erőforrás regisztrálva lesz az Azure Active Directoryban. A regisztráció után a felügyelt identitás hozzáférést kap az ügyfél által felügyelt kulcs beállítása során kiválasztott Key Vaulthoz. További információ a [felügyelt identitásokról.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Ha letiltja a rendszer hez rendelt felügyelt identitások, a hozzáférést a key vault eltávolításra kerül, és az ügyfélkulcsokkal titkosított adatok a továbbiakban nem lesz elérhető. Az adatoktól függő funkciók nem fognak működni.

> [!IMPORTANT]
> A felügyelt identitások jelenleg nem támogatják a címtárközi forgatókönyveket. Amikor az Azure Portalon ügyfél által felügyelt kulcsokat konfigurál, a rendszer automatikusan hozzárendeli a felügyelt identitást a lefedések alatt. Ha ezt követően áthelyezi az előfizetést, erőforráscsoportot vagy erőforrást az egyik Azure AD-könyvtárból egy másikba, az erőforráshoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: **Előfizetés átvitele az Azure AD-könyvtárak között** [gyakori kérdésekben és az Azure-erőforrások felügyelt identitásaival kapcsolatos ismert problémák.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása az Azure Key Vaultban

Az ügyfél által felügyelt kulcsok engedélyezéséhez egy Azure Key Vault használatával kell tárolnia a kulcsokat. Engedélyeznie kell mind a **soft delete,** mind a **Ne ürítse** ki a kulcstartót.

A Cognitive Services titkosítása csak a 2048-as méretű RSA-kulcsokat támogatja. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

### <a name="rotate-customer-managed-keys"></a>Ügyfél által kezelt kulcsok elforgatása

Az azure-key vaultban az ügyfél által felügyelt kulcsot a megfelelőségi szabályzatok szerint forgathatja. A kulcs elforgatása esetén frissítenie kell a Cognitive Services erőforrást az új kulcs URI használatához. Ha meg szeretné tudni, hogyan frissítheti az erőforrást a kulcs új verziójának használatára az Azure Portalon, tekintse meg **a Kulcsverzió frissítése** című szakaszt az [Ügyfél által felügyelt kulcsok konfigurálása a Cognitive Services számára az Azure Portal használatával című szakaszban.](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

A kulcs elforgatása nem indítja el az erőforrásban lévő adatok újratitkosítását. A felhasználótól nincs szükség további műveletre.

### <a name="revoke-access-to-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShell vagy az Azure CLI használatát. További információ: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) vagy [Azure Key Vault CLI.](https://docs.microsoft.com/cli/azure/keyvault) A hozzáférés visszavonása hatékonyan blokkolja a hozzáférést a Cognitive Services-erőforrás összes adatához, mivel a titkosítási kulcs nem érhető el a Cognitive Services számára.


