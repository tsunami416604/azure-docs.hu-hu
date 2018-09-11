---
title: Konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen az Azure portal használatával
description: Részletes utasításokat a felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen az Azure portal használatával.
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
ms.openlocfilehash: f729a7972a36d53cb8f4f3769aedde4ca2005d80
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346127"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Az Azure-erőforrások felügyelt identitások konfigurálása a virtuális gép az Azure portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti vagy letilthatja az rendszer és a felhasználó által hozzárendelt felügyelt identitások számára egy Azure virtuális gép (VM), az Azure portal használatával. 

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:
    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) engedélyezéséhez, és távolítsa el a rendszer által hozzárendelt felügyelt identitás Azure virtuális gépből.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás virtuális géphez az Azure portal használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése a virtuális gépek létrehozása során

Jelenleg az Azure portal nem támogatja egy virtuális gép létrehozása során a rendszer által hozzárendelt identitás engedélyezése. Ehelyett a következő virtuális gép létrehozása a rövid útmutató cikkek, először hozzon létre egy virtuális Géphez történő, és folytathatja a következő szakaszban a virtuális Gépet a rendszer által hozzárendelt identitás engedélyezése részleteiért:

- [Windows virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>A meglévő virtuális gép felügyelt identitás alapértelmezett engedélyezése

A rendszer által hozzárendelt felügyelt identitás eredetileg anélkül, hogy üzembe helyezett virtuális gépek engedélyezése:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.

2. Keresse meg a használni kívánt virtuális gépet, és válassza ki **identitás**.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **a** majd **mentése**:

   ![Konfiguráció lap képernyőképe](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Távolítsa el a rendszer által hozzárendelt felügyelt identitás egy virtuális gépről

Ha egy virtuális gép, amely már nincs szüksége a rendszer által hozzárendelt felügyelt identitás:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva. 

2. Keresse meg a használni kívánt virtuális gépet, és válassza ki **identitás**.

3. Alatt **rendszerhez rendelt**, **állapot**, jelölje be **ki** majd **mentése**:

   ![Konfiguráció lap képernyőképe](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

 Ebben a szakaszban megismerheti, hogyan adhat hozzá, és a egy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuális Gépet az Azure portal használatával.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Egy felhasználó által hozzárendelt identitás hozzárendelése a virtuális gép létrehozása során

Jelenleg az Azure portal nem támogatja a felhasználó által hozzárendelt felügyelt identitás hozzárendelése a virtuális gép létrehozása során. Ehelyett a következő virtuális gép létrehozása a rövid útmutató cikkek, először hozzon létre egy virtuális Géphez történő, és folytathatja a következő szakasz egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése a virtuális gép részleteiért:

- [Windows virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő virtuális gép

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.
2. Keresse meg a kívánt virtuális Gépet, majd kattintson a **identitás**, **felhasználóhoz hozzárendelt** , majd  **\+Hozzáadás**.

   ![Felhasználó által hozzárendelt felügyelt identitás hozzáadása virtuális Géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kattintson a felhasználó által hozzárendelt identitás hozzáadása a virtuális Géphez, és kattintson a kívánt **Hozzáadás**.

    ![Felhasználó által hozzárendelt felügyelt identitás hozzáadása virtuális Géphez](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Távolítsa el a felhasználó által hozzárendelt felügyelt identitás egy virtuális gépről

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva.
2. Keresse meg a kívánt virtuális Gépet, majd kattintson **identitás**, **felhasználóhoz hozzárendelt**, nevét a felhasználó által hozzárendelt felügyelt identitás törlése, és kattintson a kívánt **eltávolítása** ( kattintson **Igen** a megerősítési panelen).

   ![Távolítsa el a felügyelt identitás felhasználó által hozzárendelt virtuális gép](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>További lépések

- Az Azure Portalon adjon egy Azure virtuális gép felügyelt identitás [egy másik Azure-erőforrásokhoz való hozzáférés](howto-assign-access-portal.md).

