---
title: Windows virtuális gép átméretezése az Azure-ban
description: Módosítsa az Azure virtuális gépekhez használt virtuális gép méretét.
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
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941724"
---
# <a name="resize-a-windows-vm"></a>Windows rendszerű virtuális gép átméretezése

Ez a cikk bemutatja, hogyan helyezhet át egy virtuális gép egy másik [virtuális gép mérete.](sizes.md)

Virtuális gép (VM) létrehozása után a virtuális gép fel- vagy leskálázásával a virtuális gép méretének módosításával. Bizonyos esetekben először fel kell osztania a virtuális gép. Ez akkor fordulhat elő, ha az új méret nem érhető el a virtuális gép jelenleg üzemeltető hardverfürtön.

Ha a virtuális gép prémium szintű storage-t használ, győződjön meg arról, hogy a prémium szintű storage-támogatás hoz egy s-verziót. **s** Válassza például Standard_E4 **_v3**Standard_E4_v3 helyett.

## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg a virtuális gép lapját.
1. A bal oldali menüben válassza a **Méret lehetőséget.**
1. Válasszon egy új méretet az elérhető méretek listájából, majd válassza az **Átméretezés lehetőséget.**


Ha a virtuális gép jelenleg fut, méretének módosítása miatt újra indul. A virtuális gép leállítása további méreteket tárhat fel.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>A PowerShell használatával nem egy rendelkezésre állási készletben lévő virtuális gép átméretezése

Állítson be néhány változót. Cserélje le az értékeket a saját adataira.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Sorolja fel a virtuális gép méretei, amelyek a hardver-fürtön, ahol a virtuális gép üzemelteti. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Ha a kívánt méret szerepel a listában, futtassa a következő parancsokat a virtuális gép átméretezéséhez. Ha a kívánt méret nem szerepel a listában, folytassa a 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Ha a kívánt méret nem szerepel a listában, futtassa a következő parancsokat a virtuális gép felszabadításához, méretezze át, és indítsa újra a virtuális gép. Cserélje le ** \<az újVMsize>** a kívánt méretre.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> A virtuális gép a virtuális géphez rendelt dinamikus IP-címeket adja fel. Az operációs rendszer és az adatlemezek nem érintettek. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Virtuális gép átméretezése a PowerShell használatával egy rendelkezésre állási csoportban

Ha egy rendelkezésre állási csoportban lévő virtuális gép új mérete nem érhető el a virtuális gépet jelenleg üzemeltető hardverfürtön, majd a rendelkezésre állási csoportban lévő összes virtuális gépet fel kell osztani a virtuális gép átméretezéséhez. Előfordulhat, hogy frissítenie kell a rendelkezésre állási csoportban lévő többi virtuális gép méretét is, miután egy virtuális gépet átméreteztek. Virtuális gép átméretezéséhez egy rendelkezésre állási csoportban hajtsa végre a következő lépéseket.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Sorolja fel a virtuális gép méretei, amelyek a hardver-fürtön, ahol a virtuális gép üzemelteti. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Ha a kívánt méret szerepel a listában, futtassa a következő parancsokat a virtuális gép átméretezéséhez. Ha nem szerepel a listában, lépjen a következő szakaszra.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Ha a kívánt méret nem szerepel a listában, folytassa az alábbi lépésekkel a rendelkezésre állási csoportösszes virtuális gépének felszabadításához, a virtuális gépek átméretezéséhez és újraindításához.

Állítsa le az összes virtuális gépet a rendelkezésre állási csoportban.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

A rendelkezésre állási csoportban átméretetheti és újraindíthatja a virtuális gépeket.
   
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

További méretezhetőség érdekében futtasson több virtuálisgép-példányt, és horizontális felskálázás. További információt a [Windows-gépek automatikus méretezése virtuálisgép-méretezési készletben](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)című témakörben talál.

