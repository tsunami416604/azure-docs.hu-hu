---
title: Nem csatlakoztatott Azure által felügyelt és nem felügyelt lemezek keresése és törlése
description: A nem csatlakoztatott Azure által felügyelt és nem felügyelt (VHD-k/lapblobok) lemezek megkeresése és törlése az Azure PowerShell használatával.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cac192186c91259a5573dc27442137729816991a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869606"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Nem csatlakoztatott Azure által felügyelt és nem felügyelt lemezek keresése és törlése

Amikor töröl egy virtuális gépet (VM) az Azure-ban, alapértelmezés szerint a virtuális géphez csatlakoztatott lemezek nem törlődnek. Ez a funkció segít a virtuális gépek véletlen törlése miatti adatvesztés megelőzésében. A virtuális gép törlése után továbbra is fizetni fog a nem csatlakoztatott lemezekért. Ez a cikk bemutatja, hogyan keresheti meg és törölheti a nem csatlakoztatott lemezeket, és hogyan csökkentheti a felesleges költségeket.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: Nem csatlakoztatott lemezek keresése és törlése

A következő parancsfájl a Felügyelt lemez értékének vizsgálatával keresi **a** nem csatlakoztatott [felügyelt lemezeket.](managed-disks-overview.md) Ha egy felügyelt lemez csatlakozik egy virtuális géphez, a **ManagedBy** tulajdonság tartalmazza a virtuális gép erőforrás-azonosítóját. Ha egy felügyelt lemez nincs csatlakoztatva, a **ManagedBy** tulajdonság null értékű. A parancsfájl megvizsgálja az Azure-előfizetés összes felügyelt lemezét. Amikor a parancsfájl olyan felügyelt lemezt talál meg, amelynek **ManagedBy** tulajdonságértéke null, a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a parancsfájlt a **deleteUnattachedDisks** változó 0-ra állításával. Ezzel a művelettel megkeresheti és megtekintheti az összes nem csatlakoztatott felügyelt lemezt.
>
>Miután áttekintette az összes nem csatlakoztatott lemezt, futtassa újra a parancsfájlt, és állítsa a **deleteUnattachedDisks változót** 1-re. Ez a művelet lehetővé teszi az összes nem csatlakoztatott felügyelt lemez törlését.

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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: Nem csatlakoztatott lemezek keresése és törlése

A nem felügyelt lemezek olyan Virtuális merevlemez-fájlok, amelyek [lapblobként](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) tárolódnak az [Azure storage-fiókokban.](../../storage/common/storage-create-storage-account.md) A következő parancsfájl a nem csatlakoztatott nem felügyelt lemezeket (lapblobokat) keresi a **LeaseStatus** tulajdonság értékének vizsgálatával. Ha egy nem felügyelt lemez csatlakozik egy virtuális géphez, a **LeaseStatus** tulajdonság **zárolva**lesz. Ha egy nem felügyelt lemez nincs csatlakoztatva, a **LeaseStatus** tulajdonság **zárolása feloldva**lesz. A parancsfájl megvizsgálja az Azure-előfizetésben lévő összes Azure storage-fiók összes nem felügyelt lemezét. Ha a parancsfájl olyan nem felügyelt lemezt talál meg, amelynek **LeaseStatus** tulajdonsága **fel van oldva,** a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a parancsfájlt a **deleteUnattachedVHDs** változó 0-ra állításával. Ezzel a művelettel megkeresheti és megtekintheti az összes nem felügyelt virtuális gépazonosítót.
>
>Miután áttekintette az összes nem csatlakoztatott lemezt, futtassa újra a parancsfájlt, és állítsa a **deleteUnattachedVHDs** változót 1-re. Ez a művelet lehetővé teszi az összes nem felügyelt virtuális felhasználó törlését.

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

További információ: [Tárfiók törlése](../../storage/common/storage-create-storage-account.md) és [Árva lemezek azonosítása a PowerShell használatával](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
