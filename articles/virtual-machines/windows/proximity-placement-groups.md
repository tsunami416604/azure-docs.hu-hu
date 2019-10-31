---
title: Földrajzi elhelyezési csoportok használata Windows rendszerű virtuális gépekhez
description: További információ az Azure-beli Windows rendszerű virtuális gépekhez elérhető közelségi csoportok létrehozásáról és használatáról.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171221"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Virtuális gépek üzembe helyezése a közeli elhelyezési csoportokban a PowerShell használatával


Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy [közelségi elhelyezési csoporton](co-location.md#proximity-placement-groups)belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


## <a name="create-a-proximity-placement-group"></a>Közelségi elhelyezési csoport létrehozása
Hozzon létre egy közelségi elhelyezési csoportot a [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) parancsmag használatával. 

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

## <a name="list-proximity-placement-groups"></a>Proximity elhelyezési csoportok listázása

A [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) parancsmag használatával listázhatja az összes közelségi elhelyezési csoportot.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a közelségi elhelyezési csoportban a `-ProximityPlacementGroup $ppg.Id` használatával, és tekintse meg az elhelyezési csoport AZONOSÍTÓját, amikor a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) használatával hozza létre a virtuális gépet.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

A virtuális gépet a [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)használatával tekintheti meg az Elhelyezés csoportban.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt a `-ProximityPlacementGroup` paramétert a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) parancsmaggal, és hozzon létre egy rendelkezésre állási csoportot, és a rendelkezésre állási csoportban létrehozott összes virtuális gép is ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="scale-sets"></a>Méretezési csoportok

Létrehozhat egy méretezési csoportot is a közelségi elhelyezési csoportban. Használja ugyanazt a `-ProximityPlacementGroup` paramétert a [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) használatával egy méretezési csoport létrehozásához, és az összes példány ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="next-steps"></a>Következő lépések

Az [Azure CLI](../linux/proximity-placement-groups.md) -vel közelségi elhelyezési csoportokat is létrehozhat.
