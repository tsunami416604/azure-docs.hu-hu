---
title: 'Oktatóanyag: Hozzon létre egy Windows virtuálisgép-méretezési készletet'
description: Megtudhatja, hogy az Azure PowerShell használatával hogyan hozhat létre és helyezhet üzembe magas rendelkezésre állású alkalmazást windowsos virtuális gépeken egy virtuálisgép-méretezési készlet használatával
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bf53b2777c5d1e4d774a9f5ee9df119a0deac9d9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75464972"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Oktatóanyag: Virtuálisgép-méretezési csoport létrehozása és magas rendelkezésre állású alkalmazás üzembe helyezése Windows rendszeren, az Azure PowerShell használatával
A virtuálisgép-méretezési készlet lehetővé teszi azonos, automatikus virtualgépek készletének üzembe helyezését és kezelését. A méretezési készletben lévő virtuális gépek számát manuálisan méretezheti. Az erőforrás-használat, a memóriaigény vagy a hálózati forgalom alapján automatikus skálázáshoz is definiálhat szabályokat. Ebben az oktatóanyagban üzembe helyezhet egy virtuálisgép-méretezési készletet az Azure-ban, és megtudhatja, hogyan:

> [!div class="checklist"]
> * IIS-hely skálázásának megadása az egyéni szkriptbővítménnyel
> * Terheléselosztó létrehozása a méretezési csoporthoz
> * Virtuálisgép-méretezési csoport létrehozása
> * Példányok számának növelése vagy csökkentése méretezési csoportokban
> * Automatikus skálázási szabályok létrehozása

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="scale-set-overview"></a>Méretezési csoport – áttekintés
A virtuálisgép-méretezési készlet lehetővé teszi azonos, automatikus virtualgépek készletének üzembe helyezését és kezelését. A méretezési csoporton belüli virtuális gépek egy vagy több *elhelyezési csoportban* vannak elosztva a logikai meghibásodási és frissítési tartományok között. Az elhelyezési csoportok hasonlóan konfigurált virtuális gépek csoportjai, hasonlóan a [rendelkezésre állási csoportokhoz.](tutorial-availability-sets.md)

A virtuális gépek a méretezési csoportban igény szerint jönnek létre. Az automatikus skálázási szabályok megadásával beállíthatja, hogyan és mikor szeretne a virtuális gépeket hozzáadni vagy eltávolítani a méretezési csoportban. Ezek a szabályok olyan metrikák alapján aktiválódhatnak, mint a CPU-terhelés, a memóriahasználat és a hálózati forgalom.

A méretezési csoportok legfeljebb 1000 virtuális gépek támogatására képesek, ha Azure platformrendszerképet használnak. A jelentős telepítési követelményekkel rendelkező, illetve a virtuális gépek jelentős testreszabását igénylő számítási feladatok esetén [egyéni rendszerkép létrehozására lehet szükség a virtuális gépekhez](tutorial-custom-images.md). Egyéni rendszerkép használatakor egy méretezési csoportban legfeljebb 300 virtuális gépet hozhat létre.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Hozzon létre egy virtuálisgép-méretezési készletet [az Új AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)segítségével. A következő példa létrehoz egy *myScaleSet* nevű méretezési csoportot, amely a *Windows Server 2016 Datacenter* platformrendszerképet használja. A rendszer automatikusan létrehozza az Azure-beli hálózati erőforrásokat a virtuális hálózathoz, a nyilvános IP-címhez és a terheléselosztóhoz. Amikor a rendszer kéri, beállíthatja saját rendszergazdai hitelesítő adatait a méretezési csoportban a virtuálisgép-példányok hoz:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="deploy-sample-application"></a>Mintaalkalmazás üzembe helyezése
A méretezési csoport teszteléséhez telepítsen egy alapszintű webalkalmazást. Az egyéni Azure-szkriptbővítménnyel egy olyan szkriptet tölthet le és futtathat, amely az IIS-t telepíti a virtuálisgép-példányokon. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. További információ: [Az egyéni szkriptbővítmény áttekintése](extensions-customscript.md).

