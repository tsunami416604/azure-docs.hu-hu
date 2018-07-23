---
title: Egy PowerShell-lel az Azure VMSS MSI konfigurálása
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
ms.openlocfilehash: d5071a55c49a0749d91ec9617558ced76ebb007e
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188097"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurálja a VMSS Felügyeltszolgáltatás-identitás (MSI) PowerShell-lel

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ez a cikk ismerteti a Felügyeltszolgáltatás-identitás műveleteket a egy virtuális gép méretezési beállítása (VMSS), PowerShell-lel:
- Engedélyezheti és tilthatja le a rendszer az Azure VMSS identitásának hozzárendelve
- Hozzáadhat és eltávolíthat az Azure VMSS identitásának hozzárendelt felhasználó

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozása és engedélyezése, és távolítsa el a rendszer felügyelt identitás hozzárendelt virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése, és távolítsa el a felhasználóhoz hozzárendelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.
- Telepítés [az Azure PowerShell legújabb verzióját](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette. 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és távolítsa el a rendszer által hozzárendelt identitással Azure PowerShell-lel.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Engedélyezze a rendszerhez rendelt identitáshoz az Azure VMSS létrehozása során

A rendszer által hozzárendelt identitással engedélyezve van a VMSS létrehozása:

1. Tekintse meg *1. példa* a a [New-azurermvmssconfig parancsmaghoz](/powershell/module/azurerm.compute/new-azurermvmssconfig) referenciacikk parancsmag a VMSS létrehozásához a rendszerhez rendelt identitáshoz.  A paraméter hozzáadása `-IdentityType SystemAssigned` , a `New-AzureRmVmssConfig` parancsmagot:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Nem kötelező) Hozzáadás a MSI VMSS bővítmény használatával a `-Name` és `-Type` paraméterrel a [Add-azurermvmssextension parancsmagban](/powershell/module/azurerm.compute/add-azurermvmssextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

    > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>A rendszer egy meglévő Azure VMSS identitásának hozzárendelt engedélyezése

Ha egy rendszer által hozzárendelt identitással a egy meglévő Azure VMSS engedélyeznie kell:

1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Login-AzureRmAccount
   ```

2. Először kérjen le a VMSS-tulajdonságok használatával a [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) parancsmagot. Ezután a rendszer által hozzárendelt identitással engedélyezéséhez használja a `-IdentityType` váltani a [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Hozzáadás a MSI VMSS bővítmény használatával a `-Name` és `-Type` paraméterrel a [Add-azurermvmssextension parancsmagban](/powershell/module/azurerm.compute/add-azurermvmssextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>A rendszer az Azure VMSS hozzárendelt identitás letiltása

> [!NOTE]
> Felügyeltszolgáltatás-identitását egy virtuálisgép-méretezési csoportban a letiltás jelenleg nem támogatott. Addig is válthat a rendszer által hozzárendelt, és a felhasználó hozzárendelt identitások között.

Ha egy virtuálisgép-méretezési, már nincs szüksége a rendszerhez rendelt identitáshoz, de továbbra is a felhasználó által hozzárendelt identitások van szüksége, használja a következő parancsmagot:

1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

2. Futtassa a következő parancsmagot:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá és távolíthat el egy felhasználót egy Azure PowerShell-lel VMSS hozzárendelt identitás.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás létrehozása az Azure VMSS során

Egy új VMSS egy felhasználóhoz hozzárendelt identitás létrehozása jelenleg nem támogatott Powershellen keresztül. Tekintse át a következő szakaszban, a felhasználóhoz hozzárendelt identitás hozzáadása egy meglévő vmss-hez. Térjen vissza frissítésekért.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>A felhasználói identitás hozzárendelése egy meglévő Azure vmss-hez

Felhasználó hozzárendelése hozzárendelt identitás egy meglévő Azure vmss-hez:

1. Jelentkezzen be Azure-bA `Connect-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságainak használata a `Get-AzureRmVM` parancsmagot. Majd szeretne hozzárendelni egy felhasználóhoz hozzárendelt identitás az Azure vmss-hez, használja a `-IdentityType` és `-IdentityID` váltani a [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot. Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` a saját értékeire.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Az Azure VMSS identitás hozzárendelt felhasználó eltávolítása

> [!NOTE]
> Az összes felhasználó által hozzárendelt identitások eltávolítása egy virtuálisgép-méretezési csoportban jelenleg nem támogatott, kivéve, ha a rendszerhez rendelt identitáshoz. Térjen vissza frissítésekért.

Ha a VMSS több felhasználó által hozzárendelt identitások, az alábbi parancsokkal az utolsót kivételével az összes eltávolíthatja. Ne felejtse el a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterek értékeit a saját értékeire cserélni. A `<MSI NAME>` marad, és a VMSS a felhasználóhoz hozzárendelt identitás name tulajdonság. Ez az információ található a VMSS használatával identitás szakaszában `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Ha a VMSS hozzárendelt rendszer és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes a felhasználóhoz hozzárendelt identitások között használata csak a rendszer által hozzárendelt. Használja az alábbi parancsot:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Felügyeltszolgáltatás-identitás – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd:
  
  - [Windows virtuális gép létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 

















