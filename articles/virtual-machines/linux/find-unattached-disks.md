---
title: Keresse meg és törölje a nem csatlakoztatott Azure felügyelt és nem felügyelt lemezek |} A Microsoft Docs
description: Hogyan keresheti meg és nem csatlakoztatott Azure felügyelt és nem felügyelt (VHD-k/lapblobok) lemezek törlése az Azure CLI használatával.
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
ms.component: disks
ms.openlocfilehash: 58a54b7e867b625adf76d6287d5067c326c80f17
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462793"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Keresse meg és törölje a nem csatlakoztatott Azure felügyelt és nem felügyelt lemezek
Ha töröl egy virtuális gépet (VM) az Azure-ban alapértelmezés szerint a rendszer nem törli a virtuális géphez csatolt összes lemezt. Ez a szolgáltatás segít megakadályozni az adatvesztést miatt a virtuális gépet a véletlen törlés. Virtuális gép törlését követően továbbra is fizetnie a leválasztott lemezeket. Ez a cikk bemutatja, hogyan kereshet és a felesleges költségek csökkentéséhez és minden nem csatlakoztatott lemezek törlése. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: Keresse meg és nem csatlakoztatott lemezek törlése 

A következő parancsfájl keres nem csatolt [felügyelt lemezek](managed-disks-overview.md) értékét megvizsgálásával a **ManagedBy** tulajdonság. Ha egy felügyelt lemezt egy virtuális géphez van csatolva a **ManagedBy** tulajdonság tartalmazza a virtuális gép erőforrás-Azonosítóját. Ha egy felügyelt lemez nincs csatlakoztatva, a **ManagedBy** tulajdonság null értékű. A parancsfájl megvizsgálja a felügyelt lemezeket az Azure-előfizetéssel. Ha a parancsfájl megkeresi a felügyelt lemezzel a **ManagedBy** tulajdonság értéke null, a parancsfájl meghatározza, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy a **deleteUnattachedDisks** változó 0-ra. Ez a művelet lehetővé teszi a keresése, és megtekintheti az összes nem csatolt felügyelt lemezeket.
>
>A leválasztott lemezeket, áttekintése után futtassa a parancsprogramot, és állítsa be a **deleteUnattachedDisks** változó 1-re. Ez a művelet lehetővé teszi az összes nem csatolt felügyelt lemez törlése.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: Keresse meg és nem csatlakoztatott lemezek törlése 

Nem felügyelt lemezek a VHD-fájlok formájában tárolt [lapblobokat](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) a [az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). A következő parancsfájl értékét vizsgálatával keres nem csatolt nem felügyelt lemezek (lapblobokhoz) az **LeaseStatus** tulajdonság. Ha nem felügyelt lemezt egy virtuális géphez van csatolva a **LeaseStatus** tulajdonsága **zárolt**. Ha egy nem felügyelt lemez nincs csatlakoztatva, a **LeaseStatus** tulajdonsága **feloldva**. A parancsfájl megvizsgálja a nem felügyelt lemezek az Azure-előfizetésben az Azure storage-fiókok. Ha a parancsfájl keresi meg a nem felügyelt lemezt egy **LeaseStatus** tulajdonság **feloldva**, a parancsfájl meghatározza, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy a **deleteUnattachedVHDs** változó 0-ra. Ez a művelet lehetővé teszi, hogy Ön feltárhatja, és megtekintheti az összes nem csatolt nem felügyelt virtuális merevlemezhez.
>
>A leválasztott lemezeket, áttekintése után futtassa a parancsprogramot, és állítsa be a **deleteUnattachedVHDs** változó 1-re. Ez a művelet lehetővé teszi a leválasztott nem felügyelt virtuális Merevlemezeik törlése.
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

[Tárfiók törlése](../../storage/common/storage-create-storage-account.md)



