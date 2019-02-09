---
title: A PowerShell használatával egy Azure virtuális gép lapozófájl-kapacitás az operációs rendszer lemez |} A Microsoft Docs
description: Az Azure PowerShell-lel virtuális gép által használt operációsrendszer-lemez módosítása
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
ms.openlocfilehash: 73aab0750d97981d6684d04415683435bbd28797
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980414"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>A PowerShell használata Azure virtuális gép által használt operációsrendszer-lemez módosítása

Ha rendelkezik egy meglévő virtuális Gépet, de szeretne váltani a lemezt egy biztonsági mentési lemez vagy egy másik operációsrendszer-lemez, az Azure PowerShell használatával az operációsrendszer-lemez felcserélése. Nem kell törölnie kell, és hozza létre újra a virtuális Gépet. Akkor is használhatja egy felügyelt lemezt egy másik erőforráscsoportban, mindaddig, amíg még nincs használatban.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

A virtuális gép stopped\deallocated kell, majd a felügyelt lemez az erőforrás-Azonosítójára kicserélhető a különböző felügyelt lemez az erőforrás-azonosítója.

Győződjön meg arról, hogy a lemez csatolásához kompatibilisek legyenek-e a virtuális gép méretét és a tároló típusa. Például ha a használni kívánt lemezt Premium Storage-ban, majd a virtuális gép kell lennie (például a DS sorozatú méretét) prémium szintű tárolás képes. 

Az erőforrás csoport-lemezek listája [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Ha a lemez, amely a használni kívánt nevére, állítja be, amelyek az operációsrendszer-lemez a virtuális gép számára. Ebben a példában stop\deallocates nevű virtuális Gépet *myVM* és hozzárendeli a nevű lemez *newDisk* az új operációsrendszer-lemezként. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Következő lépések**

Lemez másolatának létrehozásához lásd: [lemez pillanatképének elkészítése](snapshot-copy-managed-disk.md).