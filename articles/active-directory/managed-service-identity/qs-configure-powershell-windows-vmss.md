---
title: Felügyeltszolgáltatás-identitás konfigurálása az Azure VMSS PowerShell-lel
description: Részletes utasításokat a rendszer és a felhasználó rendelt identitásokkal az Azure VMSS a PowerShell használatával.
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
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257742"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>PowerShell-lel VMSS felügyelt Szolgáltatásidentitás konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ez a cikk ismerteti a Felügyeltszolgáltatás-identitás műveletek végrehajtása az Azure-beli virtuálisgép-méretezési csoportot, PowerShell-lel:
- Engedélyezheti vagy letilthatja a virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással
- Hozzáadhat és eltávolíthat a virtuálisgép-méretezési csoportot egy felhasználóhoz hozzárendelt identitás

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) felügyelt virtuálisgép-méretezési csoport létrehozása és engedélyezése, és távolítsa el a rendszer által hozzárendelt, és/vagy a virtuálisgép-méretezési csoportot a felhasználói identitás beállítása.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése, és távolítsa el a felhasználóhoz hozzárendelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.
- Telepítés [az Azure PowerShell legújabb verzióját](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette. 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és távolítsa el a rendszer által hozzárendelt identitással Azure PowerShell-lel.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt identitással engedélyezése egy Azure-beli virtuálisgép-méretezési készlet létrehozása során

A rendszer által hozzárendelt identitással engedélyezve van a VMSS létrehozása:

1. Tekintse meg *1. példa* a a [New-azurermvmssconfig parancsmaghoz](/powershell/module/azurerm.compute/new-azurermvmssconfig) referenciacikk parancsmag a VMSS létrehozásához a rendszerhez rendelt identitáshoz.  A paraméter hozzáadása `-IdentityType SystemAssigned` , a `New-AzureRmVmssConfig` parancsmagot:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Nem kötelező) Hozzáadás a Managed Service Identity VMSS bővítmény használatával a `-Name` és `-Type` paraméterrel a [Add-azurermvmssextension parancsmagban](/powershell/module/azurerm.compute/add-azurermvmssextension) parancsmagot. Átadható "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a típusától függően virtuálisgép-méretezési csoport állítsa be, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

    > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure-beli virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással engedélyezése

Ha egy rendszer által hozzárendelt identitással egy meglévő Azure-beli virtuálisgép-méretezési csoportot a engedélyeznie kell:

1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`. Az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot használnia. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép méretezési csoportot, például a "Virtuális gép közreműködő":

   ```powershell
   Login-AzureRmAccount
   ```

2. A virtuális gép méretezési csoport tulajdonságait a első lekérése a [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) parancsmagot. Ezután a rendszer által hozzárendelt identitással engedélyezéséhez használja a `-IdentityType` váltani a [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) parancsmagot:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Hozzáadás a Managed Service Identity VMSS bővítmény használatával a `-Name` és `-Type` paraméterrel a [Add-azurermvmssextension parancsmagban](/powershell/module/azurerm.compute/add-azurermvmssextension) parancsmagot. Átadható "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a típusától függően virtuálisgép-méretezési csoport állítsa be, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure-beli virtuálisgép-méretezési csoportot, a rendszer által hozzárendelt identitással

Ha egy virtuális gép méretezési csoportot, amely már nincs szüksége a rendszerhez rendelt identitáshoz, de továbbra is a felhasználó által hozzárendelt identitások van szüksége, használja a következő parancsmagot:

1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép méretezési csoportot, például a "Virtuális gép közreműködő":

2. Futtassa a következő parancsmagot:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Ha egy virtuális gép méretezési csoportot, amely a rendszerhez rendelt identitáshoz már nincs szüksége van, és nem a felhasználói identitásokat, használja a következő parancsokat:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása a a felhasználóhoz hozzárendelt identitás az Azure PowerShell használatával virtuálisgép-méretezési csoportot.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Egy felhasználóhoz hozzárendelt identitás hozzárendelése egy Azure-beli virtuálisgép-méretezési készlet létrehozása során

Egy új virtuálisgép-méretezési csoportot az egy felhasználóhoz hozzárendelt identitás létrehozása jelenleg nem támogatott Powershellen keresztül. Tekintse át a következő szakasz egy felhasználóhoz hozzárendelt identitás hozzáadása egy meglévő virtuálisgép-méretezési csoportot. Térjen vissza frissítésekért.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>A felhasználói identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoportot

Egy felhasználóhoz hozzárendelt identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoportot:

1. Jelentkezzen be Azure-bA `Connect-AzureRmAccount`. Az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot használnia. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép méretezési csoportot, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzureRmAccount
   ```

2. A virtuális gép méretezési csoport tulajdonságait a első lekérése a `Get-AzureRmVM` parancsmagot. Majd szeretne hozzárendelni egy felhasználóhoz hozzárendelt identitás a virtuálisgép-méretezési csoporthoz, használja a `-IdentityType` és `-IdentityID` váltani a [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) parancsmagot. Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` a saját értékeire.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Egy Azure-beli virtuálisgép-méretezési csoportot felügyelt identitás hozzárendelt felhasználó eltávolítása

Ha a virtuális gép méretezési több felhasználó által hozzárendelt identitások, az alábbi parancsokkal az utolsót kivételével az összes eltávolíthatja. Ne felejtse el a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterek értékeit a saját értékeire cserélni. A `<MSI NAME>` marad, és a virtuálisgép-méretezési csoportot a felhasználóhoz hozzárendelt identitás name tulajdonság. Ez az információ található a virtuálisgép-méretezési csoportot a identitás szakaszában `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Ha el szeretné távolítani az összes felhasználó hozzárendelt identitások, a virtuálisgép-méretezési készlet nem rendelkezik a rendszerhez rendelt identitáshoz, használja a következő parancsot:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Ha a virtuálisgép-méretezési csoporthoz rendelt system és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználói hozzárendelt identitások között csak a hozzárendelt rendszer használatára.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Felügyeltszolgáltatás-identitás – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd:
  
  - [Windows virtuális gép létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 

















