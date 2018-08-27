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
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888556"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás az Azure portal használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és tilthatja le a rendszer és a felhasználóhoz hozzárendelt identitás számára egy Azure virtuális gép (VM), az Azure portal használatával. 

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ebben a cikkben a kezelési műveletek végrehajtásához a fióknak rendelkeznie kell a következő szerepkör-hozzárendelés:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) engedélyezéséhez, és távolítsa el a rendszer által hozzárendelt identitással Azure virtuális gépből.

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer-azonosító hozzárendelve virtuális géphez az Azure portal használatával.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy virtuális gép létrehozása során

Jelenleg az Azure portal nem támogatja a virtuális gépek létrehozása során lehetővé teszi a rendszer által hozzárendelt identitással. Ehelyett a következő virtuális gép létrehozása a rövid útmutató cikkek, először hozzon létre egy virtuális Géphez történő, és folytathatja a következő szakaszban engedélyezése a virtuális gépen a rendszer által hozzárendelt identitással kapcsolatos részletekért:

- [Windows virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy meglévő virtuális gépen

Ahhoz, hogy a rendszer hozzárendelt identitás eredetileg anélkül, hogy üzembe helyezett virtuális gépen:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.

2. Keresse meg a használni kívánt virtuális gépet, és válassza ki **identitás**.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **a** majd **mentése**:

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Távolítsa el a rendszerhez rendelt identitáshoz egy virtuális gépről

Ha egy virtuális gépet, amely már nincs szüksége a rendszer által hozzárendelt identitással rendelkezik:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva. 

2. Keresse meg a használni kívánt virtuális gépet, és válassza ki **identitás**.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **ki** majd **mentése**:

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

 Ebben a szakaszban megismerheti, hogyan adhat hozzá és távolíthat el egy felhasználót hozzárendelt identitás egy virtuális gépről az Azure portal használatával.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás egy virtuális gép létrehozása során

Jelenleg az Azure portal nem támogatja hozzárendelése egy a felhasználóhoz hozzárendelt identitás egy virtuális gép létrehozása során. Ehelyett tekintse meg a következő virtuális gép létrehozása a rövid útmutató cikkek, először hozzon létre egy virtuális gép közül, és folytathatja a következő szakasz egy a felhasználóhoz hozzárendelt identitás a virtuális géphez való hozzárendelésével kapcsolatos részletek:

- [Windows virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás meglévő virtuális géphez

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.
2. Keresse meg a kívánt virtuális Gépet, majd kattintson a **identitás**, **felhasználóhoz hozzárendelt** , majd  **\+Hozzáadás**.

   ![A felhasználóhoz hozzárendelt identitás hozzáadása virtuális Géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kattintson a felhasználóhoz hozzárendelt identitás hozzáadása a virtuális Géphez, és kattintson a kívánt **Hozzáadás**.

    ![A felhasználóhoz hozzárendelt identitás hozzáadása virtuális Géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Távolítsa el a a felhasználóhoz hozzárendelt identitás egy virtuális gépről

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.
2. Keresse meg a kívánt virtuális Gépet, majd kattintson a **identitás**, **felhasználóhoz hozzárendelt**, a felhasználóhoz hozzárendelt identitás törlése, és kattintson a kívánt nevére **eltávolítása** ( kattintson**Igen** a megerősítési panelen).

   ![Távolítsa el a felhasználóhoz hozzárendelt identitás egy virtuális gépről](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Kapcsolódó tartalom

- Felügyeltszolgáltatás-identitás áttekintését lásd: [áttekintése](overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure portal használatával egy Azure virtuális gépek Felügyeltszolgáltatás-identitást biztosíthat [egy másik Azure-erőforrásokhoz való hozzáférés](howto-assign-access-portal.md).

