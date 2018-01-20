---
title: "Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése |} Microsoft Docs"
description: "Található, és nem csatolt Azure kezelt és kezeletlen (VHD-k/lapblobokat) lemezek törlése az Azure parancssori felület használatával"
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése
Ha töröl egy virtuális gép az Azure-ban, a lemez nem csatlakoztatható alapértelmezés szerint nem törlődnek. Megakadályozza, hogy a véletlenül törli a virtuális gépek okozta adatvesztés, de továbbra is kell fizetnie a nem csatlakoztatott lemezek feleslegesen. Ez a cikk segítségével található és törli az összes nem csatlakoztatott lemez, és mentse költség. 


## <a name="find-and-delete-unattached-managed-disks"></a>Keresse meg és nem kezelt lemezek törlése 

Az alábbi parancsfájl bemutatja, hogyan felügyeli tulajdonság használatával nem kezelt lemezeken található.  A felügyelt lévő összes lemez előfizetés végighalad, és ellenőrzi, hogy a *felügyeli* tulajdonság értéke null, nem kezelt lemezeken található. *Felügyeli* a tulajdonság tárolja az erőforrás-azonosítója a virtuális gép egy kezelt lemez csatolva van. 

Erősen ajánlott, hogy első alkalommal történő futtatásakor a parancsfájl úgy, hogy a *deleteUnattachedDisks* változó 0-ra az összes nem csatlakoztatott lemez megtekintéséhez. Miután megtekintette a nem csatlakoztatott lemezeket, futtassa a parancsfájlt úgy, hogy *deleteUnattachedDisks* törli az összes nem csatlakoztatott lemez 1.

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Keresse meg és nem felügyelt nem csatlakoztatott lemezek törlése 

Nem felügyelt lemezek tárolt VHD-fájlok [lapblobokat](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) a [Azure Storage-fiókok](../../storage/common/storage-create-storage-account.md). Az alábbi parancsfájl bemutatja, hogyan választani nem felügyelt lemezek (lapblobokat) található LeaseStatus tulajdonság használatával. Azt a nem felügyelt lemezeket a tároló egy előfizetésben fiókok, és ellenőrzi, hogy minden végighalad a *LeaseStatus* tulajdonság nincs zárolva, nem csatolt nem felügyelt lemezek kereséséhez. A *LeaseStatus* tulajdonság értéke a zárolt, ha egy nem kezelt lemez csatolva van egy virtuális gépet. 

Erősen ajánlott, hogy első alkalommal történő futtatásakor a parancsfájl úgy, hogy a *deleteUnattachedVHDs* változó 0-ra az összes nem csatlakoztatott lemez megtekintéséhez. Miután megtekintette a nem csatlakoztatott lemezeket, futtassa a parancsfájlt úgy, hogy *deleteUnattachedVHDs* törli az összes nem csatlakoztatott lemez 1.


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>További lépések

[A tárfiók törlése](../../storage/common/storage-create-storage-account.md)



