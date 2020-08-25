---
title: Az ügyfél által felügyelt kulcsok konfigurálása a Azure Portal használatával
titleSuffix: Azure Storage
description: Ismerje meg, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat az Azure Storage-titkosításhoz Azure Key Vault az Azure Portal használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799169"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Ügyfél által kezelt kulcsok konfigurálása az Azure Key Vaulttal az Azure Portalon

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal a [Azure Portal](https://portal.azure.com/)használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a Azure Portal használatával](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használata megköveteli, hogy két tulajdonság legyen beállítva a kulcstartóban, a **Soft delete** és a **not Purge**. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de a PowerShell vagy az Azure CLI használatával is engedélyezhető egy új vagy meglévő kulcstartón.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartón, tekintse meg a következő cikkek egyikében, a **Soft-delete engedélyezése** és a **kiürítési védelem engedélyezése** című szakaszt:

- [A Soft-delete használata a PowerShell-](../../key-vault/general/soft-delete-powershell.md)lel.
- [A Soft delete használata a parancssori](../../key-vault/general/soft-delete-cli.md)felülettel.

Az Azure Storage encryption a 2048, 3072 és 4096 méretű RSA-és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. A Storage-fiók **Beállítások** paneljén kattintson a **titkosítás**elemre. Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget, ahogy az az alábbi képen is látható.

    ![A titkosítási beállítást megjelenítő portál képernyőképe](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Kulcs meghatározása

Az ügyfél által felügyelt kulcsok engedélyezése után lehetősége van megadnia a Storage-fiókhoz társítandó kulcsot. Azt is jelezheti, hogy az Azure Storage-nak automatikusan frissítenie kell-e az ügyfél által felügyelt kulcsot a legújabb verzióra, vagy manuálisan frissítenie kell a kulcs verzióját.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása kulcstartóból

Amikor kiválaszt egy ügyfél által felügyelt kulcsot egy kulcstartóból, a kulcs verziójának automatikus frissítése engedélyezve van. A kulcs verziójának manuális kezeléséhez adja meg helyette a kulcs URI-JÁT, és adja meg a kulcs verzióját. További információ: [kulcs megadása URI-ként](#specify-a-key-as-a-uri).

A Key vaultban lévő kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza a **kiválasztás a Key Vault** lehetőséget.
1. Válassza ki **a Key Vault és a kulcs kiválasztása**lehetőséget.
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a Key vaultból.

   ![A Key Vault és a kulcs kiválasztását bemutató képernyőkép](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Mentse a módosításokat.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

Az Azure Storage képes automatikusan frissíteni a titkosításhoz használt ügyfél által felügyelt kulcsot a legújabb kulcs verziójának használatára. Ha az ügyfél által felügyelt kulcsot Azure Key Vault elforgatják, az Azure Storage automatikusan a kulcs legújabb verzióját használja a titkosításhoz.

> [!NOTE]
> A kulcs elforgatásához hozza létre a kulcs új verzióját Azure Key Vault. Az Azure Storage nem kezeli a kulcs elforgatását Azure Key Vaultban, ezért manuálisan kell elforgatnia a kulcsot, vagy létre kell hoznia egy függvényt, amellyel elforgathatja azt ütemterv szerint.

Ha megadja a kulcs URI-JÁT, hagyja ki a kulcs verzióját az URI-ból, hogy engedélyezze az automatikus frissítést a legújabb verzióra. Ha a kulcs URI-JÁT is tartalmazza, akkor az automatikus frissítés nincs engedélyezve, és magát a kulcs verzióját kell kezelnie. A kulcs verziójának frissítésével kapcsolatos további információkért lásd [a kulcs verziójának manuális frissítését](#manually-update-the-key-version)ismertető témakört.

A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a verziószámot az adott verzió beállításainak megtekintéséhez.
1. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. A Storage-fiók **titkosítási kulcs** beállításainál válassza a **kulcs URI-azonosítójának megadása** lehetőséget.
1. Illessze be a vágólapra a **kulcs URI** mezőjébe MÁSOLt URI-t. Hagyja ki a kulcs verzióját az URI-ból, hogy engedélyezze a kulcs verziójának automatikus frissítését.

   ![A kulcs URI-azonosítójának megadását bemutató képernyőkép](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Itt adhatja meg a kulcstárolót tartalmazó előfizetést.
1. Mentse a módosításokat.

A kulcs megadása után a Azure Portal jelzi, hogy engedélyezve van-e a kulcs verziójának automatikus frissítése, és megjeleníti-e a titkosításhoz jelenleg használt kulcs verzióját.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Képernyőfelvétel, amely az engedélyezett kulcs verziójának automatikus frissítését mutatja":::

## <a name="manually-update-the-key-version"></a>A kulcs verziójának manuális frissítése

Alapértelmezés szerint, amikor a Key Vault az ügyfél által felügyelt kulcs új verzióját hozza létre, az Azure Storage automatikusan az új verziót használja az ügyfél által felügyelt kulcsokkal való titkosításhoz az előző szakaszokban leírtak szerint. Ha úgy dönt, hogy saját maga kezeli a kulcs verzióját, akkor a kulcs új verziójának létrehozásakor minden alkalommal frissítenie kell a Storage-fiókhoz társított kulcs verzióját.

Ha frissíteni szeretné a Storage-fiókot az új kulcs verziójának használatára, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg a kulcs új verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="switch-to-a-different-key"></a>Váltás másik kulcsra

Az Azure Storage-titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a kulcstartót, és kiválaszthat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Storage-fiók újból titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által felügyelt kulcsok letiltásához kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a **saját kulcs használata** beállítás melletti jelölőnégyzet jelölését.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
