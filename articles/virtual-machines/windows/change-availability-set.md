---
title: Virtuális gépek rendelkezésre állási csoportjának módosítása
description: Megtudhatja, hogyan módosíthatja a virtuális gép rendelkezésre állási csoportját Azure PowerShell használatával.
ms.service: virtual-machines
author: cynthn
ms.topic: article
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: e014dbe440f1b7baf39c290b8b7ab8fcaea0bada
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165855"
---
# <a name="change-the-availability-set-for-a-vm"></a>Virtuális gép rendelkezésre állási csoportjának módosítása
A következő lépések azt ismertetik, hogyan módosítható a virtuális gépek rendelkezésre állási csoportja a Azure PowerShell használatával. A virtuális gépeket csak akkor lehet felvenni a rendelkezésre állási csoportba, ha az létrejött. A rendelkezésre állási csoport módosításához törölnie kell, majd újra létre kell hoznia a virtuális gépet. 

Ez a cikk a Linux és a Windows rendszerű virtuális gépekre is vonatkozik.

Ez a cikk [Azure Cloud Shell](https://shell.azure.com/powershell) a 2/12/2019-es és az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) 1.2.0.

Ez a példa nem azt vizsgálja, hogy a virtuális gép csatlakoztatva van-e egy terheléselosztó számára. Ha a virtuális gép egy terheléselosztó számára van csatlakoztatva, frissítenie kell a parancsfájlt az adott eset kezelésére. 


## <a name="change-the-availability-set"></a>A rendelkezésre állási csoport módosítása 

Az alábbi parancsfájl egy példát mutat be a szükséges információk összegyűjtésére, az eredeti virtuális gép törlésére, majd egy új rendelkezésre állási csoportba való újbóli létrehozására.

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

Adjon hozzá további tárhelyet a virtuális géphez egy további [adatlemez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)hozzáadásával.

