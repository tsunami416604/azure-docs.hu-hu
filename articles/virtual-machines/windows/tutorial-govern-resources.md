---
title: Az Azure virtuális gépek az Azure PowerShell szabályozására |} Microsoft Docs
description: Útmutató – az Azure virtuális gépek kezelése a Szerepalapú alkalmazásával, házirendekkel, zárolások és címkék az Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 30f5fe83c46f2dbe1933e8347242be7fbb30a3e3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>Az Azure PowerShell virtuálisgép-irányítás

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha úgy dönt, hogy telepítse és a PowerShell segítségével helyileg, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. Helyi telepítés esetén is el kell [töltse le az Azure AD PowerShell modult](https://www.powershellgallery.com/packages/AzureAD/) egy új Azure Active Directory-csoport létrehozásához.

## <a name="understand-scope"></a>Hatókör ismertetése

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Ebben az oktatóanyagban alkalmaz minden beállításokat az erőforráscsoporthoz, egyszerűen távolítsa el ezeket a beállításokat, amikor hajtja végre.

Ennek az erőforráscsoportnak hozzon létre.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Az erőforráscsoport jelenleg üres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Azt szeretné, győződjön meg róla a szervezethez tartozó felhasználók a megfelelő szintű ezekhez az erőforrásokhoz való hozzáférés. Nem kívánja korlátlan hozzáférést a felhasználók számára, de azt is meg kell győződnie arról tehetik munkájukat. [Szerepköralapú hozzáférés-vezérlés](../../active-directory/role-based-access-control-what-is.md) segítségével kezelheti a jogosult felhasználók hatókörre konkrét műveletek elvégzéséhez.

Hozzon létre, és távolítsa el a szerepkör-hozzárendelések, felhasználónak rendelkeznie kell `Microsoft.Authorization/roleAssignments/*` hozzáférést. A hozzáférés a tulajdonos vagy a felhasználói hozzáférés adminisztrátora szerepkörök keresztül.

A virtuális gép megoldások kezelése, számos három erőforrás-specifikus szerepköröket, amelyek gyakran szükséges hozzáférést biztosítanak.

* [Virtuális gép közreműködő](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Tárolási fiók közreműködői](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Szerepkörök hozzárendelése az egyéni felhasználók számára, helyett célszerűbb gyakran [Azure Active Directory-csoport létrehozása](../../active-directory/active-directory-groups-create-azure-portal.md) a felhasználók, akik hasonló műveletekre. Adott csoporthoz, majd rendelje hozzá a megfelelő szerepkörhöz. Ez a cikk leegyszerűsítése hoz létre egy Azure Active Directory-csoport tagjai nélkül. Ez a csoport továbbra is hozzárendelése szerepkörhöz hatókörhöz. 

Az alábbi példa létrehoz egy Azure Active Directory csoport nevű *VMDemoContributors* és a levelezési becenév *vmDemoGroup*. A levelezési becenév szolgálja ki a csoporthoz tartozó aliast.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

A parancssor propagálása egész Azure Active Directory csoport után néhány percet vesz igénybe. Miután 20-30 másodpercet várakozik, használja a [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) parancsot az új Azure Active Directory-csoport hozzárendelése a virtuális gép közreműködő szerepkört ahhoz az erőforráscsoporthoz.  Ha azt propagálása előtt futtassa a következő parancsot, akkor kap egy hiba szerint **egyszerű <guid> nem szerepel a directory**. Próbálja újra futtatni a parancsot.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Általában, ismételje meg a folyamatot *hálózat közreműködő* és *tárolási fiók közreműködői* való győződjön meg arról, hogy a felhasználók vannak hozzárendelve a telepített erőforrások kezelése. A cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policies"></a>Az Azure házirendek

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Házirendek alkalmazása

Az előfizetés már rendelkezik több házirend-definíciók. A rendelkezésre álló házirend-definíciók megjelenítéséhez használja a [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) parancs:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Megjelenik a meglévő házirend-definíciók. A házirend típusát vagy a **beépített** vagy **egyéni**. Nézze át a definícióit, hozzárendelése kívánt feltétel leírása. Ebben a cikkben hozzárendelt házirendek, amelyek:

* Korlátozza a helyek, az összes erőforrást.
* Korlátozza a termékváltozat virtuális gépekhez.
* Virtuális gépek, amelyek nem kezelt lemezek naplózása.

A következő példában három csoportházirend-definíciók a megjelenített név alapján beolvasása. Használja a [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) parancs definíciók hozzárendelése az erőforráscsoportot. Egyes szabályzatokat az engedélyezett értékek megadásához paraméter értékeket ad meg.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>A virtuális gép telepítése

Szerepkörök és a házirendeket, készen áll a megoldás üzembe helyezéséhez rendelt hozzá. Az alapértelmezett méret Standard_DS1_v2, amely egyike a megengedett SKU. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

A telepítés befejezése után további felügyeleti beállításokat alkalmazhat a megoldás.

## <a name="lock-resources"></a>Erőforrások zárolása

[Erőforrás zárolások](../../azure-resource-manager/resource-group-lock-resources.md) akadályozza meg a szervezet véletlen törlés vagy módosítása a fontos erőforrásokhoz. Szerepköralapú hozzáférés-vezérlés, eltérően erőforrás zárolások alkalmazhatók a korlátozás összes felhasználók és szerepkörök. Beállíthatja a zárolási szint beállítása azokhoz a *CanNotDelete* vagy *ReadOnly*.

A virtuális gép és a hálózati biztonsági csoport zárolása, használja a [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) parancs:

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

A zárolásokat teszteléséhez futtassa a következő parancsot:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Arról, hogy a törlési művelet nem hajtható végre egy zárolás miatt hibaüzenet jelenik meg. Az erőforráscsoport csak akkor lehet törölni, ha kifejezetten eltávolítja a zárolása. A lépés megjelenik-e a [erőforrások törlése](#clean-up-resources).

## <a name="tag-resources"></a>Címke erőforrások

Alkalmazott [címkék](../../azure-resource-manager/resource-group-using-tags.md) számára az Azure-erőforrások logikailag kategóriák szerint rendezheti. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Címkék azokra a virtuális gép, használja a [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) parancs:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Erőforrások keresése címke szerint

A címke neve és értéke erőforrások megkereséséhez használja a [keresés-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) parancs:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

A felügyeleti feladatokat, például egy címke az összes virtuális gép leállítása a visszaadott értékeket is használhat.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Nézet költségek címkeértékeket.

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető, amíg a rendszer eltávolítja a zárolást. A zárolás eltávolításához használja a [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) parancs:

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felhasználók hozzárendelése egy szerepkörhöz
> * Házirendek, szabványok érvényesítő
> * Zárral kritikus erőforrások védelme
> * A számlázási és a felügyeleti címke erőforrások

Előzetes megtudhatja, hogyan magas rendelkezésre állású virtuális gépek kapcsolatos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitoring.md)

