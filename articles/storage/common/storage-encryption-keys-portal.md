---
title: Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-titkosításhoz a Azure Portal
description: Ismerje meg, hogyan konfigurálhatja az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsokat a Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8ec6b1e90eb6638c99ca43715c5e8bea6e48c22
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030953"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-titkosításhoz a Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Key vaultot az ügyfél által felügyelt kulcsokkal a [Azure Portal](https://portal.azure.com/)használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával [, tekintse meg a gyors útmutató: A Azure Portal](../../key-vault/quick-create-portal.md)használatával állítson be és kérjen le egy titkos kulcsot a Azure Key Vault. 


> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használata megköveteli, hogy a Key Vault két szükséges tulajdonsággal rendelkezzen, a helyreállítható törléssel és a **kiürítéssel**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, akkor a PowerShellt vagy az Azure CLI-t kell használnia.
> Csak az RSA-kulcsok és a 2048-es kulcs mérete támogatott.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. A Storage-fiók **Beállítások** paneljén kattintson a **titkosítás**elemre. Válassza a **saját kulcs használata** lehetőséget, ahogy az az alábbi ábrán is látható.

    ![A titkosítási beállítást megjelenítő portál képernyőképe](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Kulcs meghatározása

Az ügyfél által felügyelt kulcsok engedélyezése után lehetősége van megadnia a Storage-fiókhoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a beállítások megtekintéséhez. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](media/storage-encryption-keys-portal/key-uri-portal.png)

1. A Storage-fiók **titkosítási** beállításainál kattintson a **kulcs URI-ja megadása** lehetőségre.
1. A **kulcs URI** mezőjében válassza ki az URI-t.

   ![A kulcs URI-azonosítójának megadását bemutató képernyőkép](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása kulcstartóból

A Key Vault kulcsának megadásához először győződjön meg arról, hogy rendelkezik egy kulcsot tartalmazó kulcstartóval. A Key vaultban lévő kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza a **kiválasztás a Key Vault** lehetőséget.
2. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
3. Válassza ki a kulcsot a Key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását bemutató képernyőfelvétel](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcsok új verziójának létrehozásakor frissítenie kell a Storage-fiókot az új verzió használatára. Kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Az új kulcs verziójának URI azonosítójának megadása. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.

## <a name="next-steps"></a>További lépések

- [Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md)
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
