---
title: "Automatikus oszlopszélesség egy Windows Azure-ban a PowerShell használatával |} Microsoft Docs"
description: "A Resource Manager üzembe helyezési modellel, az Azure Powershell létrehozott Windows virtuális gépek méretét."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 742efd1496de9ce76b1e5636297ef30f546bd108
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="resize-a-windows-vm"></a>A Windows virtuális gép átméretezése
Ez a cikk bemutatja, hogyan méretezze át egy Windows virtuális Gépet, a Resource Manager üzembe helyezési modellel Azure Powershell használatával létrehozni.

Miután létrehozta a virtuális gép (VM), méretezhető a virtuális gép felfelé vagy lefelé a Virtuálisgép-méretet módosításával. Néhány esetben először a virtuális gép kell felszabadítani. Ez akkor fordulhat elő, ha új mérete nem érhető el a virtuális gép jelenleg üzemeltető hardver fürt.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Egy Windows virtuális gép nem a rendelkezésre állási csoportok átméretezése
1. A hardver fürt, ahol a virtuális gép tárolása elérhető Virtuálisgép-méretek listázása. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Ha a kívánt méretet, a következő parancsokat a virtuális gép átméretezésével. Ha nem jelenik meg a kívánt méretet, folytassa a 3. lépésre.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Ha a kívánt méretet nem szerepel, a következő parancsokat a virtuális gép felszabadítása méretezze át, és indítsa újra a virtuális Gépet.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> A virtuális Géphez rendelt dinamikus IP-címek a virtuális gép felszabadítása kiadását. Az operációsrendszer- és adatlemezek, nem érintettek. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Egy Windows virtuális gép rendelkezésre állási csoportba átméretezése
Ha egy virtuális gép rendelkezésre állási csoportba új mérete nem érhető el a virtuális Gépet tartalmazó hardver fürtön, majd a rendelkezésre állási csoport virtuális gépeinek kell átméretezni a virtuális gép felszabadítása. Is szükség lehet frissíteni a rendelkezésre állási csoportban, miután egy virtuális gép át lett méretezve más virtuális gépek méretét. A virtuális gépek rendelkezésre állási csoportba átméretezéséhez hajtsa végre az alábbi lépéseket.

1. A hardver fürt, ahol a virtuális gép tárolása elérhető Virtuálisgép-méretek listázása.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Ha a kívánt méretet, a következő parancsokat a virtuális gép átméretezésével. Ha nem szerepel, folytassa a 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Ha nem jelenik meg a kívánt méretet, folytassa a következő lépéseket a rendelkezésre állási csoport virtuális gépeinek felszabadítani, méretezze át a virtuális gépek és újra kell indítania őket.
4. Állítsa le a rendelkezésre állási csoportot az összes virtuális gépet.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Automatikus oszlopszélesség, és indítsa újra a virtuális gépek a rendelkezésre állási csoport.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Következő lépések
* További méretezhetőséget, a virtuális gép több példányának futtatása, és kiterjesztése. További információkért lásd: [automatikus méretezése a Windows-alapú gépek egy virtuálisgép-méretezési csoportban lévő](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

