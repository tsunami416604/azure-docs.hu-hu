---
title: Megosztott virtuálisgép-lemezképek létrehozása az Azure PowerShell használatával
description: Megtudhatja, hogy miként hozhat létre az Azure PowerShellt megosztott virtuálisgép-lemezkép létrehozására az Azure-ban
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: cfaf545ad2388688666a04076559ff82b7a5d120
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458062"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Megosztott képgaléria létrehozása az Azure PowerShell használatával 

A [megosztott képtár](shared-image-galleries.md) leegyszerűsíti az egyéni képmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Egyéni lemezképek segítségével rendszerindításközponti telepítési feladatok, például az alkalmazások előtöltése, alkalmazáskonfigurációk és egyéb operációs rendszer konfigurációk. 

A megosztott képtár lehetővé teszi, hogy az egyéni virtuálisgép-képeket megoszthatja a szervezet másaival, a régiókon belül vagy régiók között, egy AAD-bérlőn belül. Válassza ki, hogy mely képeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és kivel szeretné megosztani őket. Több galériát is létrehozhat, így logikusan csoportosíthatja a megosztott képeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A rendszerképek verziószámba állíthatók, és kiválaszthatja, hogy az egyes lemezképek verzióit az Azure-régiók egy másik készletére replikálja.Images can be versioned, and you can choose to replicate each image version to a different set of Azure regions. A galéria csak felügyelt képekkel működik.

A Megosztott képtár szolgáltatás több erőforrástípussal rendelkezik. Mi lesz használ vagy épület ezek ebben a cikkben:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt kép** | Ez egy alapkép, amely önmagában használható, vagy **képverzió** létrehozására használható egy képgalériában. Felügyelt lemezképek általános virtuális gépekből jönnek létre. A felügyelt lemezkép egy speciális típusú virtuális merevlemez, amely több virtuális gép létrehozásához használható, és most már használható megosztott lemezkép-verziók létrehozásához. |
| **Képgaléria** | Az Azure Marketplace-hez hasonlóan a **képgaléria** is egy tárház a képek kezeléséhez és megosztásához, de ön szabályozza, hogy ki rendelkezik hozzáféréssel. |
| **Képdefiníció** | A képek egy galérián belül vannak definiálva, és a képre és a belső használatra vonatkozó követelményekre vonatkozó információkat tartalmaznak. Ez magában foglalja, hogy a lemezkép Windows vagy Linux, kiadási megjegyzések, valamint minimális és maximális memóriakövetelmények. Ez egy fajta kép definíciója. |
| **Kép verziószáma** | A **lemezkép-verzió** az, amit a virtuális gép létrehozásához használ katalógus használatakor. A környezetnek szüksége van egy lemezkép több verziójára is. A felügyelt lemezképhez hasonlóan, amikor egy **lemezkép-verziót** használ a virtuális gép létrehozásához, a rendszerkép-verzió a virtuális gép új lemezeinek létrehozásához használatos. A képverziótöbbször is használható. |

Minden 20 virtuális gép, amely egyidejűleg létrehozott, azt javasoljuk, hogy egy replika. Ha például 120 virtuális gépet hoz létre egyidejűleg ugyanazon a régióban ugyanazt a lemezképet használva, javasoljuk, hogy legalább 6 replikáját tartsa meg a lemezkép. További információt a [Méretezés című](/azure/virtual-machines/windows/shared-image-galleries#scaling)témakörben talál.

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy meglévő felügyelt lemezképpel. Az oktatóanyag ot [követheti: Hozzon létre egy egyéni lemezképet egy Azure-beli virtuális gépről az Azure PowerShell használatával,](tutorial-custom-images.md) és szükség esetén hozzon létre egyet. Ha a felügyelt lemez adatlemezt tartalmaz, az adatlemez mérete nem lehet több 1 TB-nál.

Ebben a cikkben végzett munka során cserélje le az erőforráscsoport és a virtuális gép nevét, ahol szükséges.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Virtuális gépek létrehozása lemezképből

Miután a lemezkép verziója befejeződött, létrehozhat egy vagy több új virtuális gépet. A [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmag használata. 

Ebben a példában létrehoz egy *myVMfromImage*nevű virtuális gép, a *myResourceGroup* az *USA déli középső* adatközpontjában.


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>További lépések
[Az Azure Image Builder (előzetes verzió)](image-builder-overview.md) segítségével automatizálhatja a lemezképek verziólétrehozását, akár egy meglévő lemezkép-verzió frissítésére és [új lemezkép-verzió létrehozására](image-builder-gallery-update-image-version.md)is használható. 

A megosztott képtár erőforrást sablonok használatával is létrehozhatja. Számos Azure-gyorsindítási sablon áll rendelkezésre: 

- [Megosztott képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Képdefiníció létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Képverzió létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása a rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képgalériákról további információt az [Áttekintés című témakörben talál.](shared-image-galleries.md) Ha problémákba ütközik, olvassa el A [megosztott képgalériák hibaelhárítása című témakört.](troubleshooting-shared-images.md)

