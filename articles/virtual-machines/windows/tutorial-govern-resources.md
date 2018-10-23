---
title: Oktatóanyag – Azure-beli virtuális gépek szabályozása az Azure PowerShell használatával | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan kezelheti Azure-beli virtuális gépeit az RBAC, szabályzatok, zárolások és címkék alkalmazásával az Azure PowerShell-lel
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 10/12/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 6377a54cc862bb5f62726c3ce91a41cc6eb0763d
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311388"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>Oktatóanyag: Windowsos virtuális gépek vezérlése az Azure PowerShell-lel

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

A cikkben szereplő példák használatához az Azure PowerShell 6.0-s vagy újabb verziója szükséges. Ha helyileg futtatja a PowerShellt, és 6.0-snál régebbi verzióval rendelkezik, [frissítsen egy újabb verzióra](/powershell/azure/install-azurerm-ps). Emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. A helyi telepítések estén [az Azure AD PowerShell-modult is le kell töltenie](https://www.powershellgallery.com/packages/AzureAD/), hogy létrehozhasson egy új Azure Active Directory-csoportot.

## <a name="understand-scope"></a>A hatókör megismerése

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Ebben az oktatóanyagban minden kezelési beállítást egy erőforráscsoportra alkalmaz, így egyszerűen eltávolíthatja a beállításokat, ha elkészült.

Hozzuk létre az erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Az erőforráscsoport jelenleg üres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Győződjön meg arról, hogy az intézmény felhasználói megfelelő hozzáférési szinttel rendelkeznek ezekhez az erőforrásokhoz. Nem ajánlott korlátlan hozzáférést nyújtania a felhasználóknak, de fontos biztosítania, hogy el tudják végezni a munkájukat. A [Szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md) kezelheti, hogy az egyes hatókörökben mely felhasználók hajthatnak végre adott műveleteket.

A szerepkör-hozzárendelések létrehozásához és eltávolításához a felhasználóknak `Microsoft.Authorization/roleAssignments/*` hozzáféréssel kell rendelkezniük. Ez a hozzáférés a tulajdonosi vagy a felhasználói hozzáférés rendszergazdájának szerepkörével adható meg.

A virtuálisgép-megoldások kezeléséhez három erőforrás-specifikus szerepkör létezik, amelyek biztosítják a leggyakrabban szükséges hozzáféréseket:

* [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Tárfiók-közreműködő](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Ahelyett, hogy szerepköröket rendelne az egyéni felhasználókhoz, gyakran célszerűbb egy azokat a felhasználókat tartalmazó Azure Active Directory-csoportot használni, akiknek hasonló műveleteket kell elvégezniük. Ezután rendelje hozzá a csoportot a megfelelő szerepkörhöz. Ehhez a cikkhez használhat egy meglévő csoportot a virtuális gép kezeléséhez, vagy a portálon [létrehozhat egy Azure Active Directory-csoportot](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Egy új csoport létrehozása vagy egy meglévő csoport megkeresése után a [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) paranccsal rendelje hozzá az Azure Active Directory-csoportot az erőforráscsoport Virtuális gépek közreműködője szerepköréhez.  

```azurepowershell-interactive
$adgroup = Get-AzureRmADGroup -DisplayName <your-group-name>

New-AzureRmRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Ha egy hibaüzenetet kap, amely szerint **A <guid> rendszerbiztonsági tag nem található a címtárban**, az új csoport nem lett az Azure Active Directoryba propagálva. Próbálja meg ismét futtatni a parancsot.

A folyamatot általában a *Hálózati közreműködő* és a *Tárfiók-közreműködő* szerepkörön is végre kell hajtani, hogy a felhasználók megkapják az üzembe helyezett erőforrások kezeléséhez szükséges jogosultságokat. Ebben a cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policy"></a>Azure Policy

Az [Azure Policy](../../azure-policy/azure-policy-introduction.md) segítségével ellenőrizheti, hogy az előfizetés összes erőforrása megfelel-e a vállalati szabványoknak. Az előfizetése már számos szabályzatdefinícióval rendelkezik. Az elérhető szabályzatdefiníciók megtekintéséhez használja a [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) parancsot:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Itt láthatja a meglévő szabályzatdefiníciókat. A szabályzat típusa lehet **Beépített** vagy **Egyéni**. Keresse meg azokat a definíciókat, amelyek az Ön által hozzárendelni kívánt feltételt írják le. Ebben a cikkben olyan szabályzatokat rendel hozzá, amelyek:

* Korlátozzák az összes erőforrás helyét.
* Korlátozzák a virtuális gépek termékváltozatait.
* Naplózzák a felügyelt lemezeket nem használó virtuális gépeket.

A következő példában három olyan szabályzatdefiníciót fog lekérni, amely a megjelenítendő néven alapul. A [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) paranccsal rendelje hozzá ezeket a definíciókat az erőforráscsoporthoz. Egyes szabályzatoknál paraméterértékekkel határozhatja meg az engedélyezett értékeket.

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

## <a name="deploy-the-virtual-machine"></a>A virtuális gép üzembe helyezése

A szerepkörök és szabályzatok hozzárendelése megtörtént, így megkezdheti a megoldás üzembe helyezését. Az alapértelmezett méret a Standard_DS1_v2, amely az egyik engedélyezett termékváltozat. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

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

Az üzembe helyezés befejezése után további kezelési beállításokat alkalmazhat a megoldáson.

## <a name="lock-resources"></a>Erőforrások zárolása

Az [Erőforrás-zárolások](../../azure-resource-manager/resource-group-lock-resources.md) megakadályozzák, hogy a cég vagy intézmény felhasználói véletlenül töröljék vagy módosítsák a kritikus erőforrásokat. Az erőforrás-zárolások a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaznak. A zárolási szintet *CanNotDelete* (nem törölhető) vagy *ReadOnly* (csak olvasható) értékre állíthatja be.

A virtuális gép és a hálózati biztonsági csoport zárolásához használja a [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) parancsot:

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

A zárolások teszteléséhez futtassa az alábbi parancsot:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Megjelenik egy hibaüzenet, amely szerint a törlési művelet nem hajtható végre egy zárolás miatt. Az erőforráscsoport csak akkor törölhető, ha külön eltávolítja a zárolásokat. Ezt a lépést [Az erőforrások eltávolítása](#clean-up-resources) című szakasz ismerteti.

## <a name="tag-resources"></a>Erőforrások címkézése

A [címkézéssel](../../azure-resource-manager/resource-group-using-tags.md) logikusan, kategóriák szerint rendszerezheti az Azure-erőforrásokat. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Egy virtuális gép címkézéséhez használja a [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) parancsot:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Erőforrások keresése címke szerint

Az adott címkenévvel és -értékkel rendelkező erőforrások kereséséhez használja a [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) parancsot:

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

A visszaadott értékeket kezelési feladatokhoz, például az adott címkeértékkel rendelkező összes virtuális gép leállításához használhatja.

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Költségek megtekintése címkeértékek szerint

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető a zárolás eltávolításáig. A zárolás eltávolításához használja a [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) parancsot:

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
> * Felhasználók hozzárendelése szerepkörhöz
> * Szabványokat kényszerítő szabályzatok alkalmazása
> * Kritikus erőforrások védelme zárolásokkal
> * Erőforrások címkézése számlázáshoz és felügyelethez

A következő oktatóanyagban a magas rendelkezésre állású virtuális gépeket ismerheti meg.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitoring.md)

