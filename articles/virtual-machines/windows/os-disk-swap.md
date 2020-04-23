---
title: Operációs rendszerlemez felcserélése Egy Azure-beli virtuális géphez a PowerShell segítségével"
description: Módosítsa az Azure virtuális gép által használt operációsrendszer-lemezt a PowerShell használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 566347414ffe707b1d68a61b00ba21d19ff2b1eb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869389"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Az Azure virtuális gép által használt operációsrendszer-lemez módosítása a PowerShell használatával

Ha rendelkezik egy meglévő virtuális gép, de szeretné cserélni a lemezt egy biztonsági másolat lemez vagy egy másik operációs rendszer lemez, az Azure PowerShell segítségével felcserélheti az operációs rendszer lemezek. Nem kell törölnie, és újra létre kell hoznia a virtuális gép. Egy felügyelt lemezt is használhat egy másik erőforráscsoportban, feltéve, hogy az még nincs használatban.

 

A virtuális gép nem kell állítani\felszabadított, majd az erőforrás-azonosító a felügyelt lemez lehet cserélni az erőforrás-azonosító egy másik felügyelt lemez.

Győződjön meg arról, hogy a virtuális gép mérete és a tároló típusa kompatibilis a csatlakoztatni kívánt lemezzel. Ha például a használni kívánt lemez a prémium szintű storage-ban van, akkor a virtuális gépnek képesnek kell lennie a prémium szintű storage-ra (például egy DS-sorozat méretére). Mindkét lemeznek azonos méretűnek kell lennie.

Erőforráscsoport lemezeinek listájának beszereznie a [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) segítségével

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Ha rendelkezik a használni kívánt lemez nevével, állítsa be azt a virtuális gép operációs rendszerlemezeként. Ez a példa stop\felszabadítja a virtuális gép nevű *myVM* és hozzárendeli a lemez nevű *newDisk* az új operációs rendszer lemez. 
 
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

**További lépések**

Lemez másolatának létrehozásához olvassa el [a Lemez pillanatképe](snapshot-copy-managed-disk.md)című témakört.
