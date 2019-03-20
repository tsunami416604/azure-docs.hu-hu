---
title: Felügyelt identitások az Azure-erőforrások a virtuálisgép-méretezési csoport konfigurálása PowerShell-lel
description: Részletes utasításokat a rendszer és felhasználó által hozzárendelt felügyelt identitások a virtuálisgép-méretezési csoportot állítsa be a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dee7759dccf3093e9ba9f66bffcceaf603a11d4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226878"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Az Azure-erőforrások felügyelt identitások konfigurálása a virtual machine scale sets PowerShell-lel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben a PowerShell-lel, mutatja a felügyelt identitásokból egy virtuálisgép-méretezési csoportot az Azure-erőforrások műveletek végrehajtásához:
- Engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot
- Hozzáadhat és eltávolíthat egy felhasználó által hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által felügyelt hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:

    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.

    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) készlet egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és a rendszer által hozzárendelt eltávolítása a felügyelt és/vagy felhasználó által hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) hozhat létre felhasználó által hozzárendelt szerepkör felügyelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és eltávolítása, felhasználó által hozzárendelt felügyelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.
- Telepítés [az Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) Ha még nem tette. 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti, és távolítsa el az Azure PowerShell-lel felügyelt rendszer által hozzárendelt identitás.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuálisgép-méretezési készlet létrehozása során

A felügyelt rendszer által hozzárendelt identitások engedélyezve van a virtuális gép méretezési csoport létrehozása:

1. Tekintse meg *1. példa* a a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag áttekintésével foglalkozó cikkben egy virtuálisgép-méretezési csoport létrehozása egy felügyelt rendszer által hozzárendelt identitással beállítása.  A paraméter hozzáadása `-IdentityType SystemAssigned` , a `New-AzVmssConfig` parancsmagot:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> A felügyelt identitásokból is üzembe is helyezheti, Azure-erőforrások virtuális gép méretezési csoport bővítményének, de azt hamarosan elavulttá válik. Hitelesítés az Azure Instance Metadata identitás végpont használatát javasoljuk. További információkért lásd: [állítsa le a VM-bővítmény használata és vegye használatba az Azure IMDS végpont hitelesítéshez](howto-migrate-vm-extension.md).


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure-beli virtuálisgép-méretezési csoportot a felügyelt identitás alapértelmezett engedélyezése

Ha egy rendszer által hozzárendelt felügyelt identitás egy meglévő Azure-beli virtuálisgép-méretezési csoportot engedélyeznie kell:

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot használnia. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép méretezési csoportot, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzAccount
   ```

2. A virtuális gép méretezési csoport tulajdonságait a első lekérése a [ `Get-AzVmss` ](/powershell/module/az.compute/get-azvmss) parancsmagot. Majd ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás, használja a `-IdentityType` váltani a [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmagot:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> A felügyelt identitásokból is üzembe is helyezheti, Azure-erőforrások virtuális gép méretezési csoport bővítményének, de azt hamarosan elavulttá válik. Hitelesítés az Azure Instance Metadata identitás végpont használatát javasoljuk. További információkért lásd: [áttelepítése a Virtuálisgép-bővítmény az Azure IMDS végpontra hitelesítéshez](howto-migrate-vm-extension.md).

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le a rendszer által hozzárendelt felügyelt identitás, az Azure-beli virtuálisgép-méretezési csoportot

Ha egy virtuális gép méretezési csoportot, amely már nincs szüksége a rendszer által hozzárendelt felügyelt identitást, de továbbra is a felügyelt identitásokból felhasználó által hozzárendelt van szüksége, használja a következő parancsmagot:

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép méretezési csoportot, például a "Virtuális gép közreműködő":

2. Futtassa a következő parancsmagot:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Ha egy virtuális gép méretezési csoportot, amely a rendszer által hozzárendelt felügyelt identitás már nincs szüksége van, és nem felügyelt felhasználó által hozzárendelt identitások rendelkezik, használja a következő parancsokat:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá, és a egy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoport Azure PowerShell használatával.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure-beli virtuálisgép-méretezési készlet létrehozása során

Egy felhasználó által hozzárendelt felügyelt identitással rendelkező új virtuális gép méretezési csoport létrehozása jelenleg nem támogatott Powershellen keresztül. Tekintse át a következő szakasz egy felhasználó által hozzárendelt felügyelt identitás hozzáadása egy meglévő virtuálisgép-méretezési csoportot. Térjen vissza frissítésekért.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoportot

Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoportot:

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot használnia. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép méretezési csoportot, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzAccount
   ```

2. A virtuális gép méretezési csoport tulajdonságait a első lekérése a `Get-AzVM` parancsmagot. Ezután a felhasználó által hozzárendelt felügyelt identitás hozzárendelni a virtuálisgép-méretezési csoportot, használja a `-IdentityType` és `-IdentityID` váltani a [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag. Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` a saját értékeire.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Egy Azure-beli virtuálisgép-méretezési csoportot egy felhasználó által hozzárendelt felügyelt identitás eltávolítása

Ha a virtuális gép méretezési több felhasználó által hozzárendelt felügyelt identitás, az alábbi parancsokkal az utolsót kivételével az összes eltávolíthatja. Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` marad, és a virtuálisgép-méretezési csoportot a felhasználó által hozzárendelt felügyelt identitáshoz tartozó name tulajdonság. Ez az információ található a virtuálisgép-méretezési csoportot a identitás szakaszában `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Ha a virtuálisgép-méretezési készlet nem rendelkezik egy rendszer által hozzárendelt felügyelt identitást, és szeretné távolítsa el az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Ha a virtuálisgép-méretezési rendelkezik, mindkét rendszer által hozzárendelt, és a felügyelt identitásokból felhasználó által hozzárendelt, eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitások egyetlen, a rendszer által hozzárendelt felügyelt identitás használatára vált át.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd:
  
  - [Windows virtuális gép létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 

















