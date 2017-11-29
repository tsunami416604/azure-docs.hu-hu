---
title: "Az az Azure PowerShell Azure lemezek kezelése |} Microsoft Docs"
description: "Útmutató – az az Azure PowerShell Azure lemezek kezelése"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 956f44068db8fe9c8c7a839a0ce80c19e2b2f11c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="manage-azure-disks-with-powershell"></a>Azure-lemezeket a PowerShell-lel kezelése

Az Azure virtuális gépek lemez használata a virtuális gépek operációs rendszer, az alkalmazások és az adatok tárolására. Virtuális gép létrehozásakor fontos válasszon ki egy lemez méretét és a konfiguráció megfelelő a várt munkaterhelés. Ez az oktatóanyag ismerteti, központi telepítésére és felügyeletére a virtuális gépek lemezei. További tudnivalók:

> [!div class="checklist"]
> * Az operációs rendszer és a ideiglenes lemezek
> * Adatlemezek
> * Standard és Premium lemezek
> * Lemez teljesítménye
> * Csatlakoztatása és adatlemezek előkészítése

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="default-azure-disks"></a>Azure-lemezeket alapértelmezett

Egy Azure virtuális gép létrehozásakor a rendszer automatikusan csatolja két lemez a virtuális gép az. 

**Operációsrendszer-lemez** -operációs rendszer lemezek is kell méretezni, legfeljebb 4 terabájt, és a virtuális gépek operációs rendszert futtatja.  Az operációs rendszer lemezének hozzá van rendelve egy meghajtóbetűjelét *c:* alapértelmezés szerint. A lemez az operációs rendszer lemezének konfigurációját gyorsítótárazása az operációs rendszer teljesítményének van optimalizálva. Az operációs rendszer lemezének **nem kell** alkalmazások vagy az adatok tárolására szolgál. Az alkalmazások és adatok használja az adatlemezt, amely a cikk későbbi részében részleteit.

**Ideiglenes lemez** -ideiglenes lemezek található SSD-meghajtó használatát a virtuális gépként Azure ugyanazon a gazdagépen. Ideiglenes lemezek magas performant és műveletek, például a ideiglenes adatfeldolgozási használható. Azonban a virtuális gép új gazdagépre helyezik át, ha eltávolítják egy ideiglenes lemezen tárolt adatokat. Az ideiglenes lemez mérete határozza meg a Virtuálisgép-méretet. Ideiglenes lemezek vannak hozzárendelve a meghajtóbetűjel *d:* alapértelmezés szerint.

### <a name="temporary-disk-sizes"></a>Ideiglenes mérete

| Típus | Virtuális gép mérete | Maximális ideiglenes lemez (GB) |
|----|----|----|
| [Általános célú](sizes-general.md) | A-D sorozat része | 800 |
| [Számításra optimalizált](sizes-compute.md) | F sorozat | 800 |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md) | D és G sorozat | 6144 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md) | L sorozat | 5630 |
| [GPU](sizes-gpu.md) | N sorozat | 1440 |
| [Magas teljesítmény](sizes-hpc.md) | A és H sorozat | 2000 |

## <a name="azure-data-disks"></a>Az Azure data lemezek

További adatlemezt felvehető alkalmazások telepítése és adatok tárolására. Az adatlemezek minden helyzetben, ahol van szükség az adatok tartósságát és rugalmas tárolási kell használni. Minden adat lemez 1 terabájtnál maximális kapacitása nem. A virtuális gép mérete határozza meg, hány adatlemezek csatolható a virtuális gépek. Minden virtuális gép vCPU, a két adatlemezek kapcsolható ki. 

### <a name="max-data-disks-per-vm"></a>Maximális adatlemezek virtuális gépenként

| Típus | Virtuális gép mérete | Maximális adatlemezek virtuális gépenként |
|----|----|----|
| [Általános célú](sizes-general.md) | A-D sorozat része | 32 |
| [Számításra optimalizált](sizes-compute.md) | F sorozat | 32 |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md) | D és G sorozat | 64 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md) | L sorozat | 64 |
| [GPU](sizes-gpu.md) | N sorozat | 48 |
| [Magas teljesítmény](sizes-hpc.md) | A és H sorozat | 32 |

