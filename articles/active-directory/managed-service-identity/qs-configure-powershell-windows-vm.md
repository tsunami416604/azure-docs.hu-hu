---
title: MSI konfigurálása az Azure virtuális gép PowerShell használatával
description: Lépés által felügyelt szolgáltatás identitásának (MSI) konfigurálásához egy Azure virtuális gépen, PowerShell-lel részletes utasításokat.
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
ms.openlocfilehash: 6981c0f917fb7175f444ceca8c55c0df186774db
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) PowerShell használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>Ebből a cikkből megismerheti, hogyan egy Azure virtuális gépen, PowerShell használatával a következő felügyelt Szolgáltatásidentitás műveletek végrehajtásához:
- 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.
- Telepítés [Azure PowerShell legújabb verziójának](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette meg.

## <a name="system-assigned-identity"></a>A rendszer azonosító hozzárendelve

Meg ebben a szakaszban megtudhatja, hogyan engedélyezheti vagy letilthatja a hozzárendelt rendszeridentitás Azure PowerShell használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Identitás hozzárendelése az Azure virtuális gép létrehozása során rendszer engedélyezése

Egy Azure virtuális gép létrehozása a rendszer a hozzárendelt engedélyezve identitás:

1. A következő Azure virtuális gép Quickstarts, csak a szükséges szakaszok befejezése hivatkozik ("Jelentkezzen be az Azure", "Erőforráscsoport létrehozása", "Create hálózati csoport", "A virtuális gép létrehozása").
    
    Amikor a "Hozzon létre a virtuális gép" szakaszban, a módosítások enyhe a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) parancsmag szintaxisát. Vegye fel a `-AssignIdentity "SystemAssigned"` paramétert adja meg a virtuális Gépet a hozzárendelt rendszeridentitás engedélyezve van, például:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [A Windows PowerShell használatával virtuális gép létrehozása](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell-lel Linux virtuális gép létrehozása](../../virtual-machines/linux/quick-create-powershell.md)

2. (Választható) Az MSI-VM bővítmény használatával hozzáadása a `-Type` paraméter a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>A rendszer egy meglévő Azure virtuális gép identitásának hozzárendelt engedélyezése

Ha egy hozzárendelt rendszeridentitás egy meglévő virtuális gépen engedélyezni kell:

1. Jelentkezzen be az Azure használatával `Login-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Login-AzureRmAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságok a `Get-AzureRmVM` parancsmag. Ezután egy hozzárendelt rendszeridentitás engedélyezéséhez használja a `-AssignIdentity` váltani a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmagot:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Választható) Az MSI-VM bővítmény használatával hozzáadása a `-Type` paraméter a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port. Adja meg a megfelelő `-Location` paraméter, a meglévő virtuális gép helyét megfelelő:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>A rendszer az Azure virtuális gép identitásának hozzárendelt letiltása

> [!NOTE]
>  Szolgáltatásidentitás felügyelt virtuális gépről letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével.

Ha egy virtuális gép, amely már nincs szüksége a rendszer identitás hozzárendelt, de továbbra is hozzá kell a felhasználói identitások, használja a következő parancsmagot:

1. Jelentkezzen be az Azure használatával `Login-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Login-AzureRmAccount
   ```

2. Futtassa a következő parancsmagot: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Az MSI-Virtuálisgép-bővítmény eltávolítása felhasználói a - Name kapcsolót a [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) parancsmag, a bővítmény hozzáadásakor használt azonos név megadása:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>A felhasználói identitás

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása a felhasználóhoz rendelt identitás egy virtuális gép Azure PowerShell használatával.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Rendelje hozzá egy a felhasználói identitás egy virtuális gép létrehozása során

A virtuális gép létrehozásakor hozzárendelt felhasználói azonosítót hozzárendelése egy Azure virtuális Gépen:

1. A következő Azure virtuális gép Quickstarts, csak a szükséges szakaszok befejezése hivatkozik ("Jelentkezzen be az Azure", "Erőforráscsoport létrehozása", "Create hálózati csoport", "A virtuális gép létrehozása"). 
  
    Amikor a "Hozzon létre a virtuális gép" szakaszban, a módosítások enyhe a [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) parancsmag szintaxisát. Adja hozzá a `-IdentityType UserAssigned` és `-IdentityID ` paramétereit a virtuális Géphez hozzárendelt felhasználói azonosítót a kiépítéséhez.  Cserélje le `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, és `<MSI NAME>` saját értékekkel.  Példa:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [A Windows PowerShell használatával virtuális gép létrehozása](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell-lel Linux virtuális gép létrehozása](../../virtual-machines/linux/quick-create-powershell.md)

2. (Választható) Az MSI-VM bővítmény használatával hozzáadása a `-Type` paraméter a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port. Adja meg a megfelelő `-Location` paraméter, a meglévő virtuális gép helyét megfelelő:
      > [!NOTE]
    > Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Felhasználói azonosítót rendel egy meglévő Azure virtuális Gépen

Felhasználó hozzárendelése egy meglévő Azure virtuális gép identitásának rendelt:

1. Jelentkezzen be az Azure használatával `Connect-AzureRmAccount`. Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Hozzon létre egy felhasználó lehet hozzárendelve identitást használja a [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) parancsmag.  Megjegyzés: a `Id` kimenet mivel szüksége lesz a következő lépésben.

    > [!IMPORTANT]
    > A felhasználói identitások létrehozása csak alfanumerikus és kötőjel (0 – 9 vagy a-z vagy A-Z vagy -) karaktereket. Emellett nevét kell korlátozni a virtuális gép/VMSS helyes működéséhez hozzárendelés 24 karakter hosszúságot. Biztonsági frissítések ellenőrzése. További információ: [– gyakori kérdések és ismert problémák](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. A virtuális gép tulajdonságok beolvasása a `Get-AzureRmVM` parancsmag. Ezután az Azure virtuális Géphez hozzárendelt felhasználói azonosítót rendel, használja a `-IdentityType` és `-IdentityID` váltani a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmag.  Az érték a`-IdentityId` paraméter a `Id` az előző lépésben feljegyzett.  Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, és `<USER ASSIGNED IDENTITY NAME>` saját értékekkel.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Az MSI-VM bővítmény használatával hozzáadása a `-Type` paraméter a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) parancsmag. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", attól függően, hogy a típus, a virtuális gépet, és nevezze el a használatával a `-Name` paraméter. A `-Settings` paraméter határozza meg a jogkivonat beszerzése az OAuth-jogkivonat végpont által használt port. Adja meg a megfelelő `-Location` paraméter, a meglévő virtuális gép helye megfelelő.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Távolítsa el a felhasználóhoz rendelt felügyelt identitás egy Azure virtuális gépről

> [!NOTE]
>  Az összes hozzárendelt felhasználói azonosítók eltávolítása a virtuális gép jelenleg nem támogatott, kivéve, ha az identitás hozzárendelt rendszer. Biztonsági frissítések ellenőrzése.

Ha a virtuális gép több hozzárendelt felhasználói identitást, is távolítja el az alábbi parancsokkal legutóbb. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<MSI NAME>` marad, és a virtuális gép az a felhasználói identitás name tulajdonság. Ez az információ a virtuális gép használatával az identitási szakaszban találhatók `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Ha a virtuális Gépet a hozzárendelt rendszer és a felhasználói identitások is rendelkezik, eltávolíthatja a minden felhasználó hozzárendelt identitások váltásával hozzárendelt csak a rendszer. Használja az alábbi parancsot:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure virtuális gépek létrehozására – Gyorsútmutatók lásd:
  
  - [A Windows rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 
