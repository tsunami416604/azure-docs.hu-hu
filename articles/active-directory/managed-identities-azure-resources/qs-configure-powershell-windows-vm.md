---
title: Felügyelt identitások az Azure-erőforrások konfigurálása az Azure virtuális gép PowerShell-lel
description: Részletes utasításokat a felügyelt identitások az Azure-erőforrások PowerShell-lel,-beli virtuális gépen.
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
ms.openlocfilehash: 57d1ff4b44ff352742ee91b61c0c774cfe7c3f9d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181354"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Felügyelt identitások az Azure-erőforrások konfigurálása az Azure virtuális gép PowerShell-lel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben PowerShell-lel, megismerheti, hogyan hajthat végre a következő felügyelt identitások az Azure-erőforrások operations-beli virtuális gépen.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítés [az Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) Ha még nem tette.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le az Azure PowerShell-lel felügyelt rendszer által hozzárendelt identitások lesz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure virtuális gép létrehozása során

Egy Azure virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással engedélyezve van, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Tekintse meg a következő Azure virtuális gép gyors útmutatók elvégzése csak a szükséges szakaszok egyikét ("Jelentkezzen be az Azure-bA", "Create resource group", "Létrehozása a hálózati csoport", "A virtuális gép létrehozása").
    
    Ha az "Az VM létrehozása" szakaszban kap, a módosítások kisebb a [New-AzVMConfig](/powershell/module/az.compute/new-azvm) parancsmag szintaxisát. Ügyeljen arra, hogy adjon hozzá egy `-AssignIdentity:$SystemAssigned` paramétert a virtuális gép a rendszer által hozzárendelt identitással engedélyezve van, például:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Hozzon létre egy Windows virtuális gépet PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
   - [Hozzon létre egy Linux rendszerű virtuális gép PowerShell-lel](../../virtual-machines/linux/quick-create-powershell.md)

