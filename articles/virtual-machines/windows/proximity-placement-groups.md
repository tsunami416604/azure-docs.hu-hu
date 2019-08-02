---
title: Földrajzi elhelyezési csoportok használata Windows rendszerű virtuális gépekhez | Microsoft Docs
description: További információ az Azure-beli Windows rendszerű virtuális gépekhez elérhető közelségi csoportok létrehozásáról és használatáról.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 9e9b8dddf7f48fe672a37b0343d215e0735ed760
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302506"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>Előzetes verzió: Virtuális gépek üzembe helyezése a közeli elhelyezési csoportokban a PowerShell használatával


Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést [](co-location.md#preview-proximity-placement-groups)kell megvalósítania, egy közelségi elhelyezési csoporton belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.

> [!IMPORTANT]
> A közelségi elhelyezési csoportok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A közelségi elhelyezési csoportok ezekben a régiókban nem érhetők el az előzetes verzió alatt: **Kelet-japán**, **Kelet-Ausztrália** és **Közép-India**.


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

Hozzon létre egy virtuális gépet a közelségi elhelyezési csoportban a használatával `-ProximityPlacementGroup $ppg.Id` , hogy a virtuális gép létrehozásához használja a [AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) .

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
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt `-ProximityPlacementGroup` a paramétert a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) parancsmaggal, és hozzon létre egy rendelkezésre állási csoportot, és a rendelkezésre állási csoportban létrehozott összes virtuális gép ugyanazon a közelségi elhelyezési csoportban is létre lesz hozva.

## <a name="scale-sets"></a>Méretezési csoportok

Létrehozhat egy méretezési csoportot is a közelségi elhelyezési csoportban. A `-ProximityPlacementGroup` [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) ugyanezzel a paraméterrel hozzon létre egy méretezési csoportot, és az összes példány ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="next-steps"></a>További lépések

Az [Azure CLI](../linux/proximity-placement-groups.md) -vel közelségi elhelyezési csoportokat is létrehozhat.
