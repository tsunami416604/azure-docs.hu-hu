---
title: Az Azure-beli Windows virtuális gép átméretezése a PowerShell használatával |} A Microsoft Docs
description: Az Azure Powershell használatával a Resource Manager üzemi modellben létrehozott Windows virtuális gép átméretezése.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 6bd41115f586bf2969dacb772f097d84654f0306
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646132"
---
# <a name="resize-a-windows-vm"></a>Egy Windows virtuális gép átméretezése

Ez a cikk bemutatja, hogyan virtuális gép áthelyezése egy másik [Virtuálisgép-méret](sizes.md) Azure Powershell-lel.

Miután létrehozott egy virtuális gépet (VM), méretezheti a virtuális gép felfelé vagy lefelé a virtuális gép méretének módosításával. Bizonyos esetekben kell először felszabadítja a virtuális Gépet. Ez akkor fordulhat elő, ha az új méret nem érhető el a virtuális gép jelenleg futtató hardver-fürtön.

Ha a virtuális gép Premium szintű tárolást használ, győződjön meg arról, hogy a kiválasztott egy **s** méretét a Premium Storage-támogatás kéréséhez verzióját. Például választhatja, Standard_E4**s**_v3 standard E4 v3 helyett.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Nincs rendelkezésre állási csoportban a Windows virtuális gép átméretezése

Egyes változók értékét. Cserélje le az értékeket a saját adatait.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Virtuálisgép-méretek listájának a virtuális Gépet üzemeltető hardver-fürtön elérhető. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Ha a kívánt méretre szerepel a listán, a következő parancsokat a virtuális gép átméretezése. Ha a kívánt méret nem szerepel, folytassa a 3. lépés.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

Ha a kívánt méret nem szerepel, futtassa az alábbi parancsokat a virtuális gép felszabadítása méretezze át, és indítsa újra a virtuális Gépet. Cserélje le **<newVMsize>** a kívánt méretre.
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Bármely dinamikus a virtuális géphez rendelt IP-címek a virtuális gép felszabadítása kiadások. Az operációs rendszer és az adatlemezek nem érinti. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Windows virtuális gép egy rendelkezésre állási csoportban átméretezése

Ha egy virtuális Gépet egy rendelkezésre állási csoportban új mérete nem érhető el a jelenleg a virtuális Gépet üzemeltető hardver-fürtön, a rendelkezésre állási csoport virtuális gépeinek kell fel kell szabadítani a virtuális gép átméretezése. Emellett előfordulhat, hogy frissíteni szeretné a többi virtuális gép mérete a rendelkezésre állási csoportban, miután egy virtuális gép át lett méretezve. Méretezze át egy virtuális Gépet egy rendelkezésre állási csoportban, hajtsa végre az alábbi lépéseket.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Virtuálisgép-méretek listájának a virtuális Gépet üzemeltető hardver-fürtön elérhető. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Ha a listában a kívánt méretet, a következő parancsokat a virtuális gép átméretezése. Ha nem szerepel, lépjen a következő szakaszban.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Ha nem találja a kívánt méretre, továbbra is az alábbi lépéseket követve szabadítsa fel a rendelkezésre állási csoportban lévő összes virtuális gép, virtuális gépek átméretezése, és indítsa újra a csomópontokat.

A rendelkezésre állási csoport összes virtuális gép leállítására.
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Méretezze át, és indítsa újra a virtuális gépek rendelkezésre állási csoportban.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>További lépések

A további skálázhatóság érdekében több Virtuálisgép-példány futtatása és a horizontális felskálázás. További információkért lásd: [automatikus méretezése egy virtuálisgép-méretezési csoportban lévő Windows-gépek](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

