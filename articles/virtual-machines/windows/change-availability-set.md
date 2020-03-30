---
title: Virtuális gépek rendelkezésre állási készletének módosítása
description: Ismerje meg, hogyan módosíthatja a virtuális gép rendelkezésre állási készletét az Azure PowerShell használatával.
ms.service: virtual-machines
author: cynthn
ms.topic: article
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 092dafff6622d3402322eb96d0fe4215e52e16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964923"
---
# <a name="change-the-availability-set-for-a-vm"></a>Virtuális gép rendelkezésre állási csoportjának módosítása
Az alábbi lépések ismertetik, hogyan módosíthatja a rendelkezésre állási készlet egy virtuális gép az Azure PowerShell használatával. Virtuális gép csak akkor adható hozzá egy rendelkezésre állási csoport hozlétre. A rendelkezésre állási csoport módosításához törölnie kell, majd újra létre kell hoznia a virtuális gépet. 

Ez a cikk linuxos és windowsos virtuális gépekre egyaránt vonatkozik.

Ezt a cikket utoljára 2019.02.12-én teszteltük az [Azure Cloud Shell](https://shell.azure.com/powershell) és az Az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) 1.2.0-s verziójával.


## <a name="change-the-availability-set"></a>Az elérhetőségi készlet módosítása 

A következő parancsfájl egy példa a szükséges információk összegyűjtésére, az eredeti virtuális gép törlésére, majd egy új rendelkezésre állási csoportban való újbóli létrehozására.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>További lépések

További tárhely hozzáadása a virtuális géphez egy további [adatlemez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)hozzáadásával.

