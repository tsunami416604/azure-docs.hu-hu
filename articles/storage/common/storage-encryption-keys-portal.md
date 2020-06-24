---
title: Az ügyfél által felügyelt kulcsok konfigurálása a Azure Portal használatával
titleSuffix: Azure Storage
description: Ismerje meg, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat az Azure Storage-titkosításhoz Azure Key Vault az Azure Portal használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 30ef840e254355e7db34c626f482d86246e6a738
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805404"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault a Azure Portal használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal a [Azure Portal](https://portal.azure.com/)használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a Azure Portal használatával](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használata megköveteli, hogy két tulajdonság legyen beállítva a kulcstartóban, a **Soft delete** és a **not Purge**. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de a PowerShell vagy az Azure CLI használatával is engedélyezhető egy új vagy meglévő kulcstartón.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartón, tekintse meg a következő cikkek egyikében, a **Soft-delete engedélyezése** és a **kiürítési védelem engedélyezése** című szakaszt:

- [A Soft-delete használata a PowerShell-](../../key-vault/general/soft-delete-powershell.md)lel.
- [A Soft delete használata a parancssori](../../key-vault/general/soft-delete-cli.md)felülettel.

Az Azure Storage-titkosítás csak 2048 bites RSA-és RSA-HSM-kulcsokat támogat. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. A Storage-fiók **Beállítások** paneljén kattintson a **titkosítás**elemre. Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget, ahogy az az alábbi képen is látható.

    ![A titkosítási beállítást megjelenítő portál képernyőképe](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Kulcs meghatározása

Az ügyfél által felügyelt kulcsok engedélyezése után lehetősége van megadnia a Storage-fiókhoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a verziószámot az adott verzió beállításainak megtekintéséhez.
1. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. A Storage-fiók **titkosítási** beállításainál kattintson a **kulcs URI-ja megadása** lehetőségre.
1. Illessze be a vágólapra a **kulcs URI** mezőjébe MÁSOLt URI-t.

   ![A kulcs URI-azonosítójának megadását bemutató képernyőkép](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Itt adhatja meg a kulcstárolót tartalmazó előfizetést.
1. Mentse a módosításokat.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása kulcstartóból

A Key Vault kulcsának megadásához először győződjön meg arról, hogy rendelkezik egy kulcsot tartalmazó kulcstartóval. A Key vaultban lévő kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza a **kiválasztás a Key Vault** lehetőséget.
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a Key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását bemutató képernyőfelvétel](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Mentse a módosításokat.

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor frissítse a Storage-fiókot az új verzió használatára. Kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="use-a-different-key"></a>Másik kulcs használata

Az Azure Storage-titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a kulcstartót, és kiválaszthat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Storage-fiók újból titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által felügyelt kulcsok letiltásához kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a **saját kulcs használata** beállítás melletti jelölőnégyzet jelölését.

## <a name="next-steps"></a>További lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
