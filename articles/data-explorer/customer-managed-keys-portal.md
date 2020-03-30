---
title: Ügyfél által kezelt kulcsok konfigurálása az Azure Portalhasználatával
description: Ez a cikk ismerteti, hogyan konfigurálhatja az ügyfél által felügyelt kulcsok titkosítását az adatok az Azure Data Explorerben.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301043"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Ügyfél által kezelt kulcsok konfigurálása az Azure Portal használatával

> [!div class="op_single_selector"]
> * [Portál](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sablon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Titkosítás engedélyezése ügyféláltal kezelt kulcsokkal az Azure Portalon

Ez a cikk bemutatja, hogyan engedélyezheti az ügyfél által felügyelt kulcsok titkosítását az Azure Portalhasználatával. Alapértelmezés szerint az Azure Data Explorer titkosítás a Microsoft által felügyelt kulcsokat használja. Konfigurálja az Azure Data Explorer-fürtöt az ügyfél által kezelt kulcsok használatára, és adja meg a fürthöz társítandó kulcsot.

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg az [Azure Data Explorer fürterőforrás.](create-cluster-database-portal.md#create-a-cluster) 
1. Válassza a**Beállítások titkosítás** **lehetőséget** > a portál bal oldali ablaktáblájában.
1. A **Titkosítás** ablaktáblán válassza a **Be** lehetőséget az **Ügyfél által kezelt kulcs** beállításhoz.
1. Kattintson **a Kulcs kijelölése gombra.**

    ![Ügyfél által felügyelt kulcsok konfigurálása](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Az **Azure Key Vault ból kulcs kiválasztása** ablakban válasszon ki egy meglévő **kulcstartót** a legördülő listából. Ha [új kulcstartó létrehozásához](/azure/key-vault/quick-create-portal#create-a-vault)az **Új létrehozása** lehetőséget választja, a **kulcstartó létrehozása** képernyőre irányítja a fájlokat.

1. Válassza a **Kulcs**lehetőséget.
1. Válassza a **Verzió**lehetőséget.
1. Kattintson a **Kiválasztás** gombra.

    ![Kulcs kiválasztása az Azure Key Vaultból](media/customer-managed-keys-portal/cmk-key-vault.png)

1. A **Kulcsát** tartalmazó Titkosítás ablaktáblán válassza a **Mentés lehetőséget.** Amikor a CMK létrehozása sikeres, egy sikeres üzenet jelenik meg az **Értesítések**alkalmazásban.

    ![Ügyfél által kezelt kulcs mentése](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Az Azure Data Explorer-fürt ügyfél által felügyelt kulcsainak engedélyezésével létre fog létrehozni egy rendszerhez rendelt identitást a fürthöz, ha nem létezik. Emellett a kijelölt Key Vault-fürtön biztosítja a szükséges megtekintési engedélyeket az Azure Data Explorer-fürthöz, és lefogja szerezni a Key Vault-tulajdonságokat. 

> [!NOTE]
> Válassza **a Ki** lehetőséget, ha a létrehozás után el szeretné távolítani az ügyfél által kezelt kulcsot.

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Data Explorer-fürtök az Azure-ban](security.md)
* [Biztonságossá a fürt az Azure Data Explorer – Azure Portal](manage-cluster-security.md) in in-in titkosítás engedélyezésével.
* [Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)
* [Ügyfél által kezelt kulcsok konfigurálása C használatával #](customer-managed-keys-csharp.md)



