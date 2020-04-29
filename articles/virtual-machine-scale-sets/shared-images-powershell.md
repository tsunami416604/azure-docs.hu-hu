---
title: Megosztott virtuálisgép-rendszerképek használata méretezési csoport létrehozásához az Azure-ban
description: Megtudhatja, hogyan hozhat létre a virtuálisgép-méretezési csoportok Azure-ban való üzembe helyezéséhez használható megosztott virtuálisgép-rendszerképeket a Azure PowerShell használatával.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368734"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Megosztott rendszerképek létrehozása és használata virtuálisgép-méretezési csoportokhoz a Azure PowerShell

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. A megosztott rendszerkép-katalógus szolgáltatás nagymértékben leegyszerűsíti az Egyéni rendszerképek megosztását a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes körű szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik. 

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. Ebben a cikkben a következő lépéseket fogjuk használni vagy felépíteni:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez egy alapszintű rendszerkép, amely önmagában vagy rendszerkép- **verzió** létrehozásához használható egy képgyűjteményben. A felügyelt lemezképek általánosított virtuális gépekről jönnek létre. A felügyelt rendszerkép olyan speciális VHD-típus, amellyel több virtuális gép hozható létre, és most már használható a megosztott rendszerkép-verziók létrehozásához is. |
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A lemezképek a katalógusban vannak definiálva, és a rendszerképekkel és a belső használattal kapcsolatos követelményekkel rendelkeznek. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerkép verziója** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amellyel új virtuálisgép-példányokat hozhat létre.

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy meglévő felügyelt képpel. A következő [oktatóanyagot követheti: hozzon létre és használjon egyéni rendszerképet a virtuálisgép-méretezési csoportokhoz, Azure PowerShell](tutorial-use-custom-image-powershell.md) segítségével hozzon létre egyet, ha szükséges. Ha a felügyelt lemezkép adatlemezt tartalmaz, az adatlemez mérete nem haladhatja meg az 1 TB-ot.

A cikkben végzett munka során cserélje le az erőforráscsoportot és a virtuális gépek nevét, ahol szükséges.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Méretezési csoport létrehozása a megosztott rendszerkép verziójából

Hozzon létre egy virtuálisgép-méretezési készletet a [New-AzVmss](/powershell/module/az.compute/new-azvmss). A következő példa létrehoz egy méretezési készletet az új rendszerkép-verzióból az *USA déli középső* régiójában. Ha a rendszer kéri, állítsa be a saját rendszergazdai hitelesítő adatait a méretezési csoport virtuálisgép-példányaihoz:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>További lépések

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Megosztott Képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerkép-verzió létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](troubleshooting-shared-images.md)című témakört.
