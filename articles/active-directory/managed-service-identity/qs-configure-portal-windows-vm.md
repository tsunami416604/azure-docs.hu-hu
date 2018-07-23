---
title: Felügyeltszolgáltatás-identitás konfigurálása az Azure virtuális Gépekhez az Azure portal használatával
description: Lépés útmutató konfigurálásához a Felügyeltszolgáltatás-identitás-beli virtuális gépen az Azure portal használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 81aa8153198f69abd1722f97462927a0c242d4e7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186159"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás az Azure portal használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben, megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer hozzárendelt identitás egy Azure virtuális gép, az Azure portal használatával. Hozzárendelése és eltávolítása, felhasználó által hozzárendelt identitások az Azure virtuális gépekről jelenleg nem támogatott az Azure Portalon keresztül.

> [!NOTE]
> A felhasználóhoz hozzárendelt identitás műveletek jelenleg nem támogatottak az Azure Portalon keresztül. Térjen vissza frissítésekért. 

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ebben a cikkben a kezelési műveletek végrehajtásához a fióknak rendelkeznie kell a következő szerepkör-hozzárendelés:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) engedélyezéséhez, és távolítsa el a Felügyeltszolgáltatás-identitását egy Azure virtuális gépből.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Felügyeltszolgáltatás-identitás az Azure-beli virtuális gép létrehozása során

A virtuális gép létrehozása az Azure Portalon keresztül jelenleg nem támogatja a Felügyeltszolgáltatás-identitás műveletek. Ehelyett olvassa el a következő virtuális gép létrehozása a rövid útmutató cikkek, először hozzon létre egy virtuális gép közül:

- [Windows virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Majd folytassa a következő szakaszban részleteiért engedélyezi a Felügyeltszolgáltatás-identitást a virtuális gépen.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Engedélyezi a Felügyeltszolgáltatás-identitását egy meglévő Azure virtuális gépen

Ahhoz, hogy a rendszer hozzárendelt identitás eredetileg anélkül, hogy üzembe helyezett virtuális gépen:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.

2. Keresse meg a használni kívánt virtuális gépet, és válassza a "Konfiguráció" lapot.

3. A virtuális gépen a rendszer által hozzárendelt identitással engedélyezése "Igen" kiválasztásával "Managed service identity" alatt, és kattintson a **mentése**. Ez a művelet is igénybe vehet, 60 másodperc vagy több végrehajtásához:

   > [!NOTE]
   > Egy felhasználóhoz hozzárendelt identitás hozzáadása egy virtuális gép jelenleg nem támogatott az Azure Portalon keresztül.

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Távolítsa el a Felügyeltszolgáltatás-identitás az Azure virtuális gépből

Ha egy virtuális gépet, amely már nincs szüksége a rendszer által hozzárendelt identitással rendelkezik:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva. 

2. Keresse meg a használni kívánt virtuális gépet, és válassza a "Konfiguráció" lapot.

3. Tiltsa le a rendszer, ha a "Nem", "Managed service identity" alatt a virtuális gép identitásának hozzárendelte, majd kattintson a Mentés gombra. Ez a művelet is igénybe vehet, 60 másodperc vagy több végrehajtásához:

    > [!NOTE]
    > Egy felhasználóhoz hozzárendelt identitás hozzáadása egy virtuális gép jelenleg nem támogatott az Azure Portalon keresztül.

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Kapcsolódó tartalom

- Felügyeltszolgáltatás-identitás áttekintését lásd: [áttekintése](overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure portal használatával egy Azure virtuális gépek Felügyeltszolgáltatás-identitást biztosíthat [egy másik Azure-erőforrásokhoz való hozzáférés](howto-assign-access-portal.md).

