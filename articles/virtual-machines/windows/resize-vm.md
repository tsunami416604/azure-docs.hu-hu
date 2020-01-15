---
title: Windows rendszerű virtuális gép átméretezése az Azure-ban
description: Az Azure-beli virtuális gépekhez használt virtuálisgép-méret módosítása.
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
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941724"
---
# <a name="resize-a-windows-vm"></a>Windows rendszerű virtuális gép átméretezése

Ez a cikk bemutatja, hogyan helyezhet át egy virtuális gépet egy másik virtuálisgép- [méretre](sizes.md).

Miután létrehozta a virtuális gépet (VM), a virtuálisgép-méret módosításával felfelé vagy lefelé méretezheti a virtuális gépet. Bizonyos esetekben először fel kell szabadítania a virtuális gépet. Ez akkor fordulhat elő, ha az új méret nem érhető el azon a hardveres fürtön, amely jelenleg a virtuális gépet üzemelteti.

Ha a virtuális gép Premium Storaget használ, győződjön meg arról, hogy a Premium Storage támogatásához **a méret egyik verzióját kell** választania. Válassza például a Standard_E4**s**_v3 a Standard_E4_v3 helyett.

## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure portált](https://portal.azure.com).
1. Nyissa meg a virtuális gép lapját.
1. A bal oldali menüben válassza a **méret**lehetőséget.
1. Válasszon új méretet az elérhető méretek listájából, majd válassza az **átméretezés**lehetőséget.


Ha a virtuális gép jelenleg fut, a méret módosításakor a rendszer újraindítást eredményez. A virtuális gép leállítása további méreteket jelezhet.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>A PowerShell használata a rendelkezésre állási csoportba nem tartozó virtuális gépek átméretezéséhez

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

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Virtuális gép átméretezése rendelkezésre állási csoportokban a PowerShell használatával

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

## <a name="next-steps"></a>Következő lépések

További méretezhetőség érdekében futtasson több virtuálisgép-példányt és horizontális felskálázást. További információkért lásd: Windows rendszerű [gépek automatikus méretezése virtuálisgép-méretezési csoportokban](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

