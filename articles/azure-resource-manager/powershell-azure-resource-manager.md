---
title: Az Azure PowerShell-megoldások kezelése |} Microsoft Docs
description: Azure PowerShell és a Resource Manager segítségével kezelheti az erőforrásokat.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 02616ef566dd576c3f406d4b9f3059dab27bf3e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603413"
---
# <a name="manage-resources-with-azure-powershell"></a>Az Azure PowerShell-erőforrások kezelése

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="understand-scope"></a>A hatókör megismerése

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

Ez a cikk telepít, a virtuális gépek és a kapcsolódó virtuális hálózatot. A virtuálisgép-megoldások kezeléséhez három erőforrás-specifikus szerepkör létezik, amelyek biztosítják a leggyakrabban szükséges hozzáféréseket:

* [Virtuális gépek közreműködője](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../role-based-access-control/built-in-roles.md#network-contributor)
* [Tárfiók-közreműködő](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Ahelyett, hogy szerepköröket rendelne az egyéni felhasználókhoz, gyakran célszerűbb [létrehozni egy Azure Active Directory-csoportot](../active-directory/active-directory-groups-create-azure-portal.md) azoknak a felhasználóknak, akiknek hasonló műveleteket kell elvégezniük. Ezután rendelje hozzá a csoportot a megfelelő szerepkörhöz. Ebben a cikkben az egyszerűség kedvéért egy tagok nélküli Azure Active Directory-csoportot fog létrehozni. A csoportot így is hozzárendelheti egy szerepkörhöz, hogy legyen hatóköre. 

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

A folyamatot általában a **Hálózati közreműködő** és a **Tárfiók-közreműködő** szerepkörön is végre kell hajtani, hogy a felhasználók megkapják az üzembe helyezett erőforrások kezeléséhez szükséges jogosultságokat. Ebben a cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policies"></a>Azure-szabályzatok

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Szabályzatok alkalmazása

Az előfizetése már számos szabályzatdefinícióval rendelkezik. A rendelkezésre álló házirend-definíciók megjelenítéséhez használja:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Itt láthatja a meglévő szabályzatdefiníciókat. A szabályzat típusa lehet **Beépített** vagy **Egyéni**. Keresse meg azokat a definíciókat, amelyek az Ön által hozzárendelni kívánt feltételt írják le. Ebben a cikkben olyan szabályzatokat rendel hozzá, amelyek:

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

A virtuális gép csak akkor lehet törölni, ha kifejezetten távolítsa el a zárolást. Ezt a lépést [Az erőforrások eltávolítása](#clean-up-resources) című szakasz ismerteti.

## <a name="tag-resources"></a>Erőforrások címkézése

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Erőforrások címkézése

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

A visszaadott értékeket kezelési feladatokhoz, például az adott címkeértékkel rendelkező összes virtuális gép leállításához használhatja.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Költségek megtekintése címkeértékek szerint

Címkék alkalmazása az erőforrásokat, után megtekintheti a címkék erőforrás költségeket. Költség elemzéshez megjelenítése a legutóbbi használati, ezért még nem láthatók a költségek igénybe vesz igénybe. A költségek érhetők el, tekintheti költségek erőforrások erőforráscsoportok közötti az előfizetésben. Felhasználónak rendelkeznie kell [szintű hozzáféréssel előfizetés számlázási adatokat](../billing/billing-manage-access.md) költségeket megjelenítéséhez.

A portál kódcímke költségek megtekintéséhez jelölje ki az előfizetését, és válassza ki **költség Analysis**.

![Költségelemzés](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Ezt követően a címke értéke alapján szűrni, és válassza ki **alkalmaz**.

![Nézet költség címke szerint](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Használhatja a [Azure számlázási API-k](../billing/billing-usage-rate-card-overview.md) költségek programozott módon megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető a zárolás eltávolításáig. Távolítsa el a zárolást, használja:

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
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
