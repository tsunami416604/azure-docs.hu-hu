---
title: Konfigurálása felügyelt identitások egy virtuálisgép-méretezési csoportot az Azure-erőforrások
description: Részletes utasításokat a felügyelt identitások az Azure-erőforrások egy virtuális gép méretezési csoportban az Azure portal használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: priyamo
ms.openlocfilehash: 620f3f4568b2f73452e5e5293fc53bcd8da3efbc
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887308"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Felügyelt identitások az Azure-erőforrások konfigurálása egy virtuális gép méretezési csoportban az Azure portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben PowerShell-lel, megismerheti, hogyan hajthat végre a következő felügyelt identitások egy virtuálisgép-méretezési csoportot az Azure-erőforrások műveletek:

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:

    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.

    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) engedélyezése és a rendszer által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportot.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le az Azure portal használatával felügyelt rendszer által hozzárendelt identitások lesz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy virtuálisgép-méretezési készlet létrehozása során

Jelenleg az Azure portal nem támogatja a felügyelt identitás alapértelmezett engedélyezése egy virtuálisgép-méretezési készlet létrehozása során. Ehelyett tekintse meg a következő virtuálisgép-méretezési készlet létrehozási rövid útmutató cikket, először hozzon létre egy virtuálisgép-méretezési csoportot, és folytathatja a következő szakasszal egy virtuálisgép-méretezési csoportot a felügyelt identitás alapértelmezett engedélyezése részleteiért:

- [Hozzon létre egy virtuálisgép-méretezési csoportban az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Egy meglévő virtuálisgép-méretezési csoportot a felügyelt identitás alapértelmezett engedélyezése

A rendszer által hozzárendelt felügyelt identitás eredetileg anélkül, hogy üzembe helyezett virtuális gép méretezési engedélyezése:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal.

2. Keresse meg a kívánt virtuálisgép-méretezési csoportot.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **a** majd **mentése**:

   ![Konfiguráció lap képernyőképe](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Felügyelt identitás alapértelmezett eltávolítása egy virtuálisgép-méretezési csoportot

Ha egy virtuális gép méretezési csoportot, amely egy rendszer által hozzárendelt felügyelt identitás már nincs szüksége van:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal. Ügyeljen arra, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi a virtuálisgép-méretezési csoportot írási engedéllyel.

2. Keresse meg a kívánt virtuálisgép-méretezési csoportot.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **ki** majd **mentése**:

   ![Konfiguráció lap képernyőképe](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá, és a egy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportba, az Azure portal használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy virtuálisgép-méretezési készlet létrehozása során

Jelenleg az Azure portal nem támogatja a felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy virtuálisgép-méretezési készlet létrehozása során. Ehelyett tekintse meg a következő virtuálisgép-méretezési készlet létrehozási rövid útmutató cikket, először hozzon létre egy virtuálisgép-méretezési csoportot, és folytathatja a következő szakasszal egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése részleteiért:

- [Hozzon létre egy virtuálisgép-méretezési csoportban az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő virtuálisgép-méretezési csoportot

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal.
2. Keresse meg a kívánt virtuálisgép-méretezési csoportot, és kattintson a **identitás**, **felhasználóhoz hozzárendelt** , majd  **\+Hozzáadás**.

   ![Adja hozzá a felhasználó által hozzárendelt identitás vmss-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kattintson a felhasználó által hozzárendelt identitását, adja hozzá a virtuálisgép-méretezési csoporthoz, és kattintson a kívánt **Hozzáadás**.
   
   ![Adja hozzá a felhasználó által hozzárendelt identitás vmss-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportot egy felhasználó által hozzárendelt felügyelt identitás eltávolítása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.
2. Keresse meg a kívánt virtuálisgép-méretezési csoportot, és kattintson a **identitás**, **felhasználóhoz hozzárendelt**, nevét a felhasználó által hozzárendelt felügyelt identitás törlése, és kattintson a kívánt **eltávolítása** (kattintson **Igen** a megerősítési panelen).

   ![Távolítsa el a felhasználó által hozzárendelt identitás-ből](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>További lépések

- Az Azure Portallal, adja meg az Azure virtuálisgép-méretezési csoport felügyelt identitás beállításához [egy másik Azure-erőforrásokhoz való hozzáférés](howto-assign-access-portal.md).