Telepítsen egy alapszintű IIS-webkiszolgálót az egyéni szkriptbővítménnyel. Alkalmazza az IIS-t telepítő egyéni szkriptbővítményt a következő módon:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Forgalom engedélyezése az alkalmazáshoz

Az alapszintű webalkalmazáshoz való hozzáférés engedélyezéséhez hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) és [a New-AzNetworkSecurityGroup segítségével.](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) További információ: [Networking for Azure virtual machine scale sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
A méretezési csoport működés közbeni megtekintéséhez a [Get-AzPublicIPAddress segítségével](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress)kapja meg a terheléselosztó nyilvános IP-címét. A következő példa a méretezési csoport részeként létrehozott *myPublicIP* IP-címét jeleníti meg:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

Írja be a nyilvános IP-címet egy webböngészőbe. Ekkor megjelenik a webalkalmazás, és tartalmazza annak a virtuális gépnek a nevét, amelyhez a terheléselosztó forgalmat irányított:

![Futó IIS-hely](./media/tutorial-create-vmss/running-iis-site.png)

Ha kíváncsi a méretezési csoport működésére, kényszerítse a webböngésző frissítését, és tekintse meg, miként osztja el a terheléselosztó a forgalmat az alkalmazást futtató összes virtuális gép között.


## <a name="management-tasks"></a>Felügyeleti feladatok
A méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Az Azure PowerShell gyors módot kínál e feladatok elvégzéséhez. Lássunk néhány gyakori feladatot.

### <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési csoportban lévő virtuálisgép-példányok listájának megtekintéséhez használja a [Get-AzVmssVM-et](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm) az alábbiak szerint:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

Az alábbi példa kimenetében két virtuálisgép-példány látható a méretezési csoportban:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Egy adott virtuálisgép-példányra vonatkozó további `-InstanceId` információk megtekintéséhez adja hozzá a paramétert a [Get-AzVmssVM-hez.](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm) A következő példa információkat tekint meg az *1*-es számú virtuálisgép-példányról:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Virtuális gépek példányszámának növelése vagy csökkentése
A méretezési csoportban jelenleg használt példányok számát használja a [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) és a query *(sku.capacity) oldalon:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

Ezután manuálisan növelheti vagy csökkentheti a méretezési készletben lévő virtuális gépek számát az [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss)segítségével. Az alábbi példában a méretezési csoport virtuális gépeinek számát *3*-ra állítjuk:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

A méretezési csoportban található példányok megadott számának frissítése néhány percet vesz igénybe.


### <a name="configure-autoscale-rules"></a>Automatikus skálázási szabályok konfigurálása
A méretezési csoportban futó példányok számának manuális beállítása helyett automatikus skálázási szabályokat adhat meg. Ezek a szabályok figyelik a méretezési csoportban futó példányokat, és az Ön által megadott mérőszámok, illetve küszöbértékek alapján lépnek működésbe. Az alábbi példában a szabály eggyel növeli a példányok számát, amikor az átlagos processzorhasználat 5 percnél hosszabb ideig meghaladja a 60%-ot. Ha az átlagos processzorhasználat 5 percnél hosszabb ideig 30% alá csökken, a szabály eggyel csökkenti a példányok számát:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Az automatikus skálázás tervezésével kapcsolatban bővebben az [automatikus skálázás ajánlott eljárásait](/azure/architecture/best-practices/auto-scaling) ismertető cikkben tájékozódhat.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy virtuálisgép-méretezési csoportot. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * IIS-hely skálázásának megadása az egyéni szkriptbővítménnyel
> * Terheléselosztó létrehozása a méretezési csoporthoz
> * Virtuálisgép-méretezési csoport létrehozása
> * Példányok számának növelése vagy csökkentése méretezési csoportokban
> * Automatikus skálázási szabályok létrehozása

Folytassa a következő oktatóanyaggal, amely a virtuális gépekre vonatkozó terheléselosztási alapelveket ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása](tutorial-load-balancer.md)
