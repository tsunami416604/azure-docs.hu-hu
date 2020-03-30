---
title: Felügyelt identitások konfigurálása az Azure Portalon – Azure AD
description: Lépésenkénti útmutató az Azure-erőforrások felügyelt identitások konfigurálása egy Azure-beli virtuális gép en az Azure Portalon.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266662"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Felügyelt identitások konfigurálása az Azure-erőforrásokhoz virtuális gépen az Azure Portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer- és a felhasználó által hozzárendelt felügyelt identitásokat egy Azure virtuális gép (VM) számára az Azure Portal használatával. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md)
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást a virtuális gép hez az Azure Portal használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése virtuális gép létrehozása során

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás a virtuális gépen létrehozása során, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

- Az **Identitás** csoport **Kezelés** lapján váltson **a Felügyelt szolgáltatás identitása** **kapcsolva be**.  

![Rendszeráltal hozzárendelt identitás engedélyezése a virtuális gép létrehozása során](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

A virtuális gép létrehozásához tekintse meg az alábbi rövid útmutatókat: 

- [Windows virtuális gép létrehozása az Azure Portallal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Linux virtuális gép létrehozása az Azure Portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő virtuális gépen

A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez egy virtuális gépen, amely eredetileg nélküle lett kiépítve, a fióknak szüksége van a [Virtuálisgép közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a virtuális gép tartalmazza az Azure-előfizetéshez társított fiók használatával.

2. Keresse meg a kívánt virtuális gépet, és válassza **az Identitás**lehetőséget.

3. A **Rendszer hozzárendelt** **területen**válassza a **Be** lehetőséget, majd kattintson a **Mentés**gombra:

   ![Konfigurációs lap képernyőképe](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>A rendszer által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről

A rendszer által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

Ha olyan virtuális gépe van, amelynek már nincs szüksége rendszeráltal hozzárendelt felügyelt identitásra:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a virtuális gép tartalmazza az Azure-előfizetéshez társított fiók használatával. 

2. Keresse meg a kívánt virtuális gépet, és válassza **az Identitás**lehetőséget.

3. A **Rendszer hozzárendelt** **területen**válassza a **Ki** lehetőséget, majd kattintson a **Mentés**gombra:

   ![Konfigurációs lap képernyőképe](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

 Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy virtuális gépről az Azure Portalhasználatával.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Felhasználó által hozzárendelt identitás hozzárendelése virtuális gép létrehozása során

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

Jelenleg az Azure Portal nem támogatja a felhasználó által hozzárendelt felügyelt identitás hozzárendelését a virtuális gép létrehozása során. Ehelyett tekintse meg az alábbi virtuális gép létrehozása rövid útmutató cikkek először hozzon létre egy virtuális gép, majd folytassa a következő szakaszban a részleteket a felhasználó által hozzárendelt felügyelt identitás a virtuális gép:

- [Windows virtuális gép létrehozása az Azure Portallal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux virtuális gép létrehozása az Azure Portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő virtuális géphez

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a virtuális gép tartalmazza az Azure-előfizetéshez társított fiók használatával.
2. Keresse meg a kívánt virtuális gép, és kattintson **identitás**, **felhasználó hozzárendelt,** majd ** \+hozzáadás.**

   ![Felhasználó által hozzárendelt felügyelt identitás hozzáadása a virtuális géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kattintson a virtuális géphez hozzáadni kívánt, felhasználó által hozzárendelt identitásra, majd kattintson a **Hozzáadás gombra.**

    ![Felhasználó által hozzárendelt felügyelt identitás hozzáadása a virtuális géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről

A felhasználó által hozzárendelt identitás eltávolítása a virtuális gép, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a virtuális gép tartalmazza az Azure-előfizetéshez társított fiók használatával.
2. Keresse meg a kívánt virtuális gépre, és kattintson az **Identitás**gombra, a **Felhasználó hozrendelt**gombra, a törölni kívánt, felhasználóáltal hozzárendelt felügyelt identitás nevére, majd kattintson az **Eltávolítás** gombra (a megerősítő ablaktáblában kattintson az **Igen** gombra).

   ![Felhasználó által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>További lépések

- Az Azure Portalhasználatával egy Azure-beli virtuális gép felügyelt [identitásának hozzáférést biztosít egy másik Azure-erőforráshoz.](howto-assign-access-portal.md)

