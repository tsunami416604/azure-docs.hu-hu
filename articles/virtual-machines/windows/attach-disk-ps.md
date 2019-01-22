---
title: Adatlemez csatolása az Azure-beli Windows virtuális gépekhez a PowerShell-lel |} A Microsoft Docs
description: Új vagy meglévő lemez csatolása Windows virtuális gép PowerShell használatával a Resource Manager üzemi modellel hogyan.
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
ms.date: 10/16/2018
ms.author: cynthn
ms.openlocfilehash: d606009ea378de4addb06f9123baf3282637cf85
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423374"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Adatlemez csatolása egy Windows virtuális géphez a PowerShell-lel

Ez a cikk bemutatja, hogyan új és meglévő lemez csatolása Windows virtuális gép PowerShell-lel. 

Először tekintse át a következő tippeket:
* A virtuális gép méretét szabályozza, hány adatlemez csatolható. További információkért lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Premium storage szolgáltatást használja a DS vagy GS sorozatú virtuális gépek például egy Premium Storage-kompatibilis virtuális gép típusa lesz szüksége. További információkért lásd: [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

A helyi telepítése és használata a PowerShell, az oktatóanyaghoz az Azure PowerShell-modul verzióját 6.0.0 vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha Ön helyileg futtatja a Powershellt, is kell futtatni `Connect-AzureRmAccount` kapcsolat létrehozása az Azure-ral.


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

Miután hozzáadott egy üres lemez, kell inicializálnia. A lemez inicializálása, jelentkezzen be a virtuális gép, és a Lemezkezelés eszközzel. Ha engedélyezte a [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) és a egy tanúsítványt a virtuális gép létrehozásakor adott meg, távoli PowerShell használatával inicializálja a lemezt. Egy egyéni szkriptbővítmény is használhatja: 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
A parancsfájl például inicializálja a lemezeket kódot tartalmazza:

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
