---
title: A virtuálisgép-méretezési csoportok létrehozása a Windows Azure-ban |} Microsoft Docs
description: A Windows-alapú virtuális gépek használata a virtuálisgép-méretezési csoport egy magas rendelkezésre állású alkalmazás létrehozását és telepítését
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd573a90e49197d59e6228359f57fcd4cc3f69e2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Hozzon létre egy virtuálisgép-méretezési csoportban, és a magas rendelkezésre állású alkalmazás a Windows központi telepítése
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát skálázhatja manuálisan, vagy megadhat automatikus skálázási szabályokat is az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Ebben az oktatóanyagban egy virtuálisgép-méretezési csoportot fog létrehozni az Azure-ban. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával adja meg az IIS-webhelyek méretezése
> * Hozzon létre egy terheléselosztót a méretezési csoport
> * Virtuálisgép-méretezési csoport létrehozása
> * Példányok számának növelése vagy csökkentése méretezési csoportokban
> * Automatikus skálázási szabályok létrehozása

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell telepítéséhez és használatához a helyi választja, az oktatóanyag az Azure PowerShell 5.6 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="scale-set-overview"></a>Méretezési csoport – áttekintés
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoporton belüli virtuális gépek egy vagy több *elhelyezési csoportban* vannak elosztva a logikai meghibásodási és frissítési tartományok között. Ezek a csoportok a [rendelkezésre állási csoportokhoz](tutorial-availability-sets.md) hasonlóan azonos módon konfigurált virtuális gépekből állnak.

A virtuális gépek a méretezési csoportban igény szerint jönnek létre. Az automatikus skálázási szabályok megadásával beállíthatja, hogyan és mikor szeretne a virtuális gépeket hozzáadni vagy eltávolítani a méretezési csoportban. Ezek a szabályok alapján metrikák például CPU-terhelést, a memória használata vagy a hálózati forgalmat is elindíthatja.

A méretezési csoportok legfeljebb 1000 virtuális gépek támogatására képesek, ha Azure platformrendszerképet használnak. A jelentős telepítési követelményekkel rendelkező, illetve a virtuális gépek jelentős testreszabását igénylő számítási feladatok esetén [egyéni rendszerkép létrehozására lehet szükség a virtuális gépekhez](tutorial-custom-images.md). Egyéni rendszerkép használatakor egy méretezési csoportban legfeljebb 300 virtuális gépet hozhat létre.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A következő példa létrehoz egy *myScaleSet* nevű méretezési csoportot, amely a *Windows Server 2016 Datacenter* platformrendszerképet használja. A rendszer automatikusan létrehozza az Azure-beli hálózati erőforrásokat a virtuális hálózathoz, a nyilvános IP-címhez és a terheléselosztóhoz. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
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
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
A skálázási művelet beállításához szerezze be a nyilvános IP-címét a terheléselosztót, hogy [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Az alábbi példa beolvassa az IP-címek *myPublicIP* hozza létre a méretezési részeként:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Írja be a nyilvános IP-címet egy webböngészőbe. A webes alkalmazás jelenik meg, beleértve az állomásnevet, a virtuális gép, amelyek a terheléselosztó felé irányuló forgalom:

![Futó IIS-hely](./media/tutorial-create-vmss/running-iis-site.png)

Ha kíváncsi a méretezési csoport működésére, kényszerítse a webböngésző frissítését, és tekintse meg, miként osztja el a terheléselosztó a forgalmat az alkalmazást futtató összes virtuális gép között.


## <a name="management-tasks"></a>Felügyeleti feladatok
A méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Az Azure PowerShell ezen feladatok gyors lehetőséget kínál. Lássunk néhány gyakori feladatot.

### <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési csoportban futó virtuálisgép-példányok listájának megjelenítéséhez használja a [Get-AzureRmVssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parancsot az alábbi módon:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

Az alábbi példa kimenetében két virtuálisgép-példány látható a méretezési csoportban:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Az egy adott virtuálisgép-példánnyal kapcsolatos további információk megtekintéséhez adja hozzá az `-InstanceId` paramétert a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parancshoz. A következő példa információkat tekint meg az *1*-es számú virtuálisgép-példányról:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Virtuális gépek példányszámának növelése vagy csökkentése
Már van egy méretezési csoportban lévő példányok száma, használja a [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) és a lekérdezés *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Ezután manuálisan növeléséhez vagy csökkentéséhez tegye a következőket a méretezési készletben rendelkező virtuális gépek [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Az alábbi példában a méretezési csoport virtuális gépeinek számát *3*-ra állítjuk:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Ha a megadott számú a skála-példány frissítése néhány percet vesz be.


### <a name="configure-autoscale-rules"></a>Automatikus skálázási szabályok konfigurálása
Helyett a példányok száma manuálisan a skálázási skálázás beállításához automatikus skálázási szabályok határozza meg. Ezek a szabályok figyelik a méretezési csoportban futó példányokat, és az Ön által megadott mérőszámok, illetve küszöbértékek alapján lépnek működésbe. Az alábbi példában a szabály eggyel növeli a példányok számát, amikor az átlagos processzorhasználat 5 percnél hosszabb ideig meghaladja a 60%-ot. Az átlagos CPU-terhelést, majd alá esik 30 % egy 5 perces időszakon át, ha a példány méretezése a egy példánya:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
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
$myRuleScaleDown = New-AzureRmAutoscaleRule `
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
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
```

Az automatikus skálázás tervezésével kapcsolatban bővebben az [automatikus skálázás ajánlott eljárásait](/azure/architecture/best-practices/auto-scaling) ismertető cikkben tájékozódhat.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy virtuálisgép-méretezési csoportot. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával adja meg az IIS-webhelyek méretezése
> * Hozzon létre egy terheléselosztót a méretezési csoport
> * Virtuálisgép-méretezési csoport létrehozása
> * Példányok számának növelése vagy csökkentése méretezési csoportokban
> * Automatikus skálázási szabályok létrehozása

Folytassa a következő oktatóanyaggal, amely a virtuális gépekre vonatkozó terheléselosztási alapelveket ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása](tutorial-load-balancer.md)
