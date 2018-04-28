---
title: Lapozófájl-kapacitás az operációs rendszer lemez egy Azure virtuális gép a PowerShell-lel |} Microsoft Docs
description: Módosítsa a PowerShell használata Azure virtuális gép által használt operációsrendszer-lemez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Módosítsa a PowerShell használatával, az Azure virtuális gép által használt operációsrendszer-lemez

Ha egy meglévő virtuális gép rendelkezik, de szeretne váltani a lemezt a biztonsági mentés lemezre vagy egy másik operációsrendszer-lemez, Azure PowerShell használatával az operációs rendszer lemezek. Törölje és hozza létre a virtuális gép nincs. Akkor is használhatja egy felügyelt lemezes egy másik erőforráscsoportban, mindaddig, amíg már nincs használatban.

A virtuális gép stopped\deallocated kell, majd az erőforrás-azonosítója a kezelt lemez az erőforrás-azonosítója egy másik felügyelt lemez lehet cserélni.

Győződjön meg arról, hogy a virtuális gép mérete és a tárolási típus kompatibilisek a csatlakoztatni kívánt lemezt. Például ha a lemez szeretné használni a prémium szintű Storage, majd a virtuális gép kell lennie (például a DS sorozatnak megfelelő méretűre) prémium szintű Storage képes. 

Az erőforrás csoport Using lemezek listáját [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Ha használni szeretné a lemez neve, állítsa be, amelyek az operációsrendszer-lemezképet a virtuális gép számára. A példa stop\deallocates nevű virtuális gép *myVM* és hozzárendeli a nevű lemez *newDisk* az új operációs rendszer lemezeként. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Következő lépések**

A lemez egy példányát létrehozásához lásd: [pillanatkép-lemez](snapshot-copy-managed-disk.md).