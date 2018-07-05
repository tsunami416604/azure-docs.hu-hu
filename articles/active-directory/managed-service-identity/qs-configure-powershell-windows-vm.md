---
title: Az MSI konfigurálása Azure virtuális gép PowerShell-lel
description: Útmutató Azure virtuális gép, PowerShell-lel konfigurálja a Felügyeltszolgáltatás-identitás (MSI) lépésben.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 20bf16eeb6aff952423af6754812f9532e55cd5f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444455"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) PowerShell-lel

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>Ebből a cikkből elsajátíthatja az alábbi műveletek Felügyeltszolgáltatás-identitás-beli virtuális gépen, PowerShell-lel:
- 

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nem rendelkezik Azure-fiók [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a folytatás előtt.
- Telepítés [az Azure PowerShell legújabb verzióját](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette.

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt identitással Azure PowerShell-lel.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy Azure virtuális gép létrehozása során

Egy Azure virtuális gép létrehozása a rendszer a hozzárendelt identitás engedélyezve:

1. Tekintse meg a következő Azure virtuális gép gyors útmutatók elvégzése csak a szükséges szakaszok egyikét ("Jelentkezzen be az Azure-bA", "Create resource group", "Létrehozása a hálózati csoport", "A virtuális gép létrehozása").
    
    Ha az "Az VM létrehozása" szakaszban kap, a módosítások kisebb a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) parancsmag szintaxisát. Ügyeljen arra, hogy adjon hozzá egy `-AssignIdentity:$SystemAssigned` paramétert a virtuális gép a rendszerhez rendelt identitáshoz engedélyezve van, például:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Hozzon létre egy Windows virtuális gépet PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
   - [Hozzon létre egy Linux rendszerű virtuális gép PowerShell-lel](../../virtual-machines/linux/quick-create-powershell.md)

2. (Nem kötelező) Adja hozzá az MSI-VM bővítmény használatával a `-Type` paraméterrel a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy meglévő Azure virtuális gépen

Ha egy meglévő virtuális gépet a rendszer által hozzárendelt identitással engedélyeznie kell:

1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Login-AzureRmAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságainak használata a `Get-AzureRmVM` parancsmagot. Ezután a rendszer által hozzárendelt identitással engedélyezéséhez használja a `-AssignIdentity` váltani a [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Nem kötelező) Adja hozzá az MSI-VM bővítmény használatával a `-Type` paraméterrel a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Ügyeljen arra, hogy adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helye:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Tiltsa le a rendszer hozzárendelt identitás Azure virtuális gépből

> [!NOTE]
>  A Felügyeltszolgáltatás-identitást a virtuális gépről letiltása jelenleg nem támogatott. Addig is válthat a rendszer által hozzárendelt, és a felhasználó hozzárendelt identitások között.

Ha egy virtuális gépet, amely már nincs szüksége a rendszerhez rendelt identitáshoz, de továbbra is a felhasználó által hozzárendelt identitások van szüksége, használja a következő parancsmagot:

1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Login-AzureRmAccount
   ```

2. Futtassa a következő parancsmagot: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Az MSI-Virtuálisgép-bővítmény eltávolítása felhasználói a - Name kapcsolót a [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) parancsmagot, ugyanazt a bővítmény hozzáadásakor használt név megadásával:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá, és a egy a felhasználóhoz hozzárendelt identitás az Azure PowerShell-lel virtuális gép eltávolítása.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Rendelje hozzá egy a felhasználói identitás egy virtuális gép létrehozása során

A virtuális gép létrehozásakor egy felhasználóhoz hozzárendelt identitás hozzárendelése egy Azure virtuális Gépen:

1. Tekintse meg a következő Azure virtuális gép gyors útmutatók elvégzése csak a szükséges szakaszok egyikét ("Jelentkezzen be az Azure-bA", "Create resource group", "Létrehozása a hálózati csoport", "A virtuális gép létrehozása"). 
  
    Ha az "Az VM létrehozása" szakaszban kap, a módosítások kisebb a [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) parancsmag szintaxisát. Adja hozzá a `-IdentityType UserAssigned` és `-IdentityID ` a virtuális gép egy felhasználó által hozzárendelt identitással a paramétereket.  Cserélje le `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, és `<MSI NAME>` a saját értékeire.  Példa:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Hozzon létre egy Windows virtuális gépet PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
    - [Hozzon létre egy Linux rendszerű virtuális gép PowerShell-lel](../../virtual-machines/linux/quick-create-powershell.md)

2. (Nem kötelező) Adja hozzá az MSI-VM bővítmény használatával a `-Type` paraméterrel a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Ügyeljen arra, hogy adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helye:
      > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>A felhasználói identitás hozzárendelése egy meglévő Azure virtuális Gépen

Felhasználó hozzárendelése egy meglévő Azure virtuális géphez hozzárendelt identitás:

1. Jelentkezzen be Azure-bA `Connect-AzureRmAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot. Ügyeljen arra, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális Gépen, például a "Virtuális gép közreműködő":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Hozzon létre egy felhasználót hozzárendelt identitás használatával a [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) parancsmagot.  Megjegyzés: a `Id` a kimenetben, mert szüksége lesz a következő lépésben.

    > [!IMPORTANT]
    > A felhasználóhoz hozzárendelt identitás létrehozása csak alfanumerikus és kötőjel (0-9 vagy a – z vagy A – Z vagy a-) karaktert. Ezenkívül név legfeljebb 24 karakter hosszúságú VM/VMSS megfelelő működéséhez a hozzárendelés legyen. Biztonsági frissítések keresése. További információ: [– gyakori kérdések és ismert problémák](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Beolvasni a virtuális gép tulajdonságainak használata a `Get-AzureRmVM` parancsmagot. Majd szeretne hozzárendelni egy felhasználóhoz hozzárendelt identitás az Azure virtuális géphez, használja a `-IdentityType` és `-IdentityID` váltani a [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot.  Az érték a`-IdentityId` paraméter a `Id` az előző lépésben feljegyzett.  Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, és `<USER ASSIGNED IDENTITY NAME>` a saját értékeire.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Adja hozzá az MSI-VM bővítmény használatával a `-Type` paraméterrel a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helyét.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Távolítsa el a felhasználóhoz rendelt felügyelt identitás Azure virtuális gépből

> [!NOTE]
>  Az összes felhasználó által hozzárendelt identitások eltávolítása a virtuális gép jelenleg nem támogatott, kivéve, ha a rendszerhez rendelt identitáshoz. Biztonsági frissítések keresése.

Ha a virtuális gépen több felhasználó által hozzárendelt identitások, az alábbi parancsokkal az utolsót kivételével az összes eltávolíthatja. Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékeire. A `<MSI NAME>` továbbra is a virtuális gépen a felhasználóhoz hozzárendelt identitás name tulajdonság. Ez az információ található a virtuális Gépet az identitás szakaszában `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Ha a virtuális Géphez hozzárendelt rendszer és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználói hozzárendelt identitások között csak a rendszer által hozzárendelt használatára. Használja az alábbi parancsot:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Felügyeltszolgáltatás-identitás – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd:
  
  - [Windows virtuális gép létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 
