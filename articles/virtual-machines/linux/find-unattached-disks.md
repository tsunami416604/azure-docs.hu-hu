---
title: Keresse meg és nem csatolt Azure kezelt és nem kezelt lemezek törlése |} Microsoft Docs
description: Hogyan keresse meg és nem csatolt Azure kezelt és kezeletlen (VHD-k/lapblobokat) lemezek törlése az Azure parancssori felület használatával.
services: virtual-machines-linux
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: 1718b35aa68937cad4b3ae7e677e300506820bd0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30323253"
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: keresse meg és nem csatlakoztatott lemezek törlése 

Nem felügyelt lemezek, tárolt VHD-fájlok [lapblobokat](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) a [Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). A következő parancsfájl megkeresi választani nem felügyelt lemezek (lapblobokat) értéke által a **LeaseStatus** tulajdonság. Ha egy nem felügyelt lemezt egy virtuális gép csatlakozik a **LeaseStatus** tulajdonsága **zárolt**. Ha egy nem felügyelt lemezt választani, a **LeaseStatus** tulajdonsága **feloldva**. A parancsfájl megvizsgálja egy Azure-előfizetés az Azure storage-fiókok a nem felügyelt összes lemez. Ha a parancsfájl megkeresi a nem felügyelt lemezzel egy **LeaseStatus** tulajdonsága **feloldva**, a parancsfájl megállapítja, hogy a lemez választani.

>[!IMPORTANT]
>Először futtassa a parancsfájlt úgy, hogy a **deleteUnattachedVHDs** változó 0-ra. Ez a művelet lehetővé teszi a található, és minden nem csatolt nem felügyelt virtuális megtekintése.
>
>Az összes nem csatlakoztatott lemez áttekintése után futtassa újra a parancsfájlt, és állítsa be a **deleteUnattachedVHDs** változó 1. Ez a művelet lehetővé teszi minden nem csatolt nem felügyelt virtuális törlése.
>

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



