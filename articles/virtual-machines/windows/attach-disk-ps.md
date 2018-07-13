---
title: Adatlemez csatolása egy Windows virtuális géphez az Azure PowerShell-lel |} A Microsoft Docs
description: Új vagy meglévő adatlemez csatolása egy Windows virtuális géphez PowerShell használatával a Resource Manager üzemi modellel hogyan.
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
ms.openlocfilehash: 384203134d1588053f91b66d32e9b0bf1ec69306
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38680915"
---
# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>Adatlemez csatolása a Windows virtuális gép a PowerShell használatával

Ez a cikk bemutatja, hogyan új és meglévő lemez csatlakoztatása a virtuális gépek Windows PowerShell segítségével. 

Ennek végrehajtása előtt tekintse át a következő tippeket:
* A virtuális gép méretét szabályozza, hány adatlemez csatolható. További információkért lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Premium storage szolgáltatást használja kell egy prémium szintű Storage engedélyezve van a virtuális gép méretét, például a DS vagy GS sorozatú virtuális gépet. További információkért lásd: [Premium Storage: nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 6.0.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Üres adatlemez hozzáadása egy virtuális géphez

Ez a példa bemutatja, hogyan üres adatlemez hozzáadása egy meglévő virtuális gépet.

### <a name="using-managed-disks"></a>Felügyelt lemezek használata

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Felügyelt lemezek használata rendelkezésre állási zónában
Lemez létrehozása egy rendelkezésre állási zónában, használja a [New-azurermdiskconfig parancsmaghoz](/powershell/module/azurerm.compute/new-azurermdiskconfig) együtt a `-Zone` paraméter. Az alábbi példa létrehoz egy lemezt a zóna *1*.


```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```


### <a name="initialize-the-disk"></a>Inicializálja a lemezt

Miután hozzáadott egy üres lemez, inicializálnia kell. A lemez inicializálása, jelentkezzen be egy virtuális Gépre, és a Lemezkezelés eszközzel. Ha engedélyezte a Rendszerfelügyeleti webszolgáltatások és a egy tanúsítványt a virtuális gép létrehozásakor adott meg, a távoli PowerShell használatával inicializálja a lemezt. Egy egyéni szkriptbővítmény is használhatja: 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
A parancsfájl tartalmazza ezt a kódot, a lemezek inicializálása hasonló:

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


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Egy már meglévő adatlemez csatolása a virtuális géphez

Meglévő felügyelt lemez is csatlakoztatható egy virtuális géphez adatlemezként. 

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