## <a name="vm-disk-types"></a>Virtuális gép lemeztípusok

Azure két lemez biztosít.

### <a name="standard-disk"></a>Standard méretű lemez

A merevlemez-meghajtókra épülő Standard Storage költséghatékony tárolási megoldás, amely emellett jó teljesítményt nyújt. Standard lemezek ideális, ha a költséghatékony fejlesztői és munkaterhelés teszteléséhez.

### <a name="premium-disk"></a>Prémium szintű lemez

Premium lemezek SSD-alapú nagy teljesítményű, alacsony késésű lemez üzemelnek. Tökéletes éles munkaterhelést futtató virtuális gépekhez. Prémium szintű Storage támogatja a DS-méretek, DSv2-méretek, GS sorozatnak és FS sorozatú virtuális gépeket. Prémium szintű lemezekhez három típusa (P10, P20, P30, P40, P50) helyen, és a lemez mérete a lemez típusát határozza meg. Ha választja, a lemez méretét az érték felfelé kerekíti a következő típusra. Például, ha a mérete 128 GB alá a lemez típusát és fog esni P10, 129 512 P20, 512 P30, 2TB és P50 P40 4 TB-os. 

### <a name="premium-disk-performance"></a>Prémium szintű lemez teljesítménye

|Prémium szintű storage lemeztípus | P10 | P20 | P30 |
| --- | --- | --- | --- |
| A lemezméret (kerek mentése) | 128 GB | 512 GB | 1024 GB (1 TB) |
| IOPS-érték lemezenként | 500 | 2,300 | 5,000 |
Adattovábbítás lemezenként | 100 MB/s | 150 MB/s | 200 MB/s |

A fenti táblázat azonosítja a maximális iops-érték lemezenként, magasabb szintű teljesítmény szerint több adatlemezek szétosztott érhető el. Például a 64 adatlemezek Standard_GS5 virtuális géphez csatlakoztatható. Ha ezeknek a lemezeknek mindegyikének, egy P30 mérete, 80000 IOPS maximális érhető el. A virtuális gép maximális iops-értéket részletes információkért lásd: [VM meg kell adnia, és méretének](./sizes.md).

## <a name="create-and-attach-disks"></a>Hozzon létre, és csatlakoztassa a lemezeket

A példa az oktatóanyag elvégzéséhez rendelkeznie kell egy meglévő virtuális gépet. Ha szükséges, ez [parancsfájl minta](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) hozhat létre egyet. Az oktatóanyag lépéseinek működő cseréje esetén a az erőforráscsoportot és a virtuális gép nevét, amennyiben szükséges.

A kezdeti konfiguráció létrehozása [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). A következő példa konfigurál egy lemezt, amely 128 GB-nál.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

Az adatok lemez létrehozása a [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) parancsot.

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

A virtuális gépet, amely hozzá szeretné adni a adatlemez az beszerzése a [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) parancsot.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Az adatok lemez hozzáadása a virtuálisgép-konfigurációnak a [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) parancsot.

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

A virtuális gép frissítése a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) parancsot.

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>Az adatlemezek előkészítése

Ha egy lemezt a virtuális géphez van csatolva, az operációs rendszer kell megadni a lemez használata. A következő példa bemutatja, hogyan kézzel konfigurálásához az első lemezt a virtuális gép hozzá. Ez a folyamat használatával is automatizálhatók a [egyéni parancsprogramok futtatására szolgáló bővítmény](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Kézi konfigurálás

A virtuális gép RDP-kapcsolat létrehozásához. Nyissa meg a PowerShell és a parancsfájl futtatásával.

```azurepowershell-interactive
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte méretű lemezek témakörök, mint:

> [!div class="checklist"]
> * Az operációs rendszer és a ideiglenes lemezek
> * Adatlemezek
> * Standard és Premium lemezek
> * Lemez teljesítménye
> * Csatlakoztatása és adatlemezek előkészítése

Továbblépés a következő oktatóanyag automatizálása a Virtuálisgép-konfiguráció megismeréséhez.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
