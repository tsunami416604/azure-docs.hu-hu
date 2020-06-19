---
title: Inaktív adatok Language Understanding szolgáltatás titkosítása
titleSuffix: Azure Cognitive Services
description: Az inaktív adatok Language Understanding szolgáltatás titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 4fc816c3894120a5d1b356d91ebebbc56f21b530
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052690"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Inaktív adatok Language Understanding szolgáltatás titkosítása

A Language Understanding szolgáltatás automatikusan titkosítja az adatait, amikor a felhőben is megmarad. A Language Understanding szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról

Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Alapértelmezés szerint az adatai biztonságban vannak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás kihasználása érdekében.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Lehetősége van arra is, hogy az előfizetését a saját kulcsaival kezelje. Az ügyfél által felügyelt kulcsok (CMK-EK) nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Felhasználó által kezelt kulcsok az Azure Key Vaulttal

Lehetősége van arra is, hogy az előfizetését a saját kulcsaival kezelje. Az ügyfél által felügyelt kulcsok (CMK) – más néven saját kulcs használata (BYOK) – nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services erőforrásnak és a kulcstartónak ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>Ügyfél által felügyelt kulcsok Language Understanding

Az ügyfél által felügyelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Luis szolgáltatás ügyfél által felügyelt kulcs kérése űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK-val a LUIS-t, létre kell hoznia egy új Language Understanding-erőforrást a Azure Portal, és a E0 kell választania. Az új SKU ugyanúgy fog működni, mint a F0 SKU, amely a CMK kivételével már elérhető. A felhasználók nem fognak tudni frissíteni a F0 az új E0 SKU-ra.

![LUIS-előfizetési rendszerkép](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok minden [szerzői régióban](luis-reference-regions.md)elérhetők. 

### <a name="limitations"></a>Korlátozások

A meglévő/korábban létrehozott alkalmazásokkal a E0 szinten vannak korlátozások:

* A rendszer letiltja a E0-erőforrásba való áttelepítést. A felhasználók csak F0-erőforrásokra telepíthetik alkalmazásaikat. Miután áttelepített egy meglévő erőforrást a F0-ra, létrehozhat egy új erőforrást a E0 szinten. További információ az [áttelepítésről](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Az alkalmazások E0-erőforrásba vagy onnan történő áthelyezése le lesz tiltva. A korlátozásnak megfelelő megoldás a meglévő alkalmazás exportálása, és E0-erőforrásként való importálása.
* A Bing Spell Check funkció nem támogatott.
* A végfelhasználói forgalom naplózása le van tiltva, ha az alkalmazás E0.
* Az Azure bot Service-ből származó Speech alapozó képesség nem támogatott a E0 szinten lévő alkalmazások esetében. Ez a funkció a Azure Bot Serviceon keresztül érhető el, amely nem támogatja a CMK.
* A portál beszédfelismerési funkciójának használatához Azure Blob Storage szükséges. További információ: [saját tároló](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)használata.

### <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

Egy új Cognitive Services erőforrás mindig Microsoft által felügyelt kulcsokkal van titkosítva. Az ügyfél által felügyelt kulcsokat nem lehet engedélyezni az erőforrás létrehozásának időpontjában. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak a Cognitive Services erőforráshoz társított felügyelt identitásnak. A felügyelt identitás csak akkor érhető el, ha az erőforrást a CMK díjszabási szintje alapján hozták létre.

A következő témakörből megtudhatja, hogyan használhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault Cognitive Services titkosításhoz:

- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Cognitive Services titkosításhoz a Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Az ügyfél által felügyelt kulcsok engedélyezése lehetővé teszi a rendszerhez rendelt felügyelt identitások, az Azure AD funkciójának engedélyezését is. Ha engedélyezve van a rendszerhez rendelt felügyelt identitás, ez az erőforrás Azure Active Directory lesz regisztrálva. A regisztrálást követően a felügyelt identitás hozzáférést kap az ügyfél által felügyelt kulcs beállításakor kiválasztott Key Vaulthoz. További információ a [felügyelt identitásokról](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Ha letiltja a rendszerhez rendelt felügyelt identitásokat, a Key vaulthoz való hozzáférés el lesz távolítva, és az ügyfél kulcsaival titkosított összes adat már nem lesz elérhető. Az adatoktól függő funkciók nem fognak működni.

> [!IMPORTANT]
> A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy az erőforrást egy Azure AD-címtárból egy másikba, az erőforráshoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása Azure Key Vault

Az ügyfél által felügyelt kulcsok engedélyezéséhez Azure Key Vault kell használnia a kulcsok tárolásához. Engedélyeznie kell a **Soft delete** és a No **Purge** tulajdonságot a Key vaulton.

Csak a 2048 méretű RSA-kulcsok támogatottak Cognitive Services titkosítással. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

### <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell a Cognitive Services erőforrást az új kulcs URI-azonosítójának használatához. Ha meg szeretné tudni, hogyan frissítheti az erőforrást úgy, hogy a kulcs új verzióját használja a Azure Portalban, tekintse meg a következő témakört: **az** [ügyfél által felügyelt kulcsok konfigurálása a Cognitive Serviceshoz a Azure Portal használatával](../Encryption/cognitive-services-encryption-keys-portal.md).

A kulcs elforgatása nem aktiválja újra az erőforrásban lévő adattitkosítást. Nincs szükség további műveletre a felhasználótól.

### <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) vagy [Azure Key Vault parancssori](https://docs.microsoft.com/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Cognitive Services erőforrásban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs Cognitive Services nem érhető el.

## <a name="next-steps"></a>Következő lépések

* [LUIS szolgáltatás ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
