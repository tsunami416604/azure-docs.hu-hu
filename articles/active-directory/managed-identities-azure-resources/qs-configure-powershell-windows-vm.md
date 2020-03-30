---
title: Felügyelt identitások konfigurálása Azure-beli virtuális gépen a PowerShell használatával – Azure AD
description: Lépésenkénti útmutató az Azure-erőforrások felügyelt identitások konfigurálása egy Azure-beli virtuális gép en powershell használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547218"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Felügyelt identitások konfigurálása Azure-erőforrásokhoz egy Azure-beli virtuális gépen a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveletek egy Azure virtuális gép.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítse [az Azure PowerShell legújabb verzióját,](/powershell/azure/install-az-ps) ha még nem tette meg.

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást az Azure PowerShell használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése az Azure virtuális gép létrehozása során

Hozzon létre egy Azure-beli virtuális gép a rendszer által hozzárendelt felügyelt identitás engedélyezve van, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Tekintse meg az alábbi Azure-vm-rövidútmutatók egyikét, amely csak a szükséges szakaszokat tölti ki ("Bejelentkezés az Azure-ba", "Erőforráscsoport létrehozása", "Hálózati csoport létrehozása", "A virtuális gép létrehozása").
    
    Amikor a "Virtuális gép létrehozása" szakaszhoz ér, hajtson végre egy kis módosítást az [Új AzVMConfig](/powershell/module/az.compute/new-azvm) parancsmag szintaxisán. Ügyeljen arra, `-AssignIdentity:$SystemAssigned` hogy adjon hozzá egy paramétert a virtuális gép kiépítéséhez a rendszer által hozzárendelt identitás engedélyezve van, például:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Windows rendszerű virtuális gépek létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
   - [Linuxos virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuális gépen

A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez egy virtuális gépen, amely eredetileg nélküle lett kiépítve, a fióknak szüksége van a [Virtuálisgép közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. A virtuális gép tartalmazó Azure-előfizetéshez társított fiók használata.

   ```powershell
   Connect-AzAccount
   ```

2. Először olvassa be a `Get-AzVM` virtuális gép tulajdonságait a parancsmag használatával. Ezután a rendszer által hozzárendelt felügyelt `-AssignIdentity` identitás engedélyezéséhez használja az [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmag kapcsolóját:

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Virtuálisgép-rendszer hozzárendelt identitásának hozzáadása egy csoporthoz

Miután engedélyezte a rendszer hozzárendelt identitása a virtuális gép, hozzáadhatja azt egy csoporthoz.  Az alábbi eljárás hozzáadja a virtuális gép egy csoporthoz rendelt identitását.

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. A virtuális gép tartalmazó Azure-előfizetéshez társított fiók használata.

   ```powershell
   Connect-AzAccount
   ```

2. A virtuális `ObjectID` gép szolgáltatásnév (a `Id` visszaadott értékek mezőjében meghatározott) lekérése és megjegyzése:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. A csoport `ObjectID` lekérése és megjegyzése (a `Id` visszaadott értékek mezőjében megadottak szerint):

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Adja hozzá a virtuális gép egyszerű szolgáltatását a csoporthoz:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>A rendszer által hozzárendelt felügyelt identitás letiltása Egy Azure virtuális gépről

A rendszer által hozzárendelt felügyelt identitás letiltásához a virtuális gépen a fióknak szüksége van a [Virtuálisgép közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

Ha olyan virtuális géppel rendelkezik, amelynek már nincs szüksége a rendszer által hozzárendelt felügyelt identitásra, de továbbra is szüksége van a felhasználó által hozzárendelt felügyelt identitásokra, használja a következő parancsmast:

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. A virtuális gép tartalmazó Azure-előfizetéshez társított fiók használata.

   ```powershell
   Connect-AzAccount
   ```

2. A virtuális gép tulajdonságainak beolvasása `Get-AzVM` `-IdentityType` a `UserAssigned`parancsmag használatával, és állítsa a paramétert a következőre:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Ha olyan virtuális gépe van, amelynek már nincs szüksége rendszeráltal hozzárendelt felügyelt identitásra, és nem rendelkezik a felhasználó által hozzárendelt felügyelt identitásokkal, használja a következő parancsokat:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy virtuális gépről az Azure PowerShell használatával.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuális géphez a létrehozás során

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Tekintse meg az alábbi Azure-vm-rövidútmutatók egyikét, amely csak a szükséges szakaszokat tölti ki ("Bejelentkezés az Azure-ba", "Erőforráscsoport létrehozása", "Hálózati csoport létrehozása", "A virtuális gép létrehozása"). 
  
    Amikor a "Virtuális gép létrehozása" szakaszhoz ér, hajtsa végre egy kis módosítását a [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) parancsmag szintaxisán. Adja `-IdentityType UserAssigned` hozzá `-IdentityID` a és a paramétereket a virtuális gép egy felhasználó által hozzárendelt identitással.  Cserélje `<VM NAME>``<SUBSCRIPTION ID>`ki `<RESROURCE GROUP>`a `<USER ASSIGNED IDENTITY NAME>` , , , és a saját értékeket.  Példa:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Windows rendszerű virtuális gépek létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
    - [Linuxos virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. A virtuális gép tartalmazó Azure-előfizetéshez társított fiók használata.

   ```powershell
   Connect-AzAccount
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) parancsmag használatával.  Vegye `Id` figyelembe a kimenetet, mert szüksége lesz erre a következő lépésben.

   > [!IMPORTANT]
   > A felhasználó által hozzárendelt felügyelt identitások létrehozása csak az alfanumerikus, aláhúzás- és \_ kötőjel (0-9 vagy a-z vagy A-Z vagy -) karaktereket támogatja. Emellett a név 3-tól 128 karakterhosszúságúig kell lennie ahhoz, hogy a virtuális gép/VMSS hozzárendelése megfelelően működjön. További információkért lásd [a gyIK-et és az ismert problémákat](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. A virtuális gép tulajdonságainak lekérése a `Get-AzVM` parancsmag használatával. Ezután rendeljen hozzá egy felhasználó által hozzárendelt felügyelt `-IdentityType` identitást az Azure virtuális géphez, használja a és `-IdentityID` kapcsolja be az [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmag.  A paraméter`-IdentityId` értéke az `Id` előző lépésben megadott érték.  Cserélje `<VM NAME>` `<SUBSCRIPTION ID>`ki `<RESROURCE GROUP>`a `<USER ASSIGNED IDENTITY NAME>` , , , és a saját értékeket.

   > [!WARNING]
   > A virtuális géphez rendelt, korábban felhasználó által hozzárendelt `Identity` felügyelt identitások megőrzéséhez kérdezze `$vm.Identity`le a virtuális gép objektumának tulajdonságát (például.  Ha bármely felhasználó hozzárendelt felügyelt identitások vissza, tartalmazza őket a következő parancs mellett az új felhasználó által hozzárendelt felügyelt identitást szeretne rendelni a virtuális gép.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuális gépből

A virtuális géphez felhasználó által hozzárendelt identitás eltávolításához a fióknak szüksége van a [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.

Ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitással rendelkezik, az alábbi parancsok használatával eltávolíthatja az összes, de az utolsó. Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` a felhasználó által hozzárendelt felügyelt identitás név tulajdonsága, amely továbbra is a virtuális gép. Ez az információ megtalálható a `Identity` virtuális gép objektum tulajdonságának lekérdezésével.  Például: `$vm.Identity`

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Ha a virtuális gép nem rendelkezik rendszeráltal hozzárendelt felügyelt identitással, és el szeretné távolítani belőle az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Ha a virtuális gép rendelkezik a rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitások, eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitások váltással csak a rendszer által hozzárendelt felügyelt identitások.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes Azure virtuális gép létrehozása rövid útmutatók, lásd:
  
  - [Windows rendszerű virtuális gép létrehozása PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása PowerShell segítségével](../../virtual-machines/linux/quick-create-powershell.md) 
