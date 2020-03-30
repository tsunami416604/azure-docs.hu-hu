---
title: A közelségelhelyezési csoportok előnézete a virtuálisgép-méretezési készletekhez
description: Ismerje meg a közelségelhelyezési csoportok létrehozását és használatát a Windows virtuálisgép-méretezési csoportokhoz az Azure-ban.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273617"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Előzetes verzió: Közelségi elhelyezési csoportok létrehozása és használata a PowerShell használatával

Ahhoz, hogy a virtuális gépek a lehető legközelebb legyenek, és a lehető legalacsonyabb késést érjeel el, a méretezési csoportot egy [közelségelhelyezési csoporton](co-location.md#preview-proximity-placement-groups)belül kell üzembe helyeznie.

A közelségelhelyezési csoport egy logikai csoportosítás, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag egymáshoz közel helyezkednek el. A közelségelhelyezési csoportok olyan számítási feladatokhoz hasznosak, ahol az alacsony késés követelmény.

> [!IMPORTANT]
> A közelségelhelyezési csoportok jelenleg nyilvános előzetes verzióban vannak.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
> Az előzetes verzió ban nem érhetők el közelségelhelyezési csoportok ezekben a régiókban: **Japán Kelet**, **Ausztrália Kelet** és **India Közép.**


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


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Hozzon létre egy skálát `-ProximityPlacementGroup $ppg.Id` a közelségelhelyezési csoportban a közelségelhelyezési csoport azonosítójára való hivatkozáshoz, amikor [a New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) használatával hozza létre a méretezési csoportot.

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

A példány az elhelyezési csoportban a [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)segítségével látható.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>További lépések

Az [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) használatával is létrehozhat közelségi elhelyezési csoportokat.
