---
title: Windows virtuális gép operációsrendszer-meghajtójának bővítése Azure-ban
description: Az Erőforrás-kezelő telepítési modelljében az Azure Powershell használatával bővítheti egy virtuális gép operációsrendszer-meghajtójának méretét.
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
ms.openlocfilehash: c76f57d15cd4cbdad5ded3b7545aab4d57272a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033501"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Virtuális gép operációsrendszer-meghajtójának bővítése

Amikor egy erőforráscsoportban egy új virtuális gépet (VM) hoz létre egy lemezkép [azure Piactérről](https://azure.microsoft.com/marketplace/)való üzembe helyezésével, az alapértelmezett operációs rendszer meghajtója gyakran 127 GB (egyes lemezképek kisebb operációs rendszer lemezmérete alapértelmezés szerint). Bár hozzáadhat adatlemezeket a virtuális géphez (ezeknek száma a választott termékváltozattól függ), és ajánlott ezekre a kiegészítő lemezekre telepíteni az alkalmazásokat és a processzorigényes számítási feladatokat, az ügyfeleknek gyakran bővíteniük kell az operációs rendszer meghajtóját bizonyos forgatókönyvek támogatásához, mint például a következők:

- Az operációs rendszer meghajtójára összetevőket telepítő, régebbi alkalmazások támogatása.
- Nagyobb operációsrendszer-meghajtóval rendelkező fizikai számítógép vagy virtuális gép migrálása a helyszínről.


> [!IMPORTANT]
> Az Azure virtuális gép operációs rendszerlemezének átméretezése a virtuális gép felszabadítását igényli.
>
> A lemezek kibontása után ki kell [bővítenie a kötetet az operációs rendszeren belül,](#expand-the-volume-within-the-os) hogy kihasználja a nagyobb lemez előnyeit.
> 


 


## <a name="resize-a-managed-disk"></a>Felügyelt lemez átméretezése

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be Microsoft Azure-fiókjába erőforrás-kezelési módban, és válassza ki az előfizetést az alábbiak szerint:
   
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
5. Hivatkozás beszerzése a felügyelt operációsrendszer-lemezre. Állítsa be a felügyelt operációsrendszer-lemez méretét a kívánt értékre, és frissítse a lemezt az alábbiak szerint:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A maximálisan engedélyezett 2048 GB operációs rendszer lemezek. (Lehetőség van a virtuális merevlemez blobja ezen a méreten túlra is kiterjeszthető, de az operációs rendszer csak az első 2048 GB-os területtel fog tudni dolgozni.)
   > 
   > 
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Készen is van. Csatlakozzon RDP-kapcsolaton keresztül a virtuális géphez, nyissa meg a Számítógép-kezelés (vagy Lemezkezelés) elemet, és bővítse ki a meghajtót az újonnan kiosztott tárhellyel.

## <a name="resize-an-unmanaged-disk"></a>Nem felügyelt lemez átméretezése

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be Microsoft Azure-fiókjába erőforrás-kezelési módban, és válassza ki az előfizetést az alábbiak szerint:
   
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
5. Állítsa be a nem felügyelt operációsrendszer-lemez méretét a kívánt értékre, és frissítse a virtuális gép méretét az alábbiak szerint:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A maximálisan engedélyezett 2048 GB operációs rendszer lemezek. (Lehetőség van a virtuális merevlemez blobja ezen a méreten túlra is kiterjeszthető, de az operációs rendszer csak az első 2048 GB-os területtel fog tudni dolgozni.)
   > 
   > 
   
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Parancsfájlok operációsrendszer-lemezhez

Az alábbiakban a felügyelt és nem felügyelt lemezek hivatkozási parancsfájlját olvashatja:


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

Ez a cikk elsősorban a virtuális gép operációsrendszer-lemezének bővítésére összpontosít, de a parancsfájl a virtuális géphez csatlakoztatott adatlemezek bővítésére is használható. A virtuális géphez csatolt első adatlemez bővítéséhez például cserélje ki a `StorageProfile``OSDisk` objektumát a `DataDisks` tömbre, és egy numerikus indexszel szerezzen be az első csatolt adatlemezre mutató hivatkozást az alább látható módon:

**Felügyelt lemez**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Nem felügyelt lemez**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Hasonlóképpen hivatkozhat a virtuális géphez csatolt más adatlemezekre is, akár a fenti index használatával, akár a lemez **Name** tulajdonságával:


**Felügyelt lemez**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nem felügyelt lemez**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>A hangerő kibontása az operációs rendszeren belül

Miután bővítette a lemezt a virtuális gép, be kell mennie az operációs rendszer, és bontsa ki a kötetet, hogy magában foglalja az új helyet. A partíciók bővítésének számos módja van. Ez a szakasz a virtuális gép RDP-kapcsolaton keresztüli csatlakoztatását ismerteti a partíció DiskPart használatával történő **kibontásához.**

1. Nyisson meg egy RDP-kapcsolatot a virtuális géphez.

2.  Nyisson meg egy parancssort, és írja be **a diskpart parancsot.**

2.  A **DISKPART** parancssorba írja be a következőt: `list volume`. Jegyezze fel a kiterjeszteni kívánt mennyiséget.

3.  A **DISKPART** parancssorba írja be a következőt: `select volume <volumenumber>`. Ezzel kiválasztja azt a *kötetkötetszámot,* amelyet ugyanazon a lemezen lévő összefüggő, üres területre szeretne kiterjeszteni.

4.  A **DISKPART** parancssorba írja be a következőt: `extend [size=<size>]`. Ez kiterjeszti a kijelölt *kötetet méret* szerint megabájtban (MB).


## <a name="next-steps"></a>További lépések

Lemezeket is csatolhat az [Azure Portal használatával.](attach-managed-disk-portal.md)
