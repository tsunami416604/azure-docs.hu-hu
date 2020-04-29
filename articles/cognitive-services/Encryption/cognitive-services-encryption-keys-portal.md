---
title: Az ügyfél által felügyelt kulcsok konfigurálása a Azure Portal használatával
titleSuffix: Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault segítségével a Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455257"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault a Azure Portal használatával

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services erőforrásnak és a kulcstartónak ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal a [Azure Portal](https://portal.azure.com/)használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a Azure Portal használatával](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok használata megköveteli, hogy a Key vaulton két tulajdonság legyen beállítva, a **Soft delete** és a **not Purge**. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de a PowerShell vagy az Azure CLI használatával is engedélyezhető egy új vagy meglévő kulcstartón.

> [!IMPORTANT]
> Ha nem rendelkezik a helyreállítható **Törlés** és a kitakarítási **Tulajdonságok lehetőséggel, és törli** a kulcsot, nem fogja tudni helyreállítani a kognitív szolgáltatás erőforrásában lévő adatokat.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartón, tekintse meg a következő cikkek egyikében, a **Soft-delete engedélyezése** és a **kiürítési védelem engedélyezése** című szakaszt:

- [A Soft-delete használata a PowerShell-](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)lel.
- [A Soft delete használata a parancssori](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)felülettel.

Az Azure Storage-titkosítás csak a 2048 méretű RSA-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services-erőforráshoz.
1. A Cognitive Services erőforrás **Beállítások** paneljén kattintson a **titkosítás**elemre. Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget az alábbi ábrán látható módon.

    ![Az ügyfél által felügyelt kulcsok kijelölését bemutató képernyőkép](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Kulcs meghatározása

Miután engedélyezte az ügyfél által felügyelt kulcsokat, lehetősége lesz megadnia a Cognitive Services erőforráshoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a verziószámot az adott verzió beállításainak megtekintéséhez.
1. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](../media/cognitive-services-encryption/key-uri-portal.png)

1. A Storage-fiók **titkosítási** beállításainál kattintson a **kulcs URI-ja megadása** lehetőségre.
1. Illessze be a vágólapra a **kulcs URI** mezőjébe MÁSOLt URI-t.

   ![A kulcs URI-azonosítójának megadását bemutató képernyőkép](../media/cognitive-services-encryption/ssecmk2.png)

1. Itt adhatja meg a kulcstárolót tartalmazó előfizetést.
1. Mentse a módosításokat.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása kulcstartóból

A Key Vault kulcsának megadásához először győződjön meg arról, hogy rendelkezik egy kulcsot tartalmazó kulcstartóval. A Key vaultban lévő kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza a **kiválasztás a Key Vault** lehetőséget.
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a Key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását bemutató képernyőfelvétel](../media/cognitive-services-encryption/ssecmk3.png)

1. Mentse a módosításokat.

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor frissítse a Cognitive Services erőforrást az új verzió használatára. Kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="use-a-different-key"></a>Másik kulcs használata

A titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a kulcstartót, és kiválaszthat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltotta az ügyfél által felügyelt kulcsokat, akkor a Cognitive Services-erőforrást a Microsoft által felügyelt kulcsokkal titkosítja. Az ügyfél által felügyelt kulcsok letiltásához kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a **saját kulcs használata** beállítás melletti jelölőnégyzet jelölését.

## <a name="next-steps"></a>További lépések

* [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [Szembenéző szolgáltatások titkosítása inaktív adatok esetén](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker inaktív adatok titkosítása](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Inaktív adatok Language Understanding szolgáltatás titkosítása](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator inaktív adatok titkosítása](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Inaktív adatok Translator-titkosítása](../translator/translator-encryption-of-data-at-rest.md)
* [A REST-adatok személyre szabása](../personalizer/personalizer-encryption-of-data-at-rest.md)
