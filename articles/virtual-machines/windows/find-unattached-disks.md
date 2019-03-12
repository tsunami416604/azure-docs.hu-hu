---
title: Keresse meg és törölje a nem csatlakoztatott Azure felügyelt és nem felügyelt lemezek |} A Microsoft Docs
description: Hogyan keresheti meg és nem csatlakoztatott Azure felügyelt és nem felügyelt (VHD-k/lapblobok) lemezek törlése az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: cb52956afe085c076f0a9a7c2d6810f3def32e3f
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568957"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Keresse meg és törölje a nem csatlakoztatott Azure felügyelt és nem felügyelt lemezek

Ha töröl egy virtuális gépet (VM) az Azure-ban alapértelmezés szerint a rendszer nem törli a virtuális géphez csatolt összes lemezt. Ez a szolgáltatás segít megakadályozni az adatvesztést miatt a virtuális gépet a véletlen törlés. Virtuális gép törlését követően továbbra is fizetnie a leválasztott lemezeket. Ez a cikk bemutatja, hogyan kereshet és a felesleges költségek csökkentéséhez és minden nem csatlakoztatott lemezek törlése.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: Keresse meg és nem csatlakoztatott lemezek törlése

A következő parancsfájl keres nem csatolt [felügyelt lemezek](managed-disks-overview.md) értékét megvizsgálásával a **ManagedBy** tulajdonság. Ha egy felügyelt lemezt egy virtuális géphez van csatolva a **ManagedBy** tulajdonság tartalmazza a virtuális gép erőforrás-Azonosítóját. Ha egy felügyelt lemez nincs csatlakoztatva, a **ManagedBy** tulajdonság null értékű. A parancsfájl megvizsgálja a felügyelt lemezeket az Azure-előfizetéssel. Ha a parancsfájl megkeresi a felügyelt lemezzel a **ManagedBy** tulajdonság értéke null, a parancsfájl meghatározza, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy a **deleteUnattachedDisks** változó 0-ra. Ez a művelet lehetővé teszi a keresése, és megtekintheti az összes nem csatolt felügyelt lemezeket.
>
>A leválasztott lemezeket, áttekintése után futtassa a parancsprogramot, és állítsa be a **deleteUnattachedDisks** változó 1-re. Ez a művelet lehetővé teszi az összes nem csatolt felügyelt lemez törlése.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: Keresse meg és nem csatlakoztatott lemezek törlése

Nem felügyelt lemezek a VHD-fájlok formájában tárolt [lapblobokat](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) a [az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). A következő parancsfájl értékét vizsgálatával keres nem csatolt nem felügyelt lemezek (lapblobokhoz) az **LeaseStatus** tulajdonság. Ha nem felügyelt lemezt egy virtuális géphez van csatolva a **LeaseStatus** tulajdonsága **zárolt**. Ha egy nem felügyelt lemez nincs csatlakoztatva, a **LeaseStatus** tulajdonsága **feloldva**. A parancsfájl megvizsgálja a nem felügyelt lemezek az Azure-előfizetésben az Azure storage-fiókok. Ha a parancsfájl keresi meg a nem felügyelt lemezt egy **LeaseStatus** tulajdonság **feloldva**, a parancsfájl meghatározza, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy a **deleteUnattachedVHDs** változó 0-ra. Ez a művelet lehetővé teszi, hogy Ön feltárhatja, és megtekintheti az összes nem csatolt nem felügyelt virtuális merevlemezhez.
>
>A leválasztott lemezeket, áttekintése után futtassa a parancsprogramot, és állítsa be a **deleteUnattachedVHDs** változó 1-re. Ez a művelet lehetővé teszi a leválasztott nem felügyelt virtuális Merevlemezeik törlése.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

További információkért lásd: [tárfiók törlése](../../storage/common/storage-create-storage-account.md) és [azonosítása árva lemezek PowerShell használatával](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