2. (Nem kötelező) Adja hozzá a felügyelt identitások az Azure-erőforrások virtuális gép (elavult. január 2019 a tervezett) bővítmény használatával a `-Type` paraméterrel a [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok. A felügyelt identitások Azure-erőforrások Virtuálisgép-bővítmény elavult a január 2019 tervezünk. 

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>A meglévő Azure virtuális gép felügyelt identitás alapértelmezett engedélyezése

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás eredetileg anélkül, hogy üzembe helyezett virtuális gépen, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot.

   ```powershell
   Connect-AzAccount
   ```

2. Először kérjen le a virtuális gép tulajdonságainak használata a `Get-AzVM` parancsmagot. Majd ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás, használja a `-AssignIdentity` váltani a [Update-azvm parancsmag](/powershell/module/az.compute/update-azvm) parancsmagot:

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Nem kötelező) Adja hozzá a felügyelt identitások az Azure-erőforrások virtuális gép (elavult. január 2019 a tervezett) bővítmény használatával a `-Type` paraméterrel a [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Ügyeljen arra, hogy adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helye:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Virtuális gép rendszer által hozzárendelt identitással hozzáadása csoporthoz

Miután engedélyezte a rendszerhez rendelt identitáshoz egy virtuális gépen, hozzáadhat egy csoporthoz.  Az alábbi eljárás egy virtuális Gépet a rendszer által hozzárendelt identitással hozzáadja egy csoporthoz.

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot.

   ```powershell
   Connect-AzAccount
   ```

2. Lekérni, és jegyezze fel a `ObjectID` (meghatározott a `Id` mezőjét, a visszaadott értékekhez) a virtuális gép szolgáltatásnév:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Lekérni, és jegyezze fel a `ObjectID` (meghatározott a `Id` mezőjét, a visszaadott értékekhez), a csoport:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. A virtuális gép egyszerű szolgáltatásnév hozzáadása a csoporthoz:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Tiltsa le a rendszer által hozzárendelt felügyelt identitás Azure virtuális gépből

A virtuális gép felügyelt identitás alapértelmezett letiltásához a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

Ha egy virtuális gépet, a rendszer által hozzárendelt felügyelt identitás már nincs szüksége, de továbbra is szüksége van a felügyelt identitásokból felhasználó által hozzárendelt, használja a következő parancsmagot:

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot.

   ```powershell
   Connect-AzAccount
   ```

2. Beolvasni a virtuális gép tulajdonságait a a `Get-AzVM` parancsmagot, és állítsa a `-IdentityType` paramétert `UserAssigned`:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Ha már nincs szüksége a felügyelt identitás rendszer által hozzárendelt virtuális gépet, és nem felügyelt felhasználó által hozzárendelt identitások rendelkezik, használja a következő parancsokat:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

A felügyelt identitások Azure-erőforrások Virtuálisgép-bővítmény eltávolítása felhasználói a - Name kapcsolót a [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) parancsmagot, a bővítmény hozzáadásakor használt azonos név megadása:

   ```powershell
   Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá, és a egy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuális Gépet az Azure PowerShell használatával.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése a virtuális gép létrehozása során

A felhasználó által hozzárendelt identitás hozzárendelése egy virtuális Gépet, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseket. Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Tekintse meg a következő Azure virtuális gép gyors útmutatók elvégzése csak a szükséges szakaszok egyikét ("Jelentkezzen be az Azure-bA", "Create resource group", "Létrehozása a hálózati csoport", "A virtuális gép létrehozása"). 
  
    Ha az "Az VM létrehozása" szakaszban kap, a módosítások kisebb a [ `New-AzVMConfig` ](/powershell/module/az.compute/new-azvm) parancsmag szintaxisát. Adja hozzá a `-IdentityType UserAssigned` és `-IdentityID ` a virtuális gép egy felhasználó által hozzárendelt identitással a paramétereket.  Cserélje le `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, és `<USER ASSIGNED IDENTITY NAME>` a saját értékeire.  Példa:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Hozzon létre egy Windows virtuális gépet PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
    - [Hozzon létre egy Linux rendszerű virtuális gép PowerShell-lel](../../virtual-machines/linux/quick-create-powershell.md)

2. (Nem kötelező) Adja hozzá az Azure-erőforrások virtuális gép bővítmény használatával felügyelt identitást a `-Type` paraméterrel a [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Ügyeljen arra, hogy adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helye:
      > [!NOTE]
    > Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok. A felügyelt identitások Azure-erőforrások Virtuálisgép-bővítmény elavult a január 2019 tervezünk.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure virtuális Gépen

A felhasználó által hozzárendelt identitás hozzárendelése egy virtuális Gépet, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseket. Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Jelentkezzen be Azure-bA `Connect-AzAccount`. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot.

   ```powershell
   Connect-AzAccount
   ```

2. Hozzon létre egy felügyelt identitás felhasználó által hozzárendelt a [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) parancsmagot.  Megjegyzés: a `Id` a kimenetben, mert szüksége lesz a következő lépésben.

   > [!IMPORTANT]
   > Felhasználó által hozzárendelt felügyelt identitás létrehozása csak alfanumerikus és kötőjel (0-9 vagy a – z vagy A – Z vagy a-) karaktert. Ezenkívül név legfeljebb 24 karakter hosszúságú VM/VMSS megfelelő működéséhez a hozzárendelés legyen. Térjen vissza frissítésekért. További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Beolvasni a virtuális gép tulajdonságainak használata a `Get-AzVM` parancsmagot. Ezután a felhasználó által hozzárendelt felügyelt identitás hozzárendelése az Azure virtuális Géphez, használja a `-IdentityType` és `-IdentityID` váltani a [Update-azvm parancsmag](/powershell/module/az.compute/update-azvm) parancsmag.  Az érték a`-IdentityId` paraméter a `Id` az előző lépésben feljegyzett.  Cserélje le `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, és `<USER ASSIGNED IDENTITY NAME>` a saját értékeire.

   > [!WARNING]
   > A virtuális géphez hozzárendelt felügyelt korábban felhasználó által hozzárendelt identitások megőrzéséhez lekérdezése a `Identity` a Virtuálisgép-objektum tulajdonságát (például `$vm.Identity`).  Ha minden olyan felhasználóhoz felügyelt identitások adja vissza, akkor belefoglalhatja őket a következő parancsot az új felhasználóhoz hozzárendelt identitás felügyelt együtt, amelyet szeretne hozzárendelni a virtuális géphez.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Adja hozzá a felügyelt identitás, az Azure-erőforrások virtuális gép (elavult. január 2019 a tervezett) bővítmény használatával a `-Type` paraméterrel a [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsmagot. "ManagedIdentityExtensionForWindows" vagy "ManagedIdentityExtensionForLinux", a virtuális gép, típusától függően adja át, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port. Adja meg a megfelelő `-Location` paraméter, egyező a meglévő virtuális gép helyét.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Távolítsa el a felhasználó által hozzárendelt felügyelt identitás Azure virtuális gépből

Egy felhasználó által hozzárendelt identitással virtuális Géphez való eltávolításához a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.

Ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitás, az alábbi parancsokkal az utolsót kivételével az összes eltávolíthatja. Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` továbbra is a virtuális gépen a felhasználó által hozzárendelt felügyelt identitáshoz tartozó name tulajdonság. Ezek az információk lekérdezésével található a `Identity` a Virtuálisgép-objektum tulajdonságának.  Ha például `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Ha a virtuális gép nem rendelkezik egy rendszer által hozzárendelt felügyelt identitást, és szeretné távolítsa el az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Ha a virtuális gép alapértelmezett és a felhasználó által hozzárendelt is felügyelt identitások, eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitások között csak alapértelmezett felügyelt identitások használatával.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd:
  
  - [Windows virtuális gép létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md) 
