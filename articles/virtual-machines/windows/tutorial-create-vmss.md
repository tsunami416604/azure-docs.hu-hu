---
title: "A virtuálisgép-méretezési csoportok létrehozása a Windows Azure-ban |} Microsoft Docs"
description: "A Windows-alapú virtuális gépek használata a virtuálisgép-méretezési csoport egy magas rendelkezésre állású alkalmazás létrehozását és telepítését"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d8f161af7753d2cd93a8683e8a93128144b86079
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Hozzon létre egy virtuálisgép-méretezési csoportban, és a magas rendelkezésre állású alkalmazás a Windows központi telepítése
A virtuálisgép-méretezési csoport lehetővé teszi, telepítéséhez és kezeléséhez azonos, az automatikus skálázást virtuális gépek halmazát jelenti. A méretezési csoportban lévő virtuális gépek száma manuálisan méretezhető, vagy szabályokat definiálhat, például a Processzor, memória igény szerint vagy a hálózati forgalom erőforrás-használat alapján automatikus skálázást. Ebben az oktatóanyagban telepít egy virtuálisgép-méretezési beállítása az Azure-ban. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával adja meg az IIS-webhelyek méretezése
> * Hozzon létre egy terheléselosztót a méretezési csoport
> * Hozzon létre egy virtuálisgép-méretezési csoport
> * Növeli vagy csökkenti a méretezési csoportban lévő példányok száma
> * Automatikus skálázási szabályok létrehozása

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="scale-set-overview"></a>Méretezési készlet – áttekintés
A virtuálisgép-méretezési csoport lehetővé teszi, telepítéséhez és kezeléséhez azonos, az automatikus skálázást virtuális gépek halmazát jelenti. Virtuális gépek méretezési csoportban lévő egy vagy több hiba és a frissítési tartományok programot elosztott *elhelyezési csoportok*. Ezek a csoportok hasonló módon konfigurált virtuális gépek, hasonló [rendelkezésre állási készletek](tutorial-availability-sets.md).

Virtuális gépek méretezési csoportban lévő igény szerint jönnek létre. Megadhatja az automatikus skálázási szabályok, hogy hogyan és mikor virtuális gépek hozzáadásakor vagy eltávolításakor a méretezési készlet. Ezek a szabályok alapján metrikák például CPU-terhelést, a memória használata vagy a hálózati forgalmat is elindíthatja.

Skálázási készletekben legfeljebb 1000 virtuális gépek támogatása, ha az Azure platformon lemezképet használ. Olyan munkaterhelések esetén jelentős telepítés vagy a virtuális gép testreszabása követelmények, érdemes lehet [hozzon létre egy egyéni Virtuálisgép-lemezkép](tutorial-custom-images.md). 300 virtuális gépek egy méretezési állítható be, ha egyéni lemezkép használatával hozhat létre.


