---
title: Az inaktív adatok fordítótitkosítása
titleSuffix: Azure Cognitive Services
description: Fordító titkosítása adatok inaktív.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372237"
---
# <a name="translator-encryption-of-data-at-rest"></a>Az inaktív adatok fordítótitkosítása

A fordító automatikusan titkosítja az adatokat, amelyeket egyéni fordítási modellek létrehozásához tölt fel, amikor az a felhőben megmarad, és segít a szervezeti biztonsági és megfelelőségi célok elérésében.

## <a name="about-cognitive-services-encryption"></a>A Cognitive Services titkosítása

Az adatok titkosítása és visszafejtése [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) kompatibilis [256 bites AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítással történik. A titkosítás és a visszafejtés transzparens, ami azt jelenti, hogy a titkosítás és a hozzáférés kezelése az Ön számára. Az adatok alapértelmezés szerint biztonságosak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás előnyeinek kihasználásához.

## <a name="about-encryption-key-management"></a>Titkosítási kulcs kezelése –

Az előfizetés alapértelmezés szerint Microsoft által felügyelt titkosítási kulcsokat használ. Ha olyan tarifacsomagot használ, amely támogatja az ügyfél által felügyelt kulcsokat, az [azure](https://portal.azure.com)portal **titkosítási** szakaszában láthatja az erőforrás titkosítási beállításait, ahogy az az alábbi képen látható.

![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/encryptionblade.png)

Olyan előfizetések esetén, amelyek csak a Microsoft által felügyelt titkosítási kulcsokat támogatják, nem lesz **Titkosítás** szakasz.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok az Azure Key Vault segítségével

Lehetőség van arra is, hogy az előfizetést saját kulcsokkal kezelje. Az ügyfél által felügyelt kulcsok (CMK), más néven a Saját kulcs (BYOK) létrehozása, elforgatása, letiltása és visszavonása nagyobb rugalmasságot biztosít a hozzáférés-vezérlők létrehozásához, elforgatásához, letiltásához és visszavonásához. Az adatok védelmére használt titkosítási kulcsokat is naplózhatja.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok a Fordító szolgáltatás összes tarifacsomaghoz elérhetők. Az ügyfél által kezelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Fordító ügyfél által felügyelt kulcskérelem-űrlapot,](https://aka.ms/cogsvc-cmk) körülbelül 3-5 munkanapba telik, mire visszahallgatja a kérés állapotát. Az igénytől függően előfordulhat, hogy várólistába kerül, és jóváhagyják, amint rendelkezésre áll a hely. Miután jóváhagyta a CMK használatát a fordító szolgáltatással, létre kell hoznia egy új fordítói erőforrást. A fordítói erőforrás létrehozása után az Azure Key Vault segítségével beállíthatja a felügyelt identitást.

Az alábbi lépésekkel engedélyezheti az ügyfél által felügyelt kulcsokat a Fordítószámára:

1. Hozza létre az új regionális fordítószöveg vagy regionális Cognitive Services erőforrás. Ez nem fog működni egy globális erőforrással.
2. Engedélyezett felügyelt identitás az Azure Portalon, és adja meg az ügyfél által felügyelt kulcsadatait.
3. Hozzon létre egy új munkaterületet az egyéni fordítóban, és társítsa az előfizetési adatokat.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok tárolásához az Azure Key Vault használatával kell tárolnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services-erőforrás és a key vault kell lennie ugyanabban a régióban és ugyanabban az Azure Active Directory (Azure AD) bérlőben, de lehetnek különböző előfizetések. Az Azure Key Vaultról a [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Egy új Cognitive Services-erőforrás mindig titkosítva van a Microsoft által felügyelt kulcsok használatával. Az erőforrás létrehozásakor nem lehet engedélyezni az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcsok az Azure Key Vaultban tárolódnak, és a key vaultot olyan hozzáférési szabályzatokkal kell kiépíteni, amelyek kulcsengedélyeket adnak a Cognitive Services-erőforráshoz társított felügyelt identitáshoz. A felügyelt identitás az erőforrás létrehozásaután azonnal elérhetővé válik.

Az ügyfél által felügyelt kulcsok azure Key Vault for Cognitive Services titkosítással való használatáról a következő témakört olvashatja:

- [Ügyfél által felügyelt kulcsok konfigurálása a Cognitive Services key vaulttitkosításával az Azure Portalról](../Encryption/cognitive-services-encryption-keys-portal.md)

Az ügyfél által kezelt kulcsok engedélyezése lehetővé teszi a rendszer hez rendelt felügyelt identitás, az Azure AD egyik szolgáltatása. Miután a rendszer hez rendelt felügyelt identitás engedélyezve van, ez az erőforrás regisztrálva lesz az Azure Active Directoryban. A regisztráció után a felügyelt identitás hozzáférést kap az ügyfél által felügyelt kulcs beállítása során kiválasztott Key Vaulthoz. További információ a [felügyelt identitásokról.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Ha letiltja a rendszer hez rendelt felügyelt identitások, a hozzáférést a key vault eltávolításra kerül, és az ügyfélkulcsokkal titkosított adatok a továbbiakban nem lesz elérhető. Az adatoktól függő funkciók nem fognak működni. A telepített modellek is lelesznek telepítve. Minden feltöltött adat törlődik az egyéni fordítóból. Ha a felügyelt identitások újra engedélyezve vannak, nem telepítjük újra automatikusan a modellt.

> [!IMPORTANT]
> A felügyelt identitások jelenleg nem támogatják a címtárközi forgatókönyveket. Amikor az Azure Portalon ügyfél által felügyelt kulcsokat konfigurál, a rendszer automatikusan hozzárendeli a felügyelt identitást a lefedések alatt. Ha ezt követően áthelyezi az előfizetést, erőforráscsoportot vagy erőforrást az egyik Azure AD-könyvtárból egy másikba, az erőforráshoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: **Előfizetés átvitele az Azure AD-könyvtárak között** [gyakori kérdésekben és az Azure-erőforrások felügyelt identitásaival kapcsolatos ismert problémák.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása az Azure Key Vaultban

Az ügyfél által felügyelt kulcsok engedélyezéséhez egy Azure Key Vault használatával kell tárolnia a kulcsokat. Engedélyeznie kell mind a **soft delete,** mind a **Ne ürítse** ki a kulcstartót.

A Cognitive Services titkosítása csak a 2048-as méretű RSA-kulcsokat támogatja. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

> [!NOTE]
> Ha a teljes kulcstartó törlődik, az adatok már nem jelennek meg, és az összes modell lesz undeployed. Minden feltöltött adat törlődik az egyéni fordítóból. 

### <a name="revoke-access-to-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShell vagy az Azure CLI használatát. További információ: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) vagy [Azure Key Vault CLI.](https://docs.microsoft.com/cli/azure/keyvault) A hozzáférés visszavonása hatékonyan blokkolja a hozzáférést a Cognitive Services-erőforrás összes adatához, és a modellek nem lesznek telepítve, mivel a titkosítási kulcs nem érhető el a Cognitive Services számára. Az összes feltöltött adat is törlődik az egyéni fordítóból.


## <a name="next-steps"></a>További lépések

* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
