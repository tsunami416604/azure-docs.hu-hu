---
title: Windows rendszerű virtuális gép átméretezése a PowerShell használatával az Azure-ban | Microsoft Docs
description: A Resource Manager-alapú üzemi modellben létrehozott Windows rendszerű virtuális gépek átméretezése az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 1f5f8f3a315b894ab8bc972d36008b5bce85d8e7
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749244"
---
# <a name="resize-a-windows-vm"></a>Windows rendszerű virtuális gép átméretezése

Ez a cikk bemutatja, hogyan helyezhet át egy virtuális gépet egy másik virtuálisgép- [méretre](sizes.md) az Azure PowerShell használatával.

Miután létrehozta a virtuális gépet (VM), a virtuálisgép-méret módosításával felfelé vagy lefelé méretezheti a virtuális gépet. Bizonyos esetekben először fel kell szabadítania a virtuális gépet. Ez akkor fordulhat elő, ha az új méret nem érhető el azon a hardveres fürtön, amely jelenleg a virtuális gépet üzemelteti.

Ha a virtuális gép Premium Storaget használ, győződjön meg arról, hogy a Premium Storage támogatásához **a méret egyik verzióját kell** választania. Válassza például a Standard_E4**s**_v3 elemet a standard E4 v3 helyett.

 

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Rendelkezésre állási csoportba nem tartozó Windows rendszerű virtuális gép átméretezése

Állítson be néhány változót. Cserélje le az értékeket a saját adataira.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

A virtuális gép által üzemeltetett hardveres fürtön elérhető virtuálisgép-méretek listázása. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Ha a kívánt méret szerepel a felsorolásban, futtassa a következő parancsokat a virtuális gép átméretezéséhez. Ha a kívánt méret nem szerepel a listáján, folytassa a 3. lépéssel.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Ha a kívánt méret nem szerepel a felsorolásban, futtassa a következő parancsokat a virtuális gép felszabadításához, méretezze át, majd indítsa újra a virtuális gépet. Cserélje le **\<newVMsize >** a kívánt méretre.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> A virtuális gép felszabadításával felszabadítja a virtuális géphez hozzárendelt dinamikus IP-címeket. Az operációs rendszer és az adatlemezek nem érintettek. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Windows rendszerű virtuális gép átméretezése rendelkezésre állási csoportba

Ha egy rendelkezésre állási csoportba tartozó virtuális gép új mérete nem érhető el a virtuális gépet futtató hardveres fürtön, akkor a rendelkezésre állási csoportba tartozó összes virtuális gépet fel kell osztani a virtuális gép átméretezéséhez. Előfordulhat, hogy frissítenie kell más virtuális gépek méretét a rendelkezésre állási csoportba egy virtuális gép átméretezése után. A virtuális gép rendelkezésre állási csoportba való átméretezéséhez hajtsa végre a következő lépéseket.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

A virtuális gép által üzemeltetett hardveres fürtön elérhető virtuálisgép-méretek listázása. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Ha a kívánt méret szerepel a felsorolásban, futtassa a következő parancsokat a virtuális gép átméretezéséhez. Ha nem szerepel a felsorolásban, ugorjon a következő szakaszra.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Ha a kívánt méret nem szerepel a felsorolásban, folytassa a következő lépésekkel, hogy felszabadítsa a rendelkezésre állási csoportba tartozó összes virtuális gépet, méretezze át a virtuális gépeket, és indítsa újra őket.

Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Méretezze át és indítsa újra a virtuális gépeket a rendelkezésre állási csoporton belül.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>További lépések

További méretezhetőség érdekében futtasson több virtuálisgép-példányt és horizontális felskálázást. További információkért lásd: Windows rendszerű [gépek automatikus méretezése virtuálisgép-méretezési csoportokban](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

