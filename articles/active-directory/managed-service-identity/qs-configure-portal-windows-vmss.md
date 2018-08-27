---
title: Felügyeltszolgáltatás-identitás konfigurálása egy Azure-beli virtuálisgép-méretezési csoportot, az Azure portal használatával
description: Útmutató az Azure VMSS, az Azure portal használatával konfigurálja a Felügyeltszolgáltatás-identitás lépés.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887989"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>A virtuális gép konfigurálása méretezési Felügyeltszolgáltatás-identitás az Azure portal használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és tilthatja le a rendszer és a felhasználóhoz hozzárendelt identitás méretezési csoportokhoz tartozó virtuális gépek az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ebben a cikkben a kezelési műveletek végrehajtásához a fióknak rendelkeznie kell a következő szerepkör-hozzárendelés:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) engedélyezéséhez, és távolítsa el a rendszer felügyelt identitás hozzárendelt virtuálisgép-méretezési csoportot.

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz 

Ebben a szakaszban, megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer hozzárendelt identitás egy virtuális gép méretezési csoportban az Azure portal használatával.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt identitással engedélyezése egy virtuálisgép-méretezési készlet létrehozása során

Az Azure portal jelenleg nem támogatja a engedélyezésével rendszer hozzárendelt identitás egy virtuálisgép-méretezési készlet létrehozása során. Ehelyett az alábbi virtuális gép méretezési készlet létrehozása a rövid útmutató cikkben először hozzon létre egy virtuálisgép-méretezési csoportot, és folytathatja a következő szakaszra a rendszerhez rendelt identitáshoz egy virtuálisgép-méretezési csoportot a engedélyezésével a részletekért tekintse meg:

- [Hozzon létre egy virtuálisgép-méretezési csoportban az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Egy meglévő virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással engedélyezése

Egy virtuális gép méretezési csoportban, amely eredetileg kiosztott anélkül, hogy a rendszer által hozzárendelt identitással engedélyezése:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal.

2. Keresse meg a kívánt virtuálisgép-méretezési csoportot.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **a** majd **mentése**:

   [![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>A virtuális gép méretezési távolítsa el a rendszer által hozzárendelt identitással

Ha egy virtuálisgép-méretezési csoportban, már nem kell a rendszerhez rendelt identitáshoz:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal. Ügyeljen arra, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi a virtuálisgép-méretezési csoportot írási engedéllyel.

2. Keresse meg a kívánt virtuálisgép-méretezési csoportot.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **ki** majd **mentése**:

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása a a felhasználóhoz hozzárendelt identitás egy virtuális gép méretezési csoportból az Azure portal használatával.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Rendelje hozzá egy felhasználóhoz hozzárendelt identitás egy virtuálisgép-méretezési készlet létrehozása során

Jelenleg az Azure portal nem támogatja hozzárendelése egy a felhasználóhoz hozzárendelt identitás egy virtuálisgép-méretezési készlet létrehozása során. Ehelyett az alábbi virtuális gép méretezési készlet létrehozása a rövid útmutató cikkben először hozzon létre egy virtuálisgép-méretezési csoportot, és folytathatja a következő szakasszal egy a felhasználóhoz hozzárendelt identitás ahhoz való hozzárendelésével kapcsolatos részletekért tekintse meg:

- [Hozzon létre egy virtuálisgép-méretezési csoportban az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Egy felhasználóhoz hozzárendelt identitás hozzárendelése egy meglévő virtuálisgép-méretezési csoportot

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal.
2. Keresse meg a kívánt virtuálisgép-méretezési csoportot, és kattintson a **identitás**, **felhasználóhoz hozzárendelt** , majd  **\+Hozzáadás**.

   ![Adja hozzá a felhasználóhoz hozzárendelt identitás vmss-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kattintson a felhasználóhoz hozzárendelt identitás adja hozzá a virtuálisgép-méretezési csoporthoz, és kattintson a kívánt **Hozzáadás**.
   
   ![Adja hozzá a felhasználóhoz hozzárendelt identitás vmss-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportot egy felhasználóhoz hozzárendelt identitás eltávolítása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.
2. Keresse meg a kívánt virtuálisgép-méretezési csoportot, és kattintson a **identitás**, **felhasználóhoz hozzárendelt**, a felhasználóhoz hozzárendelt identitás törlése, és kattintson a kívánt nevére **eltávolítása** () Kattintson a **Igen** a megerősítési panelen).

   ![Távolítsa el a felhasználóhoz hozzárendelt identitás-ből](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Kapcsolódó tartalom

- Felügyeltszolgáltatás-identitás áttekintését lásd: [áttekintése](overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure Portallal, adja meg az Azure virtuálisgép-méretezési csoport beállítása Felügyeltszolgáltatás-identitás [egy másik Azure-erőforrásokhoz való hozzáférés](howto-assign-access-portal.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