## <a name="create-an-app-to-scale"></a>Méretezési-alkalmazás létrehozása
A méretezési csoport létrehozása előtt hozzon létre egy erőforráscsoportot, a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupAutomate* a a *EastUS* helye:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupScaleSet -Location EastUS
```

Egy korábbi oktatóanyagban megtanulta, hogyan [automatizálásához Virtuálisgép-konfiguráció](tutorial-automate-vm-deployment.md) az egyéni parancsprogramok futtatására szolgáló bővítmény használatával. Hozzon létre egy méretezési készlet konfigurációja, akkor egy egyéni parancsprogramok futtatására szolgáló bővítmény telepítése és konfigurálása IIS alkalmazza:

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location EastUS `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
```

## <a name="create-scale-load-balancer"></a>Skála terheléselosztó létrehozása
Egy Azure terheléselosztó a réteg-4 (TCP, UDP) terheléselosztóhoz, amely a magas rendelkezésre állást biztosít azáltal, hogy a bejövő forgalom kifogástalan állapotú virtuális gépek között. Terheléselosztói állapotfigyelő mintavétel az egyes virtuális gépek egy adott portot figyeli, és csak osztja el a forgalmat egy operatív virtuális gépre. További információkért tekintse meg a következő oktatóanyag [betöltése Windows virtuális gépek egyenleg](tutorial-load-balancer.md).

Hozzon létre olyan terheléselosztóhoz, amely a nyilvános IP-címmel rendelkezik, majd továbbítja a webes forgalom 80-as porton:

```powershell
# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupScaleSet `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="create-a-scale-set"></a>Méretezési készlet létrehozása
Most hozzon létre egy virtuálisgép-méretezési állítható be [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a méretezési készletben elnevezett *myScaleSet*:

```powershell
# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword P@ssword! `
  -ComputerNamePrefix myVM

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myScaleSet `
  -VirtualMachineScaleSet $vmssConfig
```

Hozza létre és konfigurálja a méretezési készlet erőforrások és a virtuális gépek néhány percet vesz igénybe.


## <a name="test-your-app"></a>Az alkalmazás tesztelése
Tekintse meg az IIS-webhely működés közben, szerezze be a terheléselosztó a nyilvános IP-címe [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Az alábbi példa beolvassa az IP-címek *myPublicIP* hozza létre a méretezési részeként:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupScaleSet -Name myPublicIP | select IpAddress
```

Adja meg a nyilvános IP-címet egy webböngészőben. Az alkalmazás megjelenik, beleértve az állomásnevet, a virtuális gép, amelyek a terheléselosztó felé irányuló forgalom:

![Futó IIS-webhely](./media/tutorial-create-vmss/running-iis-site.png)

Tekintse meg a méretezési készletben működés közben, akkor is kényszerített frissítési a webböngészőt a terheléselosztó forgalom szét az alkalmazást futtató összes virtuális gép.


## <a name="management-tasks"></a>Felügyeleti feladatok
A méretezési életciklusa során szükség lehet egy vagy több felügyeleti feladatok futtatásához. Emellett érdemes lehet különböző életciklus-feladatokat automatizáló parancsfájlokat hozhatnak létre. Az Azure PowerShell ezen feladatok gyors lehetőséget kínál. Az alábbiakban néhány gyakori feladatot.

### <a name="view-vms-in-a-scale-set"></a>Nézet virtuális gépek méretezési csoportban lévő
A méretezési csoportban lévő rendszert futtató virtuális gépek listájának megtekintéséhez használja [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) az alábbiak szerint:

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Loop through the instanaces in your scale set
for ($i=1; $i -le ($scaleset.Sku.Capacity - 1); $i++) {
    Get-AzureRmVmssVM -ResourceGroupName myResourceGroupScaleSet `
      -VMScaleSetName myScaleSet `
      -InstanceId $i
}
```


### <a name="increase-or-decrease-vm-instances"></a>Növeli vagy csökkenti a Virtuálisgép-példányok
Már van egy méretezési csoportban lévő példányok száma, használja a [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) és a lekérdezés *sku.capacity*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -VMScaleSetName myScaleSet | `
    Select -ExpandProperty Sku
```

Ezután manuálisan növeléséhez vagy csökkentéséhez tegye a következőket a méretezési készletben rendelkező virtuális gépek [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Az alábbi példában a virtuális gépek számát beállítja a méretezés beállítása *5*:

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -Name myScaleSet `
    -VirtualMachineScaleSet $scaleset
```

Ha a megadott számú a skála-példány frissítése néhány percet vesz be.


### <a name="configure-autoscale-rules"></a>Automatikus skálázási szabályok konfigurálása
Helyett a példányok száma manuálisan a skálázási skálázás beállításához automatikus skálázási szabályok határozza meg. Ezek a szabályok a méretezési csoportban lévő példányok figyelése, és ennek megfelelően metrikák és adhat meg küszöbértékek alapján válaszol. Az alábbi példa méretezi a példányok száma, egy esetén az átlagos CPU-terhelést, nagyobb, mint 60 % 5 perc alatt. Az átlagos CPU-terhelést, majd alá esik 30 % 5 perc alatt, ha a példány méretezése a egy példánya:

```powershell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5 minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5 minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```

További tervezési információk használatára az automatikus skálázás: [automatikus skálázás gyakorlati tanácsok](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létre egy virtuálisgép-méretezési készlet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával adja meg az IIS-webhelyek méretezése
> * Hozzon létre egy terheléselosztót a méretezési csoport
> * Hozzon létre egy virtuálisgép-méretezési csoport
> * Növeli vagy csökkenti a méretezési csoportban lévő példányok száma
> * Automatikus skálázási szabályok létrehozása

A következő oktatóanyag további információt a hálózati terheléselosztást a virtuális gépek fogalmak továbblépés.

> [!div class="nextstepaction"]
> [Virtuális gépek terhelést elosztani](tutorial-load-balancer.md)
