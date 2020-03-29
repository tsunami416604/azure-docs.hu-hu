---
title: Az Azure Portal használata az ügyfél által kezelt kulcsok konfigurálásához
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat az Azure Key Vault for Azure Storage titkosítással az Azure Portalon keresztül az Azure Portalon keresztül.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bef3b53a160f17248c1a26e97bc85a86843cb3c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061214"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával az Azure Portal használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vault ügyfél által felügyelt kulcsok at az [Azure Portalon.](https://portal.azure.com/) Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure Portalon, olvassa el a [rövid útmutató: Az Azure Key Vault titkos kulcsának beállítása és beolvasása az Azure Portalon.](../../key-vault/quick-create-portal.md)

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással történő használatához két tulajdonságot kell beállítani a key vaultban, a **Helyreállító törlés** és a **Ne ürítés szolgáltatást.** Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de egy új vagy meglévő kulcstartón a PowerShell vagy az Azure CLI használatával engedélyezhetők.

Ha tudni szeretné, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartóban, olvassa el a **"Helyreállítható törlés engedélyezése** és a **kiürítési védelem engedélyezése"** című szakaszokat az alábbi cikkek egyikében:

- [A soft-delete használata a PowerShell használatával.](../../key-vault/key-vault-soft-delete-powershell.md)
- [A soft-delete használata a CLI-vel.](../../key-vault/key-vault-soft-delete-cli.md)

Csak a 2048-as méretű RSA-kulcsok at támogatja az Azure Storage titkosítása. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

## <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése

Az ügyfél által felügyelt kulcsok engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. A tárfiók **Beállítások** paneljén kattintson a **Titkosítás gombra.** Válassza az **Ügyfél által kezelt kulcsok** lehetőséget, ahogy az az alábbi képen látható.

    ![A portál képernyőképe a titkosítási lehetőséggel](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Kulcs megadása

Miután engedélyezte az ügyfél által kezelt kulcsokat, megadhatja a tárfiókhoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

Kulcs URI-ként történő megadásához kövesse az alábbi lépéseket:

1. A kulcs URI-jának megkereséséhez az Azure Portalon keresse meg a key vaultot, és válassza a **Kulcsok** beállítást. Jelölje ki a kívánt kulcsot, majd kattintson a billentyűre a verzióinak megtekintéséhez. Válassza ki a kulcsverziót az adott verzió beállításainak megtekintéséhez.
1. Másolja az URI-t megadó **Kulcsazonosító** mező értékét.

    ![Képernyőkép a kulcstartó kulcsURI-jának megjelenítéséről](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. A **tárfiók titkosítási** beállításai ban válassza a **Kulcs URI megadása** lehetőséget.
1. Illessze be a Kulcs URI-mezőbe másolt **URI-t.**

   ![Képernyőkép a kulcs URI-jának beírásáról](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Adja meg a key vaultot tartalmazó előfizetést.
1. Mentse a módosításokat.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása a kulcstartóból

Kulcs megadása egy kulcstartóból, először győződjön meg arról, hogy rendelkezik egy kulcstartót tartalmazó. Kulcs megadása egy kulcstartóból, kövesse az alábbi lépéseket:

1. Válassza a **Kijelölés a Key Vaultból** lehetőséget.
1. Jelölje ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását ábrázoló képernyőkép](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Mentse a módosításokat.

## <a name="update-the-key-version"></a>A kulcsverzió frissítése

Amikor egy kulcs új verzióját hozza létre, frissítse a tárfiókot az új verzió használatához. Kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcsverzió URI-ját. Másik lehetőségként a kulcstartót és a kulcsot is kijelölheti a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="use-a-different-key"></a>Másik kulcs használata

Az Azure Storage titkosításához használt kulcs módosításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a tárfiókot, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-ját. Másik lehetőségként kiválaszthatja a key vaultot, és választhat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="disable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a tárfiók ismét titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által kezelt kulcsok letiltásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a tárfiókot, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a jelet a **Saját kulcsbeállítás** melletti jelölőnégyzetből.

## <a name="next-steps"></a>További lépések

- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md)
- [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
