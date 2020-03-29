---
title: Nem csatlakoztatott Azure által felügyelt és nem felügyelt lemezek keresése és törlése
description: A nem csatlakoztatott Azure által felügyelt és nem felügyelt (VHD-k/lapblobok) lemezek megkeresése és törlése az Azure CLI használatával.
author: roygara
ms.service: virtual-machines
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6cf48c53e7b5c1cc8537abeda164460de66abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945152"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>A nem csatlakoztatott Azure által felügyelt és nem felügyelt lemezek keresése és törlése az Azure CLI használatával
Amikor töröl egy virtuális gépet (VM) az Azure-ban, alapértelmezés szerint a virtuális géphez csatlakoztatott lemezek nem törlődnek. Ez a funkció segít a virtuális gépek véletlen törlése miatti adatvesztés megelőzésében. A virtuális gép törlése után továbbra is fizetni fog a nem csatlakoztatott lemezekért. Ez a cikk bemutatja, hogyan keresheti meg és törölheti a nem csatlakoztatott lemezeket, és hogyan csökkentheti a felesleges költségeket. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: Nem csatlakoztatott lemezek keresése és törlése 

A következő parancsfájl a Felügyelt lemez értékének vizsgálatával keresi **a** nem csatlakoztatott [felügyelt lemezeket.](managed-disks-overview.md) Ha egy felügyelt lemez csatlakozik egy virtuális géphez, a **ManagedBy** tulajdonság tartalmazza a virtuális gép erőforrás-azonosítóját. Ha egy felügyelt lemez nincs csatlakoztatva, a **ManagedBy** tulajdonság null értékű. A parancsfájl megvizsgálja az Azure-előfizetés összes felügyelt lemezét. Amikor a parancsfájl olyan felügyelt lemezt talál meg, amelynek **ManagedBy** tulajdonságértéke null, a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a parancsfájlt a **deleteUnattachedDisks** változó 0-ra állításával. Ezzel a művelettel megkeresheti és megtekintheti az összes nem csatlakoztatott felügyelt lemezt.
>
>Miután áttekintette az összes nem csatlakoztatott lemezt, futtassa újra a parancsfájlt, és állítsa a **deleteUnattachedDisks változót** 1-re. Ez a művelet lehetővé teszi az összes nem csatlakoztatott felügyelt lemez törlését.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: Nem csatlakoztatott lemezek keresése és törlése 

A nem felügyelt lemezek olyan Virtuális merevlemez-fájlok, amelyek [lapblobként](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) tárolódnak az [Azure storage-fiókokban.](../../storage/common/storage-create-storage-account.md) A következő parancsfájl a nem csatlakoztatott nem felügyelt lemezeket (lapblobokat) keresi a **LeaseStatus** tulajdonság értékének vizsgálatával. Ha egy nem felügyelt lemez csatlakozik egy virtuális géphez, a **LeaseStatus** tulajdonság **zárolva**lesz. Ha egy nem felügyelt lemez nincs csatlakoztatva, a **LeaseStatus** tulajdonság **zárolása feloldva**lesz. A parancsfájl megvizsgálja az Azure-előfizetésben lévő összes Azure storage-fiók összes nem felügyelt lemezét. Ha a parancsfájl olyan nem felügyelt lemezt talál meg, amelynek **LeaseStatus** tulajdonsága **fel van oldva,** a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a parancsfájlt a **deleteUnattachedVHDs** változó 0-ra állításával. Ezzel a művelettel megkeresheti és megtekintheti az összes nem felügyelt virtuális gépazonosítót.
>
>Miután áttekintette az összes nem csatlakoztatott lemezt, futtassa újra a parancsfájlt, és állítsa a **deleteUnattachedVHDs** változót 1-re. Ez a művelet lehetővé teszi az összes nem felügyelt virtuális felhasználó törlését.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

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



