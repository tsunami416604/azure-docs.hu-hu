---
title: "Rendelkezésre állási készletek oktatóanyag Windows virtuális gépek Azure-ban |} Microsoft Docs"
description: "További tudnivalók a rendelkezésre állási csoportok Windows virtuális gépek Azure-ban."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2345b434a51b768793c2dea4587dc0a49ab35b70
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-use-availability-sets"></a>Rendelkezésre állási készletek használata

Ebben az oktatóanyagban elsajátíthatja a rendelkezésre állása és megbízhatósága szempontjából a virtuális gép megoldások Azure-ban egy rendelkezésre állási készletek nevű képesség növelésére. Rendelkezésre állási készletek győződjön meg arról, hogy a virtuális gépek Azure-on telepít egy fürt csomópontja több elkülönített hardver különböző pontjain. Ez biztosítja, hogy az Azure hardveres vagy szoftveres hiba akkor fordul elő, ha csak a virtuális gépek alárendelt meghatározott érintett ezzel és, hogy a teljes megoldás továbbra is elérhető, és működik. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Hozzon létre egy virtuális gép rendelkezésre állási csoportba
> * Ellenőrizze a rendelkezésre álló Virtuálisgép-méretek
> * Az Azure Advisor ellenőrzése

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

Rendelkezésre állási csoport egy olyan logikai jellegű csoportosítását képességgel, győződjön meg arról, hogy a Virtuálisgép-erőforrások helyezi-e benne el különítve egymástól belül egy Azure-adatközpontban telepítésekor használhatja az Azure-ban. Azure biztosítja, hogy a virtuális gépek elhelyezésekor belül egy rendelkezésre állási készlet több fizikai kiszolgálón futtassa, például rackszekrények, a tárolási egység és a hálózati kapcsolók számítási. A hardver- vagy Azure szoftver hiba akkor fordul elő, ha a virtuális gépek csak egy részhalmazát érintett, és az alkalmazás általános mentése marad, és továbbra is fennáll, az ügyfelek számára elérhető legyen. Rendelkezésre állási csoportok egy alapvető funkció is, ha a használni kívánt megbízható felhőalapú megoldásokat.

Mérlegeljük, ahol például 4 előtér-webkiszolgáló, és 2 háttér-virtuális gép egy adatbázist az tipikus Virtuálisgép-alapú megoldás. Az Azure-szeretné a virtuális gépek telepítése előtt határozza meg a két rendelkezésre állási csoportok: rendelkezésre állási csoporthoz a webes réteghez és egy rendelkezésre állási készletét, az adatbázis-rétegből. Amikor létrehoz egy új virtuális Gépet, majd megadhatja a rendelkezésre állási csoportot paraméterként az VM létrehozása parancs, és az Azure automatikusan biztosítja, hogy a virtuális gépeket hoz létre belül a rendelkezésre álló készlet több fizikai hardver-erőforrások között vannak elszigetelt. Ha a fizikai hardverrel, az egyik webkiszolgálón vagy adatbázis-kiszolgáló virtuális gépeken futó probléma van, akkor tudja, hogy a webkiszolgáló és az adatbázis virtuális gépek más példánya futhat, mert másik hardveren.

Rendelkezésre állási készletek használata során megbízható, VM-alapú megoldások Azure-ban telepíteni szeretné.

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Rendelkezésre állási készlet használatával hozhat létre [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Ebben a példában mindkét, frissítés és a tartalék tartományok számának hivatott *2* esetében a rendelkezésre állási csoportot elnevezett *myAvailabilitySet* a a *myResourceGroupAvailability* erőforráscsoportot.

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Hozzon létre egy felügyelt rendelkezésre állási segítségével [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) rendelkező a **- sku igazítva** paraméter.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Hozzon létre virtuális gépek rendelkezésre állási csoportok belül

Virtuális gépek a rendelkezésre állási készletét, győződjön meg arról, hogy a hardver megfelelően vannak elosztva a belül kell létrehoznia. Rendelkezésre állási készlet létrehozása után nem lehet hozzáadni egy meglévő virtuális Gépre. 

A hardver egy helyen több frissítési tartományt és a tartalék tartományok tagolódik. Egy **frissítési tartomány** egy csoport a virtuális gépek és a mögöttes fizikai hardver, amely egy időben újra kell indítani. Virtuális gépek ugyanazon **tartalék tartomány** közös tárolási, valamint egy közös forrás- és hálózati kikapcsolás megosztani. 

Amikor hoz létre egy virtuális gép konfigurációs használatával [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) használja a `-AvailabilitySetId` paraméterrel adhatja meg a rendelkezésre állási csoport azonosítója.

Hozzon létre két virtuális gépek [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) a rendelkezésre állási csoport.

```azurepowershell-interactive
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig
    
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup `
    -Location eastus `
    -Name myNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAvailability `
    -SecurityRules $nsgRuleRDP
    
# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name mySubnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -ComputerName myVM$i `
        -Credential $cred `
        -VM $vm `
        -Windows `
        -EnableAutoUpdate `
        -ProvisionVMAgent
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

Hozzon létre, és mindkét virtuális gépek néhány percet vesz igénybe. Ha elkészült, akkor kell két virtuális gép a mögöttes hardver pontjain. 

Ha megnézi a rendelkezésre állási csoportot a portálon a erőforráscsoportok > myResourceGroupAvailability > myAvailabilitySet, tekintse meg a virtuális gépeket a 2 tartalék elosztott hogyan és tartományok frissíteni kell.

![Rendelkezésre állási csoportban, a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Ellenőrizze az elérhető Virtuálisgép-méretek 

A rendelkezésre állási csoportot később adhat hozzá további virtuális gépek, de milyen Virtuálisgép-méretek állnak rendelkezésre a hardver tudnia kell. Használjon [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) listázza az összes hardveren elérhető méretek fürtön a rendelkezésre állási csoport számára.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="check-azure-advisor"></a>Az Azure Advisor ellenőrzése 

Azure Advisor segítségével a virtuális gépek rendelkezésre állásának javítása érdekében módjairól további információért. Azure Advisor segít kövesse az ajánlott eljárások az Azure-környezetekhez optimalizálása érdekében. Az erőforrás-konfigurációhoz és használat telemetriai adatai elemzi, és az, amelyek javítják a költséghatékonyság, a teljesítmény, a magas rendelkezésre állású és az Azure-erőforrások biztonsági megoldások javasolja.

Jelentkezzen be a [Azure-portálon](https://portal.azure.com), jelölje be **további szolgáltatások**, és írja be **Advisor**. Az Advisor irányítópulton megjelenített személyre szabott javaslatok a kijelölt előfizetésben. További információkért lásd: [Ismerkedés az Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Hozzon létre egy virtuális gép rendelkezésre állási csoportba
> * Ellenőrizze a rendelkezésre álló Virtuálisgép-méretek
> * Az Azure Advisor ellenőrzése

Virtuálisgép-méretezési csoportok olvashat a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport létrehozása](tutorial-create-vmss.md)


