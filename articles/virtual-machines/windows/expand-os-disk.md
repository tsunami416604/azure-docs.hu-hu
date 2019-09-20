---
title: Windows rendszerű virtuális gép operációsrendszer-meghajtójának kibontása az Azure-ban | Microsoft Docs
description: A Resource Manager-alapú üzemi modellben bontsa ki a virtuális gép operációsrendszer-meghajtójának méretét az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 692046070ffc04942a5d8a73825f6cb59e462f8b
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147210"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Virtuális gép operációsrendszer-meghajtójának kibontása

Amikor új virtuális gépet (VM) hoz létre egy erőforráscsoporthoz egy rendszerkép [Azure piactéren](https://azure.microsoft.com/marketplace/)való üzembe helyezésével, az alapértelmezett operációsrendszer-meghajtó gyakran 127 GB (a lemezképek alapértelmezés szerint kisebb operációsrendszer-lemezzel rendelkeznek). Bár hozzáadhat adatlemezeket a virtuális géphez (ezeknek száma a választott termékváltozattól függ), és ajánlott ezekre a kiegészítő lemezekre telepíteni az alkalmazásokat és a processzorigényes számítási feladatokat, az ügyfeleknek gyakran bővíteniük kell az operációs rendszer meghajtóját bizonyos forgatókönyvek támogatásához, mint például a következők:

- Az operációs rendszer meghajtójára összetevőket telepítő, régebbi alkalmazások támogatása.
- Nagyobb operációsrendszer-meghajtóval rendelkező fizikai számítógép vagy virtuális gép migrálása a helyszínről.


> [!IMPORTANT]
> Egy Azure-beli virtuális gép operációsrendszer-lemezének átméretezéséhez a virtuális gépet fel kell osztani.
>
> A lemezek bővítése után [ki kell bővíteni a kötetet az operációs rendszeren belül](#expand-the-volume-within-the-os) , hogy kihasználhassa a nagyobb lemezt.
> 


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="resize-a-managed-disk"></a>Felügyelt lemez átméretezése

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be Microsoft Azure-fiókjába erőforrás-kezelési módban, és válassza ki az előfizetését az alábbiak szerint:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Szerezze be a felügyelt operációsrendszer-lemezre mutató hivatkozást. Állítsa be a felügyelt operációsrendszer-lemez méretét a kívánt értékre, és frissítse a lemezt a következőképpen:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. Az operációsrendszer-lemezek esetében a maximálisan megengedett 2048 GB. (Lehetséges, hogy a VHD-blobot ezen a méreten túl szeretné kibontani, de az operációs rendszer csak az első 2048 GB területtel fog tudni működni.)
   > 
   > 
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Készen is van. Csatlakozzon RDP-kapcsolaton keresztül a virtuális géphez, nyissa meg a Számítógép-kezelés (vagy Lemezkezelés) elemet, és bővítse ki a meghajtót az újonnan kiosztott tárhellyel.

## <a name="resize-an-unmanaged-disk"></a>Nem felügyelt lemez átméretezése

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be Microsoft Azure-fiókjába erőforrás-kezelési módban, és válassza ki az előfizetését az alábbiak szerint:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Állítsa a nem felügyelt operációsrendszer-lemez méretét a kívánt értékre, és frissítse a virtuális gépet a következőképpen:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. Az operációsrendszer-lemezek esetében a maximálisan megengedett 2048 GB. (Lehetséges, hogy a VHD-blobot ezen a méreten túl szeretné kibontani, de az operációs rendszer csak az első 2048 GB területtel fog tudni működni.)
   > 
   > 
   
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>OPERÁCIÓSRENDSZER-lemez parancsfájljai

Alább látható a teljes parancsfájl a felügyelt és a nem felügyelt lemezekre vonatkozó referenciához:


**Felügyelt lemezek**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Nem felügyelt lemezek**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Adatlemezek átméretezése

Ez a cikk elsősorban a virtuális gép operációsrendszer-lemezének kibővítésére összpontosít, de a szkript használható a virtuális géphez csatlakoztatott adatlemezek bővítésére is. A virtuális géphez csatolt első adatlemez bővítéséhez például cserélje ki a `StorageProfile` `OSDisk` objektumát a `DataDisks` tömbre, és egy numerikus indexszel szerezzen be az első csatolt adatlemezre mutató hivatkozást az alább látható módon:

**Felügyelt lemez**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Nem felügyelt lemez**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Hasonlóképpen a virtuális géphez csatolt egyéb adatlemezekre is hivatkozhat, ha a fentiekben látható indexet vagy a lemez **Name (név** ) tulajdonságát használja:


**Felügyelt lemez**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nem felügyelt lemez**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>A kötet kibontása az operációs rendszeren belül

Miután kibontotta a virtuális gép lemezét, be kell lépnie az operációs rendszerbe, és ki kell bontania a kötetet, hogy az kiterjedjen az új területre. A partíciók kibővítésének számos módja van. Ez a szakasz a virtuális gép RDP-kapcsolaton keresztüli csatlakoztatását ismerteti a partíció a **DiskPart**használatával történő kibontásához.

1. Nyisson meg egy RDP-kapcsolat a virtuális géppel.

2.  Nyisson meg egy parancssort, és írja be a **DiskPart**parancsot.

2.  A **DiskPart** parancssorba írja be `list volume`a következőt:. Jegyezze fel a kiterjeszteni kívánt kötetet.

3.  A **DiskPart** parancssorba írja be `select volume <volumenumber>`a következőt:. Itt választhatja ki, hogy milyen kötetre kívánja kiterjeszteni a *kötetszám* , hanem az ugyanazon a lemezen lévő üres helyet.

4.  A **DiskPart** parancssorba írja be `extend [size=<size>]`a következőt:. Ez kibővíti a kijelölt kötetet megabájtban (MB).


## <a name="next-steps"></a>További lépések

A [Azure Portal](attach-managed-disk-portal.md)használatával is csatolhat lemezeket.
