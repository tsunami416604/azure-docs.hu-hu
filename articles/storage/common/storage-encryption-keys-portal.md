---
title: Ügyfél által felügyelt kulcsok az Azure Portalról az Azure Storage-titkosítás konfigurálása
description: Ismerje meg, hogyan ügyfél által felügyelt kulcsok az Azure Storage-titkosítás konfigurálása az Azure portal használatával. Ügyfél által felügyelt kulcsokat hozhat létre, elforgatása, tiltsa le, és visszavonhatja a hozzáférés-vezérlés lehetővé teszik.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154151"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Ügyfél által felügyelt kulcsok az Azure Portalról az Azure Storage-titkosítás konfigurálása

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a key vault-felhasználó által kezelt kulcsok használata a [az Azure portal](https://portal.azure.com/). Hozzon létre egy kulcstartót az Azure portal használatával kapcsolatban lásd: [a rövid útmutató: Beállítása és lekérése a titkos kulcs Azure Key vault az Azure portal használatával](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Felhasználó által kezelt kulcsok használata az Azure Storage-titkosítás megköveteli, hogy a key vault konfigurált, két kötelező tulajdonságok **a helyreállítható Törlés** és **tegye végleges törlés**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor egy új kulcstartót hoz létre az Azure Portalon. Azonban ha ezeket a tulajdonságokat a meglévő kulcstároló engedélyeznie kell, kell használnia PowerShell vagy az Azure parancssori felület.

## <a name="enable-customer-managed-keys"></a>Engedélyezze a felhasználó által kezelt kulcsok

Ahhoz, hogy az ügyfél által felügyelt kulcsok az Azure Portalon, kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. Az a **beállítások** a storage-fiókok panelen kattintson a **titkosítási**. Válassza ki a **a saját kulcs használata** beállítás, az alábbi ábrán látható módon.

    ![Portál képernyőképe a megjelenítő titkosítási beállítás](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Adja meg a kulcs

Miután engedélyezte a felhasználó által kezelt kulcsokkal, rendelkezni fog a lehetőséget, hogy adjon meg egy kulcsot a storage-fiókhoz társít.

### <a name="specify-a-key-as-a-uri"></a>Adja meg a kulcs URI-t

Adja meg a kulcs URI-t, kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg a kulcs URI-t, keresse meg a key vaultban, és válassza ki a **kulcsok** beállítás. Válassza ki a kívánt kulcsot, majd kattintson a beállítások megtekintéséhez a kulcsot. Másolja az értéket, a **azonosítója** mező, amely az URI-t biztosít.

    ![Képernyőkép bemutató. Ez a key vault-kulcshoz tartozó URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Az a **titkosítási** beállításait a tárfiókot, válassza ki a **Enter kulcshoz tartozó URI** lehetőséget.
1. Az a **kulcs URI-JÁT** mezőben adja meg az URI-t.

   ![Képernyőfelvétel: hogyan kell kulcshoz tartozó URI megadása](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Adja meg a key vault-kulcs

A key vault-kulcs megadásához, először győződjön meg arról, hogy a kulcsot tartalmazó kulcstartó. A key vault-kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza ki a **válassza ki a Key Vaultból** lehetőséget.
2. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartó.
3. Válassza ki a kulcsot a kulcstartóban található.

   ![Ügyfél által felügyelt kulcs lehetőséget ábrázoló képernyőfelvétel](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Frissítés a kulcs verziója

Amikor létrehoz egy kulcs új verziója, szüksége frissíteni a tárfiókot, hogy az új verziót használja. Kövesse az alábbi lépéseket:

1. Lépjen a tárfiókhoz, és megjeleníti a **titkosítási** beállításait.
1. Adja meg az URI-t, a kulcs új verziója. Azt is megteheti kiválaszthatja a kulcstartót és a kulcsot újra a verzióra frissíteni.

## <a name="next-steps"></a>További lépések

- [Inaktív adatok az Azure Storage-titkosítás](storage-service-encryption.md)
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
