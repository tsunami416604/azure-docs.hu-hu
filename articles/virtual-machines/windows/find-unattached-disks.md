---
title: "Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése |} Microsoft Docs"
description: "Hogyan keresse meg és törölje a nem csatolt Azure kezelt és kezeletlen (VHD-k/lapblobokat) lemezek, Azure PowerShell használatával."
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
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése
Ha töröl egy virtuális gép az Azure-ban, a lemez nem csatlakoztatható alapértelmezés szerint nem törlődnek. Megakadályozza, hogy a véletlenül törli a virtuális gépek okozta adatvesztés, de továbbra is kell fizetnie a nem csatlakoztatott lemezek feleslegesen. Ez a cikk segítségével található és törli az összes nem csatlakoztatott lemez, és mentse költség. 


## <a name="find-and-delete-unattached-managed-disks"></a>Keresse meg és nem kezelt lemezek törlése 

Az alábbi parancsfájl bemutatja, hogyan található leválasztott [kezelt lemezek](managed-disks-overview.md) használatával *felügyeli* tulajdonság. Az összes kezelt lemez előfizetés és az ellenőrzések végighalad a *felügyeli* tulajdonság értéke null, nem kezelt lemezeken található. *Felügyeli* a tulajdonság tárolja az erőforrás-azonosítója a virtuális gép egy kezelt lemez csatolva van.

Erősen ajánlott, hogy első alkalommal történő futtatásakor a parancsfájl úgy, hogy a *deleteUnattachedDisks* változó 0-ra az összes nem csatlakoztatott lemez megtekintéséhez. Miután megtekintette a nem csatlakoztatott lemezeket, futtassa a parancsfájlt úgy, hogy *deleteUnattachedDisks* törli az összes nem csatlakoztatott lemez 1.

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Keresse meg és nem felügyelt nem csatlakoztatott lemezek törlése 

Nem felügyelt lemezek, mint [lapblobokat] tárolt (/ rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) VHD-fájlokat a [Azure Storage-fiókok](../../storage/common/storage-create-storage-account.md). Az alábbi parancsfájl bemutatja, hogyan keresése választani nem felügyelt lemezek (lapblobokat) segítségével a *LeaseStatus* tulajdonság. Azt a nem felügyelt lemezeket a tároló egy előfizetésben fiókok, és ellenőrzi, hogy minden végighalad a *LeaseStatus* tulajdonság nincs zárolva, nem csatolt nem felügyelt lemezek kereséséhez. *LeaseStatus* tulajdonság értéke a zárolt, ha egy nem kezelt lemez csatolva van egy virtuális gépet.

Erősen ajánlott, hogy első alkalommal történő futtatásakor a parancsfájl úgy, hogy a *deleteUnattachedVHDs* változó 0-ra az összes nem csatlakoztatott lemez megtekintéséhez. Miután megtekintette a nem csatlakoztatott lemezeket, futtassa a parancsfájlt úgy, hogy *deleteUnattachedVHDs* törli az összes nem csatlakoztatott lemez 1.


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




