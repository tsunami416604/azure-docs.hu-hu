---
title: Azure spot virtuális gépek üzembe helyezése a PowerShell használatával
description: Megtudhatja, hogyan helyezhet üzembe helyszíni virtuális gépeket a költségek csökkentése érdekében a Azure PowerShell használatával.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158944"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Előzetes verzió: helyszíni virtuális gépek üzembe helyezése Azure PowerShell használatával


A [helyszíni virtuális gépek](spot-vms.md) használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. A `0.98765`érték például egy óránként $0,98765 USD maximális díj. Ha a maximális árat `-1`értékre állítja, a virtuális gép ára nem lesz kizárva. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

> [!IMPORTANT]
> A helyszíni példányok jelenleg nyilvános előzetes verzióban érhetők el.
> Ez az előzetes verzió nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy spotVM a [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) használatával a konfiguráció létrehozásához. `-Priority Spot` belefoglalása és `-MaxPrice` beállítása a következőkre:
- `-1`, hogy a virtuális gép árát a rendszer ne zárja ki.
- a dollár mennyisége, legfeljebb 5 számjegy. A `-MaxPrice .98765` például azt jelenti, hogy a virtuális gép fel lesz osztva, ha egy spotVM ára körülbelül 98765 USD-t mutat.


Ez a példa olyan spotVM hoz létre, amely nem lesz kiosztva a díjszabás alapján (csak akkor, ha az Azure-ban a kapacitás vissza van rendelve).

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

A virtuális gép létrehozása után a lekérdezéssel megtekintheti az erőforráscsoport összes virtuális gépe maximális árát.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Következő lépések

Az [Azure CLI](../linux/spot-cli.md) -vel vagy egy [sablonnal](../linux/spot-template.md)is létrehozhat egy direktszínt használó virtuális gépet.

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).