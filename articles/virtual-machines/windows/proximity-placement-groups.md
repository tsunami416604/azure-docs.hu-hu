---
title: 'PowerShell: Közelségi elhelyezési csoportok használata'
description: Ismerje meg a közelségelhelyezési csoportok létrehozását és használatát az Azure PowerShell használatával.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208525"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Virtuális gépek telepítése közelségi elhelyezési csoportokba a PowerShell használatával


Ahhoz, hogy a virtuális gépek a lehető legközelebb legyenek, és a lehető legalacsonyabb késést érjeel el, telepítenie kell őket egy [közelségelhelyezési csoporton](co-location.md#proximity-placement-groups)belül.

A közelségelhelyezési csoport egy logikai csoportosítás, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag egymáshoz közel helyezkednek el. A közelségelhelyezési csoportok olyan számítási feladatokhoz hasznosak, ahol az alacsony késés követelmény.


## <a name="create-a-proximity-placement-group"></a>Közelségi elhelyezési csoport létrehozása
Hozzon létre egy közelségelhelyezési csoportot a [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) parancsmag használatával. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Közelségi elhelyezési csoportok listázása

Az összes közelségelhelyezési csoportot a [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) parancsmag segítségével listázhatja.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép `-ProximityPlacementGroup $ppg.Id` a közelség elhelyezési csoport segítségével hivatkozhat a közelség elhelyezési csoport azonosítója, amikor a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) a virtuális gép létrehozásához.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

A [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)csoportban megtekintheti a virtuális gép ét.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Meglévő virtuális gép áthelyezése közelségi elhelyezési csoportba

Egy meglévő virtuális gép hozzáadása a közelség elhelyezési csoporthoz. Először le kell állítania a virtuális gép felszabadítását, majd frissítenie kell a virtuális gép és az újraindítás.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Meglévő virtuális gép áthelyezése közelségi elhelyezési csoportból

Virtuális gép eltávolítása a közelség elhelyezési csoportból, először le kell állítania\a virtuális gép felszabadítása, majd frissítse a virtuális gép és indítsa újra.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt `-ProximityPlacementGroup` a paramétert a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) parancsmaggal egy rendelkezésre állási csoport létrehozásához, és a rendelkezésre állási csoportban létrehozott összes virtuális gép ugyanabban a közelségelhelyezési csoportban jön létre.

Meglévő rendelkezésre állási csoport hozzáadása vagy eltávolítása egy közelségelhelyezési csoporthoz, először le kell állítania az összes virtuális gépet a rendelkezésre állási csoportban. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Meglévő rendelkezésre állási készlet áthelyezése közelségi elhelyezési csoportba

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Meglévő rendelkezésre állási készlet áthelyezése közelségelhelyezési csoportból

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Méretezési csoportok

Méretezési csoportot is létrehozhat a közelségi elhelyezési csoportban. Használja ugyanazt `-ProximityPlacementGroup` a paramétert [a New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) egy méretezési csoport létrehozásához, és az összes példány jön létre ugyanabban a közelség elhelyezési csoportban.


Ha meglévő méretezési csoportot szeretne hozzáadni vagy eltávolítani egy közelségi elhelyezési csoporthoz, először le kell állítania a méretezési csoportot. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Meglévő léptékkészlet áthelyezése közelségi elhelyezési csoportba

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Meglévő léptékkészlet áthelyezése közelségelhelyezési csoportból

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Az [Azure CLI](../linux/proximity-placement-groups.md) használatával is létrehozhat közelségi elhelyezési csoportokat.
