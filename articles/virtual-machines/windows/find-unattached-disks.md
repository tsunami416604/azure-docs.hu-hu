---
title: "Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése |} Microsoft Docs"
description: "Hogyan keresse meg és nem csatolt Azure kezelt és kezeletlen (VHD-k/lapblobokat) lemezek törlése az Azure PowerShell használatával."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 15c2550472156d5c1f680af77df2fe771edf3444
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése
Ha töröl egy virtuális gép (VM), az Azure-ban alapértelmezés szerint a rendszer nem törli a virtuális géphez csatlakoztatott lemezzel. Ez a szolgáltatás segít megelőzni a véletlen törlés virtuális gépek okozta adatvesztés. A virtuális gép törlését követően továbbra is által nem csatlakoztatott lemezek díj ellenében. Ez a cikk bemutatja, hogyan található és a felesleges költségek csökkentéséhez és nem csatlakoztatott lemezek törlése. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Által kezelt lemezeken: keresse meg és nem csatlakoztatott lemezek törlése 

A következő parancsfájl megkeresi választani [által kezelt lemezeken](managed-disks-overview.md) értéke által a **felügyeli** tulajdonság. Ha egy felügyelt lemezes csatolva van egy virtuális Gépet, a **felügyeli** tulajdonsága tartalmazza az erőforrás-azonosítója a virtuális gép. Ha egy felügyelt lemezes választani, a **felügyeli** tulajdonsága null értékű. A parancsfájl megvizsgálja egy Azure-előfizetés az összes kezelt lemez. Ha a parancsfájl megkeresi a felügyelt lemezes a **felügyeli** tulajdonsága null, a parancsfájl megállapítja, hogy a lemez választani.

>[!IMPORTANT]
>Először futtassa a parancsfájlt úgy, hogy a **deleteUnattachedDisks** változó 0-ra. Ez a művelet lehetővé teszi a található, és megtekintheti az összes nem csatlakoztatott kezelt lemez.
>
>Az összes nem csatlakoztatott lemez áttekintése után futtassa újra a parancsfájlt, és állítsa be a **deleteUnattachedDisks** változó 1. Ez a művelet lehetővé teszi az összes nem csatlakoztatott kezelt lemez törlése.
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: keresse meg és nem csatlakoztatott lemezek törlése 

Nem felügyelt lemezek, tárolt VHD-fájlok [lapblobokat](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) a [Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). A következő parancsfájl megkeresi választani nem felügyelt lemezek (lapblobokat) értéke által a **LeaseStatus** tulajdonság. Ha egy nem felügyelt lemezt egy virtuális gép csatlakozik a **LeaseStatus** tulajdonsága **zárolt**. Ha egy nem felügyelt lemezt választani, a **LeaseStatus** tulajdonsága **feloldva**. A parancsfájl megvizsgálja egy Azure-előfizetés az Azure storage-fiókok a nem felügyelt összes lemez. Ha a parancsfájl megkeresi a nem felügyelt lemezzel egy **LeaseStatus** tulajdonsága **feloldva**, a parancsfájl megállapítja, hogy a lemez választani.

>[!IMPORTANT]
>Először futtassa a parancsfájlt úgy, hogy a **deleteUnattachedVHDs** változó 0-ra. Ez a művelet lehetővé teszi a található, és minden nem csatolt nem felügyelt virtuális megtekintése.
>
>Az összes nem csatlakoztatott lemez áttekintése után futtassa újra a parancsfájlt, és állítsa be a **deleteUnattachedVHDs** változó 1. Ez a művelet lehetővé teszi minden nem csatolt nem felügyelt virtuális törlése.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

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

[A tárfiók törlése](../../storage/common/storage-create-storage-account.md)




