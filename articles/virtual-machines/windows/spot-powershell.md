---
title: Az Azure Spot virtuális gépek üzembe helyezéséhez használja a PowerShellt
description: Ismerje meg, hogyan használhatja az Azure PowerShellt a direkt virtuális gépek üzembe helyezéséhez a költségek csökkentése érdekében.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158944"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Előzetes verzió: Azonnali virtuális gépek üzembe helyezése az Azure PowerShell használatával


A [spot virtuális gépek](spot-vms.md) használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek. Ezért a direkt virtuális gépek kiválóan szolgálnak olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A direkt virtuális gépek díjszabása változó, a régió és a termékváltozat alapján. További információ: VM-díjszabás [Linuxra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windowsra.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) A maximális ár beállításáról további információt a Azonnali virtuális gépek – árképzés című témakörben [talál.](spot-vms.md#pricing)

Lehetősége van arra, hogy állítsa be a maximális árat, amelyet hajlandó fizetni, óránként, a virtuális gép. A direktvirtuális gép maximális ára usa dollárban (USD) állítható be, legfeljebb 5 tizedesjegy használatával. Az érték `0.98765`például óránként $0,98765 USD max ár. Ha a maximális árat `-1`állítja be, a virtuális gép nem lesz kizárva az ár alapján. A virtuális gép ára az azonnali aktuális ár vagy egy szabványos virtuális gép ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta áll rendelkezésre.

> [!IMPORTANT]
> A direktpéldányok jelenleg nyilvános előzetes verzióban vannak.
> Ez az előzetes verzió éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>



## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy spotVM a [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) a konfiguráció létrehozásához. Tartalmazza, `-Priority Spot` `-MaxPrice` és állítsa be, hogy vagy:
- `-1`így a virtuális gép nem kilakoltatták az ár alapján.
- egy dollár összeg, legfeljebb 5 számjegy. Például `-MaxPrice .98765` azt jelenti, hogy a virtuális gép fellesz terhelve, ha egy spotVM ára óránként $.98765 lesz.


Ebben a példában létrehoz egy spotVM, amely nem lesz felszámítva alapján díjszabás (csak akkor, ha az Azure-nak szüksége van a kapacitás t).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

A virtuális gép létrehozása után lekérdezheti az erőforráscsoport összes virtuális gépének maximális árát.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>További lépések

Direktvm-et az [Azure CLI](../linux/spot-cli.md) vagy egy [sablon](../linux/spot-template.md)használatával is létrehozhat.

Ha hibát észlel, olvassa el a [Hibakódok című témakört.](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)