---
title: Méretezési csoport létrehozása az Azure-ban megosztott virtuálisgép-lemezképek használatával
description: Megtudhatja, hogyan hozhat létre megosztott virtuálisgép-lemezképeket az Azure-ban az Azure-ban az Azure-ban használt megosztott virtuálisgép-lemezképek létrehozásához.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368734"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Megosztott lemezképek létrehozása és használata az Azure PowerShell használatával a virtuálisgép-méretezési csoportokhoz

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. A Megosztott képtár szolgáltatás nagymértékben leegyszerűsíti az egyéni képmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képtár lehetővé teszi, hogy az egyéni virtuálisgép-képeket megoszthatja a szervezet másaival, a régiókon belül vagy régiók között, egy AAD-bérlőn belül. Válassza ki, hogy mely képeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és kivel szeretné megosztani őket. Több galériát is létrehozhat, így logikusan csoportosíthatja a megosztott képeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A rendszerképek verziószámba állíthatók, és kiválaszthatja, hogy az egyes lemezképek verzióit az Azure-régiók egy másik készletére replikálja.Images can be versioned, and you can choose to replicate each image version to a different set of Azure regions. A galéria csak felügyelt képekkel működik. 

A Megosztott képtár szolgáltatás több erőforrástípussal rendelkezik. Mi lesz használ vagy épület ezek ebben a cikkben:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt kép** | Ez egy alapkép, amely önmagában használható, vagy **képverzió** létrehozására használható egy képgalériában. Felügyelt lemezképek általános virtuális gépekből jönnek létre. A felügyelt lemezkép egy speciális típusú virtuális merevlemez, amely több virtuális gép létrehozásához használható, és most már használható megosztott lemezkép-verziók létrehozásához. |
| **Képgaléria** | Az Azure Marketplace-hez hasonlóan a **képgaléria** is egy tárház a képek kezeléséhez és megosztásához, de ön szabályozza, hogy ki rendelkezik hozzáféréssel. |
| **Képdefiníció** | A képek egy galérián belül vannak definiálva, és a képre és a belső használatra vonatkozó követelményekre vonatkozó információkat tartalmaznak. Ez magában foglalja, hogy a lemezkép Windows vagy Linux, kiadási megjegyzések, valamint minimális és maximális memóriakövetelmények. Ez egy fajta kép definíciója. |
| **Kép verziószáma** | A **lemezkép-verzió** az, amit a virtuális gép létrehozásához használ katalógus használatakor. A környezetnek szüksége van egy lemezkép több verziójára is. A felügyelt lemezképhez hasonlóan, amikor egy **lemezkép-verziót** használ a virtuális gép létrehozásához, a rendszerkép-verzió a virtuális gép új lemezeinek létrehozásához használatos. A képverziótöbbször is használható. |

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amellyel új virtuálisgép-példányokat hozhat létre.

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy meglévő felügyelt lemezképpel. Kövesse [az oktatóanyagot: Hozzon létre és használjon egyéni lemezképet a virtuálisgép-méretezési készletekhez az Azure PowerShell segítségével,](tutorial-use-custom-image-powershell.md) ha szükséges. Ha a felügyelt lemez adatlemezt tartalmaz, az adatlemez mérete nem lehet több 1 TB-nál.

A cikk munka során cserélje le az erőforráscsoport és a virtuális gép nevét, ahol szükséges.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Méretezési készlet létrehozása a megosztott képverzióból

Hozzon létre egy virtuálisgép-méretezési készletet [az Új AzVmss](/powershell/module/az.compute/new-azvmss)segítségével. A következő példa létrehoz egy méretezési készletet az új lemezkép-verzióból az *USA déli középső adatközpontjában.* Amikor a rendszer kéri, állítsa be a saját rendszergazdai hitelesítő adatait a méretezési csoportban a virtuálisgép-példányok:


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

A megosztott képtár erőforrást sablonok használatával is létrehozhatja. Számos Azure-gyorsindítási sablon áll rendelkezésre: 

- [Megosztott képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Képdefiníció létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Képverzió létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása a rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képgalériákról további információt az [Áttekintés című témakörben talál.](shared-image-galleries.md) Ha problémákba ütközik, olvassa el A [megosztott képgalériák hibaelhárítása című témakört.](troubleshooting-shared-images.md)
