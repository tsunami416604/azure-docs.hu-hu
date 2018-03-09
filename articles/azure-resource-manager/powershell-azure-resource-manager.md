---
title: "Az Azure PowerShell-megoldások kezelése |} Microsoft Docs"
description: "Azure PowerShell és a Resource Manager segítségével kezelheti az erőforrásokat."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 96206482195cdcbd06ee2dafdc551f7b1f81d319
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Az Azure PowerShell-erőforrások kezelése

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha úgy dönt, hogy telepítse és a PowerShell segítségével helyileg, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="understand-scope"></a>Hatókör ismertetése

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

Ebben a cikkben alkalmaz minden beállításokat az erőforráscsoporthoz, egyszerűen távolítsa el ezeket a beállításokat, amikor hajtja végre.

Hozzon létre az erőforráscsoportot.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Az erőforráscsoport jelenleg üres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>A szerepkör hozzárendelése

Ez a cikk telepít, a virtuális gépek és a kapcsolódó virtuális hálózatot. A virtuális gép megoldások kezelése, számos három erőforrás-specifikus szerepköröket, amelyek gyakran szükséges hozzáférést biztosítanak.

* [Virtuális gép közreműködő](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Tárolási fiók közreműködői](../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Szerepkörök hozzárendelése az egyéni felhasználók számára, helyett célszerűbb gyakran [Azure Active Directory-csoport létrehozása](../active-directory/active-directory-groups-create-azure-portal.md) a felhasználók, akik hasonló műveletekre. Adott csoporthoz, majd rendelje hozzá a megfelelő szerepkörhöz. Ez a cikk leegyszerűsítése hoz létre egy Azure Active Directory-csoport tagjai nélkül. Ez a csoport továbbra is hozzárendelése szerepkörhöz hatókörhöz. 

Az alábbi példa létrehoz egy csoportot, és hozzárendeli a virtuális gép közreműködő szerepkört ahhoz az erőforráscsoporthoz. Futtatásához a `New-AzureAdGroup` parancsban, vagy használja kell a [Azure Cloud rendszerhéj](/azure/cloud-shell/overview) vagy [töltse le az Azure AD PowerShell modult](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Általában, ismételje meg a folyamatot **hálózat közreműködő** és **tárolási fiók közreműködői** való győződjön meg arról, hogy a felhasználók vannak hozzárendelve a telepített erőforrások kezelése. A cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policies"></a>Az Azure házirendek

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Házirendek alkalmazása

Az előfizetés már rendelkezik több házirend-definíciók. A rendelkezésre álló házirend-definíciók megjelenítéséhez használja:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Megjelenik a meglévő házirend-definíciók. A házirend típusát vagy a **beépített** vagy **egyéni**. Nézze át a definícióit, hozzárendelése kívánt feltétel leírása. Ebben a cikkben hozzárendelt házirendek, amelyek:

* a helyek, az összes erőforrás korlátozása
* korlátozza a virtuális gépek termékváltozatok
* virtuális gépek, amelyek nem kezelt lemezek naplózása

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
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

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Egy erőforrás zárolása

A virtuális gép és a hálózati biztonsági csoport zárolásához használja:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

A virtuális gép csak akkor lehet törölni, ha kifejezetten távolítsa el a zárolást. A lépés megjelenik-e a [erőforrások törlése](#clean-up-resources).

## <a name="tag-resources"></a>Címke erőforrások

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Címke erőforrások

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Címkék egy virtuális géphez segítségével:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Erőforrások keresése címke szerint

A címke neve és értéke erőforrások megkereséséhez használja:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

A felügyeleti feladatokat, például egy címke az összes virtuális gép leállítása a visszaadott értékeket is használhat.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Nézet költségek címkeértékeket.

Címkék alkalmazása az erőforrásokat, után megtekintheti a címkék erőforrás költségeket. Költség elemzéshez megjelenítése a legutóbbi használati, ezért még nem láthatók a költségek igénybe vesz igénybe. A költségek érhetők el, tekintheti költségek erőforrások erőforráscsoportok közötti az előfizetésben. Felhasználónak rendelkeznie kell [szintű hozzáféréssel előfizetés számlázási adatokat](../billing/billing-manage-access.md) költségeket megjelenítéséhez.

A portál kódcímke költségek megtekintéséhez jelölje ki az előfizetését, és válassza ki **költség Analysis**.

![Költségelemzés](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Ezt követően a címke értéke alapján szűrni, és válassza ki **alkalmaz**.

![Nézet költség címke szerint](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Használhatja a [Azure számlázási API-k](../billing/billing-usage-rate-card-overview.md) költségek programozott módon megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető, amíg a rendszer eltávolítja a zárolást. Távolítsa el a zárolást, használja:

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

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések
* A virtuális gépek figyelésével kapcsolatos további tudnivalókért lásd: [figyelésére és frissítésére a Windows rendszerű virtuális gép az Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* További információ az Azure Security Center megvalósításához ajánlott biztonsági eljárások használatáról [virtuális gép biztonsági figyelje az Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Meglévő erőforrásokat áthelyezheti egy új erőforráscsoportot. Tekintse meg a [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
