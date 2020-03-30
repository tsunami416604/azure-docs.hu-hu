---
title: Adatlemez csatolása Windows virtuális géphez az Azure-ban a PowerShell használatával
description: Új vagy meglévő adatlemez csatolása Windows virtuális géphez a PowerShell használatával az Erőforrás-kezelő telepítési modelljével.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267754"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Adatlemez csatolása PowerShelllel rendelkező Windows virtuális géphez

Ez a cikk bemutatja, hogyan csatolhatja az új és a meglévő lemezeket egy Windows virtuális gép hez a PowerShell használatával. 

Először is, tekintse át ezeket a tippeket:

* A virtuális gép mérete határozza meg, hogy hány adatlemez csatlakoztatható. További információ: [Méretek a virtuális gépekhez.](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* A prémium szintű SSD-k használatához [prémium szintű, tárhelyre képes virtuális géptípusra](sizes-memory.md)van szükség, például a DS-sorozatú vagy a GS-sorozatú virtuális gépre.

Ez a cikk a PowerShellt használja az [Azure Cloud Shellen](https://docs.microsoft.com/azure/cloud-shell/overview)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **Próbálja ki** a kódblokk tetejéről.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Üres adatlemez hozzáadása virtuális géphez

Ez a példa bemutatja, hogyan adhat hozzá üres adatlemezt egy meglévő virtuális géphez.

### <a name="using-managed-disks"></a>Felügyelt lemezek használata

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Felügyelt lemezek használata rendelkezésre állási zónában

Ha egy rendelkezésre állási zónában szeretne lemezt `-Zone` létrehozni, használja a [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) függvényt a paraméterrel. A következő példa lemezt hoz létre az *1.*

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>A lemez inicializálása

Üres lemez hozzáadása után inicializálnia kell azt. A lemez inicializálásához jelentkezzen be egy virtuális gépre, és használja a lemezkezelés. Ha engedélyezte [a WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) és a tanúsítvány a virtuális gép létrehozásakor, a távoli PowerShell segítségével inicializálhatja a lemezt. Egyéni parancsfájl-bővítményt is használhat:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

A parancsfájl tartalmazhat kódot a lemezek inicializálásához, például:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Meglévő adatlemez csatolása virtuális géphez

Egy meglévő felügyelt lemezt adatlemezként csatolhat egy virtuális géphez.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>További lépések

A felügyelt lemezeket sablonok használatával is telepítheti. További információ: [Felügyelt lemezek használata az Azure Resource Manager-sablonokban](using-managed-disks-template-deployments.md) vagy a több adatlemez üzembe helyezéséhez szolgáló [rövid útmutató sablon.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)
