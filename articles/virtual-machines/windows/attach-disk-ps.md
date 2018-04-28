---
title: Adatlemez csatlakoztatása a Windows Azure-ban PowerShell használatával |} Microsoft Docs
description: Új vagy meglévő adatlemez csatolása egy Windows-VM PowerShell használata a Resource Manager üzembe helyezési modellel hogyan.
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
ms.date: 10/11/2017
ms.author: cynthn
ms.openlocfilehash: 708cf186267f25d0f22d71959b6aeceed643d536
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>Adatlemez csatlakoztatása egy Windows-VM PowerShell-lel

Ez a cikk bemutatja, hogyan új és meglévő lemez csatolása virtuális géphez Windows PowerShell használatával. 

Mielőtt ezt megtehetné, tekintse át az alábbi tippek:
* A virtuális gép mérete csatolhat hány adatlemezek szabályozza. További információkért lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* A prémium szintű storage, szüksége lesz egy prémium szintű Storage engedélyezve van, például a DS-méretek és GS sorozatnak virtuális gép Virtuálisgép-méretet. További információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Egy üres adatlemez hozzáadása a virtuális géphez

Ez a példa bemutatja, hogyan egy üres adatlemez hozzáadása egy meglévő virtuális gépet.

### <a name="using-managed-disks"></a>Felügyelt lemezekkel

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Felügyelt lemezek használatát egy rendelkezésre állási zóna
Hozhat létre a lemezt egy rendelkezésre állási zónában, [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) rendelkező a `-Zone` paraméter. Az alábbi példakód létrehozza a lemez zónában *1*.


```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```


### <a name="initialize-the-disk"></a>A lemez inicializálása

Üres lemez hozzáadása után kell inicializálnia. A lemez inicializálása, jelentkezzen be egy virtuális Gépet, és használja a Lemezkezelést. Vannak engedélyezve a Rendszerfelügyeleti webszolgáltatások és a tanúsítványt a virtuális gép létrehozása után, a távoli PowerShell használatával végezze el a lemez inicializálását. Egy egyéni parancsprogramok futtatására szolgáló bővítmény is használhatja: 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
A parancsfájl tartalmazza a kódot, hogy a lemezek inicializálása hasonlót:

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


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Adatok meglévő lemez csatolása a virtuális géphez

A virtuális gép egy meglévő felügyelt lemezes csatolhat adatok lemezként. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>További lépések

Hozzon létre egy [pillanatkép](snapshot-copy-managed-disk.md).
