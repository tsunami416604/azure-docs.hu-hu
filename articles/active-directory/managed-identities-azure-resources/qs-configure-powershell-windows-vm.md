---
title: Felügyelt identitások konfigurálása Azure-beli virtuális gépen a PowerShell használatával – Azure AD
description: Részletes útmutató az Azure-beli virtuális gépeken futó Azure-erőforrások felügyelt identitásának konfigurálásához a PowerShell használatával.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547218"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Felügyelt identitások konfigurálása Azure-beli virtuális gépeken az Azure-erőforrásokhoz a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy Azure-beli virtuális gépen.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha még nem tette meg [, telepítse a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) .

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást Azure PowerShell használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuális gép létrehozásakor

Ha olyan Azure virtuális gépet szeretne létrehozni, amelyen engedélyezve van a rendszerhez rendelt felügyelt identitás, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Tekintse át a következő Azure-beli VM-gyors útmutatókat, csak a szükséges részeket ("Bejelentkezés az Azure-ba", "erőforráscsoport létrehozása", "hálózatkezelési csoport létrehozása", "Create The VM").
    
    Ha a "virtuális gép létrehozása" szakaszra kattint, a [New-AzVMConfig](/powershell/module/az.compute/new-azvm) parancsmag szintaxisának kis módosítását kell elvégeznie. Ügyeljen arra, hogy hozzáadjon egy `-AssignIdentity:$SystemAssigned` paramétert a virtuális gép kiépítéséhez, amely engedélyezve van a rendszer által hozzárendelt identitással, például:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Windows rendszerű virtuális gépek létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
   - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Rendszerhez rendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuális gépen

Ha olyan virtuális gépen szeretné engedélyezni a rendszer által hozzárendelt felügyelt identitást, amelyet eredetileg anélkül osztott ki, a fióknak szüksége van a [virtuális gép közreműködői](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkörének hozzárendelésére.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Használjon olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

   ```powershell
   Connect-AzAccount
   ```

2. Először kérje le a virtuális gép tulajdonságait `Get-AzVM` a parancsmag használatával. Ezután a rendszerhez rendelt felügyelt identitás engedélyezéséhez használja az `-AssignIdentity` [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmag kapcsolóját:

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Virtuálisgép-rendszerhez rendelt identitás hozzáadása egy csoporthoz

Miután engedélyezte a rendszerhez rendelt identitást egy virtuális gépen, felveheti azt egy csoportba.  Az alábbi eljárás egy virtuális gép rendszerhez rendelt identitását adja hozzá egy csoporthoz.

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Használjon olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

   ```powershell
   Connect-AzAccount
   ```

2. Kérje le és jegyezze `ObjectID` fel a virtuális gép egyszerű `Id` szolgáltatásnév mezőjében megadott értéket:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. A csoport lekérése és megjegyzése `ObjectID` ( `Id` a visszaadott értékek mezőben megadott módon):

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Adja hozzá a virtuális gép egyszerű szolgáltatásnevet a következő csoporthoz:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás letiltása egy Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszerhez rendelt felügyelt identitást egy virtuális gépen, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

Ha olyan virtuális géppel rendelkezik, amelyhez már nincs szükség a rendszerhez rendelt felügyelt identitásra, de továbbra is felhasználó által hozzárendelt felügyelt identitásokra van szükség, használja a következő parancsmagot:

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Használjon olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

   ```powershell
   Connect-AzAccount
   ```

2. Kérje le a virtuális gép tulajdonságait `Get-AzVM` a parancsmag használatával, `-IdentityType` és állítsa `UserAssigned`a paramétert a következőre:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Ha olyan virtuális géppel rendelkezik, amelyhez már nincs szükség a rendszerhez rendelt felügyelt identitásra, és nincs felhasználó által hozzárendelt felügyelt identitása, használja a következő parancsokat:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználóhoz rendelt felügyelt identitást egy virtuális gépről Azure PowerShell használatával.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése virtuális géphez a létrehozás során

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Tekintse át a következő Azure-beli VM-gyors útmutatókat, csak a szükséges részeket ("Bejelentkezés az Azure-ba", "erőforráscsoport létrehozása", "hálózatkezelési csoport létrehozása", "Create The VM"). 
  
    Ha a "virtuális gép létrehozása" szakaszra kattint, akkor a [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) parancsmag szintaxisa némileg módosul. Adja hozzá `-IdentityType UserAssigned` a `-IdentityID` és a paramétereket a virtuális gép felhasználó által hozzárendelt identitással való kiépítéséhez.  Cserélje le `<VM NAME>`a `<RESROURCE GROUP>`,, `<USER ASSIGNED IDENTITY NAME>` , és értéket a saját értékeire.`<SUBSCRIPTION ID>`  Például:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Windows rendszerű virtuális gépek létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
    - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Használjon olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

   ```powershell
   Connect-AzAccount
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) parancsmag használatával.  `Id` Ügyeljen rá, hogy a kimenetben a következő lépésben szükség lesz rá.

   > [!IMPORTANT]
   > A felhasználó által hozzárendelt felügyelt identitások létrehozása csak alfanumerikus karaktereket, aláhúzást és kötőjelet (0-9 vagy a-z \_ , a-z vagy-) karaktert támogat. Emellett a névnek 3 – 128 karakterből kell állnia ahhoz, hogy a hozzárendelt virtuális gép vagy VMSS megfelelően működjön. További információ: [Gyakori kérdések és ismert problémák](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Kérje le a virtuális gép tulajdonságait `Get-AzVM` a parancsmag használatával. Ezután rendeljen hozzá egy felhasználóhoz rendelt felügyelt identitást az Azure-beli `-IdentityType` virtuális `-IdentityID` géphez, használja az and kapcsolót az [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmaggal.  A`-IdentityId` paraméter `Id` értéke az előző lépésben feljegyzett érték.  Cserélje le `<VM NAME>`a `<RESROURCE GROUP>`,, `<USER ASSIGNED IDENTITY NAME>` , és értéket a saját értékeire. `<SUBSCRIPTION ID>`

   > [!WARNING]
   > Ha meg szeretné őrizni a virtuális géphez hozzárendelt korábban felhasználóhoz rendelt felügyelt identitásokat `Identity` , kérdezze le a virtuálisgép-objektum tulajdonságát `$vm.Identity`(például:).  Ha a rendszer minden felhasználóhoz hozzárendelt felügyelt identitást ad vissza, akkor a következő parancsban adja meg azokat az új felhasználóhoz rendelt felügyelt identitást, amelyet hozzá szeretne rendelni a virtuális géphez.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása Azure-beli virtuális gépről

A felhasználó által hozzárendelt identitás egy virtuális géphez való eltávolításához a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.

Ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van, az alábbi parancsokkal távolíthatja el az összeset, de az utolsót is. Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` a felhasználó által hozzárendelt felügyelt identitás Name tulajdonsága, amely a virtuális gépen marad. Ez az információ a virtuálisgép-objektum `Identity` tulajdonságának lekérdezésével érhető el.  Például `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Ha a virtuális gépnek nincs rendszerhez rendelt felügyelt identitása, és el szeretné távolítani az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Ha a virtuális gép rendszerhez hozzárendelt és felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, a felhasználó által hozzárendelt összes felügyelt identitást eltávolíthatja úgy, hogy csak a rendszerhez rendelt felügyelt identitásokat használja.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes körű Azure-beli virtuális gépek létrehozásához a következő témakörben talál további információt:
  
  - [Windows rendszerű virtuális gép létrehozása PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása PowerShell segítségével](../../virtual-machines/linux/quick-create-powershell.md) 
