---
title: A közelségi csoportok előzetes verziója virtuálisgép-méretezési csoportokhoz
description: További információ az Azure-beli Windows rendszerű virtuálisgép-méretezési csoportokhoz való közvetlen elhelyezési csoportok létrehozásáról és használatáról.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273617"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Előzetes verzió: közelítő elhelyezési csoportok létrehozása és használata a PowerShell-lel

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legalacsonyabb késés elérése érdekében a méretezési [csoportot egy közelségi elhelyezési csoportban](co-location.md#preview-proximity-placement-groups)helyezze üzembe.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.

> [!IMPORTANT]
> A közelségi elhelyezési csoportok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A közelségi elhelyezési csoportok ezekben a régiókban nem érhetők el az előzetes verzióban: **Kelet-japán**, **Kelet-Ausztrália** és **Közép-India**.


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


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Hozzon létre egy méretezést a közelségi elhelyezési csoportban a `-ProximityPlacementGroup $ppg.Id` használatával, ha a [AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) használatával szeretné létrehozni a méretezési csoportot, akkor a közelségi elhelyezési csoport azonosítóra kell hivatkoznia.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

A példányt a [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)használatával tekintheti meg az elhelyezési csoportban.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Következő lépések

Az [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) -vel közelségi elhelyezési csoportokat is létrehozhat.
