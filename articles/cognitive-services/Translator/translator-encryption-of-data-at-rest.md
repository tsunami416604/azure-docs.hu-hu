---
title: Inaktív adatok Translator-titkosítása
titleSuffix: Azure Cognitive Services
description: Inaktív adatok Translator-titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372237"
---
# <a name="translator-encryption-of-data-at-rest"></a>Inaktív adatok Translator-titkosítása

A Translator automatikusan titkosítja az adatait, amelyeket feltölt az egyéni fordítási modellek létrehozásához, amikor a felhőbe kerül, és segít a szervezeti biztonsági és megfelelőségi célok teljesítésében.

## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról

Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Alapértelmezés szerint az adatai biztonságban vannak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás kihasználása érdekében.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Ha olyan díjszabási szintet használ, amely támogatja az ügyfél által felügyelt kulcsokat, a [Azure Portal](https://portal.azure.com) **titkosítási** szakaszában láthatja az erőforrás titkosítási beállításait az alábbi ábrán látható módon.

![Titkosítási beállítások megtekintése](../media/cognitive-services-encryption/encryptionblade.png)

Olyan előfizetésekhez, amelyek csak a Microsoft által felügyelt titkosítási kulcsokat támogatják, nem lesz **titkosítási** szakasza.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok Azure Key Vault

Lehetősége van arra is, hogy az előfizetését a saját kulcsaival kezelje. Az ügyfél által felügyelt kulcsok (CMK) – más néven saját kulcs használata (BYOK) – nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok a Translator szolgáltatás összes díjszabási szintjéhez elérhetők. Ha az ügyfél által felügyelt kulcsok használatát szeretné igénybe venni, töltse ki és küldje el a [fordító ügyfél által felügyelt kulcs kérése űrlapot](https://aka.ms/cogsvc-cmk) , amely körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK és a Translator Service használatát, létre kell hoznia egy új Translator-erőforrást. A fordítói erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

Az alábbi lépéseket követve engedélyezheti az ügyfél által felügyelt kulcsokat a fordító számára:

1. Hozza létre az új regionális Translator Text vagy regionális Cognitive Services erőforrást. Ez nem fog működni globális erőforrással.
2. Felügyelt identitás engedélyezése a Azure Portalban, és az ügyfél által felügyelt legfontosabb információk hozzáadása.
3. Hozzon létre egy új munkaterületet az egyéni fordítóban, és rendelje hozzá az előfizetési adatokat.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services erőforrásnak és a kulcstartónak ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Egy új Cognitive Services erőforrás mindig Microsoft által felügyelt kulcsokkal van titkosítva. Az ügyfél által felügyelt kulcsokat nem lehet engedélyezni az erőforrás létrehozásának időpontjában. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak a Cognitive Services erőforráshoz társított felügyelt identitásnak. A felügyelt identitás az erőforrás létrehozása után azonnal elérhető.

A következő témakörből megtudhatja, hogyan használhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault Cognitive Services titkosításhoz:

- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Cognitive Services titkosításhoz a Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Az ügyfél által felügyelt kulcsok engedélyezése lehetővé teszi a rendszerhez rendelt felügyelt identitások, az Azure AD funkciójának engedélyezését is. Ha engedélyezve van a rendszerhez rendelt felügyelt identitás, ez az erőforrás Azure Active Directory lesz regisztrálva. A regisztrálást követően a felügyelt identitás hozzáférést kap az ügyfél által felügyelt kulcs beállításakor kiválasztott Key Vaulthoz. További információ a [felügyelt identitásokról](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Ha letiltja a rendszerhez rendelt felügyelt identitásokat, a Key vaulthoz való hozzáférés el lesz távolítva, és az ügyfél kulcsaival titkosított összes adat már nem lesz elérhető. Az adatoktól függő funkciók nem fognak működni. Az üzembe helyezett összes modell is ki lesz telepítve. Az összes feltöltött adatok törlődnek az egyéni fordítóból. Ha a felügyelt identitások újra engedélyezve vannak, nem fogjuk automatikusan újra telepíteni a modellt.

> [!IMPORTANT]
> A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy az erőforrást egy Azure AD-címtárból egy másikba, az erőforráshoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása Azure Key Vault

Az ügyfél által felügyelt kulcsok engedélyezéséhez Azure Key Vault kell használnia a kulcsok tárolásához. Engedélyeznie kell a **Soft delete** és a No **Purge** tulajdonságot a Key vaulton.

Csak a 2048 méretű RSA-kulcsok támogatottak Cognitive Services titkosítással. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

> [!NOTE]
> Ha a teljes kulcstartó törölve lett, az adatai már nem jelennek meg, és az összes modell telepítése megmarad. Az összes feltöltött adatok törlődnek az egyéni fordítóból. 

### <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) vagy [Azure Key Vault parancssori](https://docs.microsoft.com/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Cognitive Services erőforrásban lévő összes adattal való hozzáférést, és a modelleket a rendszer nem telepíti, mivel a titkosítási kulcs nem érhető el Cognitive Services. Az összes feltöltött adatok törlődnek az egyéni fordítóból is.


## <a name="next-steps"></a>Következő lépések

* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
