---
title: Felügyelt identitások konfigurálása az Azure VMSS - Azure AD szolgáltatásban
description: Lépésenkénti útmutató az Azure-erőforrások felügyelt identitások konfigurálásához egy virtuálisgép-méretezési csoportban az Azure Portalon.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32d8944b55794412eb322697469933030e2fbb56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184039"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Felügyelt identitások konfigurálása az Azure-erőforrásokhoz egy virtuálisgép-méretezési csoporton az Azure Portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveleteit egy virtuálisgép-méretezési csoporton:

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md)
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A jelen cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

    - [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához egy virtuálisgép-méretezési csoportból.

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást az Azure Portalhasználatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport létrehozása során

Jelenleg az Azure Portal nem támogatja a rendszer által hozzárendelt felügyelt identitás engedélyezése egy virtuálisgép-méretezési csoport létrehozása során. Ehelyett tekintse meg a következő virtuálisgép-méretezési csoport létrehozása rövid útmutató cikkelőször hozzon létre egy virtuálisgép-méretezési készlet, majd folytassa a következő szakaszban a rendszer által hozzárendelt felügyelt identitás engedélyezése a virtuálisgép-méretezési csoportban:

- [Virtuálisgép-méretezési csoport létrehozása az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése meglévő virtuálisgép-méretezési csoporton

A rendszer által hozzárendelt felügyelt identitás engedélyezése egy olyan virtuálisgép-méretezési csoporton, amely et eredetileg nélküle állították ki:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy, a virtuálisgép-méretezési készletet tartalmazó Azure-előfizetéshez társított fiók használatával.

2. Keresse meg a kívánt virtuálisgép-méretezési készletet.

3. A **Rendszer hozzárendelt** **területen**válassza a **Be** lehetőséget, majd kattintson a **Mentés**gombra:

   ![Konfigurációs lap képernyőképe](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési készletet rendelkezik, amelynek már nincs szüksége rendszeráltal hozzárendelt felügyelt identitásra:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy, a virtuálisgép-méretezési készletet tartalmazó Azure-előfizetéshez társított fiók használatával. Győződjön meg arról is, hogy a fiók egy olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép méretezési készletéhez.

2. Keresse meg a kívánt virtuálisgép-méretezési készletet.

3. A **Rendszer hozzárendelt** **területen**válassza a **Ki** lehetőséget, majd kattintson a **Mentés**gombra:

   ![Konfigurációs lap képernyőképe](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoportból az Azure Portal használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

Jelenleg az Azure Portal nem támogatja a felhasználó által hozzárendelt felügyelt identitás hozzárendelését egy virtuálisgép-méretezési csoport létrehozása során. Ehelyett tekintse meg a következő virtuálisgép-méretezési csoport létrehozása rövid útmutató cikket először hozzon létre egy virtuálisgép-méretezési készletet, majd folytassa a következő szakaszban a felhasználó által hozzárendelt felügyelt identitás hozzárendelésével kapcsolatos részletekért:

- [Virtuálisgép-méretezési csoport létrehozása az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő virtuálisgép-méretezési készlethez

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy, a virtuálisgép-méretezési készletet tartalmazó Azure-előfizetéshez társított fiók használatával.
2. Keresse meg a kívánt virtuálisgép-méretezési készletet, és kattintson **az Identitás**, **Hozzárendelt felhasználó** , majd ** \+a Hozzáadás parancsra.**

   ![Felhasználó által hozzárendelt identitás hozzáadása a VMSS-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kattintson a virtuálisgép-méretezési csoporthoz hozzáadni kívánt, felhasználó által hozzárendelt identitásra, majd kattintson a **Hozzáadás gombra.**
   
   ![Felhasználó által hozzárendelt identitás hozzáadása a VMSS-hez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportból

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a virtuális gép tartalmazza az Azure-előfizetéshez társított fiók használatával.
2. Keresse meg a kívánt virtuálisgép-méretezési csoportot, és kattintson az **Identitás**gombra, a **Felhasználó által hozzárendelt**gombra, a törölni kívánt, felhasználó által hozzárendelt felügyelt identitás nevére, majd kattintson az **Eltávolítás** gombra (a megerősítő ablaktáblában kattintson az **Igen** gombra).

   ![Felhasználó által hozzárendelt identitás eltávolítása vMSs-ből](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>További lépések

- Az Azure Portal használatával egy Azure virtuálisgép-méretezési csoport felügyelt identitás [hozzáférést biztosít egy másik Azure-erőforráshoz.](howto-assign-access-portal.md)


