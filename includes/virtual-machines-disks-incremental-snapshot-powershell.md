---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 895a81df858e51a266cd87c96a161695a4bf2cc1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95992897"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Támogatott régiók
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Növekményes pillanatkép létrehozásához használhatja a Azure PowerShellt is. A Azure PowerShell legújabb verziójára lesz szüksége, a következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

A telepítését követően jelentkezzen be a PowerShell-munkamenetbe a következővel: `Connect-AzAccount` .

Ha Azure PowerShell használatával szeretne növekményes pillanatképet létrehozni, állítsa a konfigurációt a [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) `-Incremental` paraméterrel, majd adja át az értékét a [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) `-Snapshot` paraméterrel.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Az azonos lemezről származó növekményes pillanatképeket a `SourceResourceId` és a `SourceUniqueId` Pillanatképek tulajdonságaival is azonosíthatja. `SourceResourceId` a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId` az érték a `UniqueId` lemez tulajdonságában örökölt. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, a `UniqueId` tulajdonság módosul.

A `SourceResourceId` és az használatával `SourceUniqueId` létrehozhat egy adott lemezhez társított összes pillanatkép listáját. Cserélje le az `<yourResourceGroupNameHere>` értéket az értékre, majd a következő példa használatával sorolja fel a meglévő növekményes pillanatképeket:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager-sablon

Növekményes pillanatkép létrehozásához Azure Resource Manager sablonokat is használhat. Győződjön meg arról, hogy a apiVersion értéke **2019-03-01** , és hogy a növekményes tulajdonság értéke TRUE (igaz) is. A következő kódrészlet egy példa arra, hogyan hozhat létre növekményes pillanatképet Resource Manager-sablonokkal:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ha szeretné megtekinteni a növekményes Pillanatképek különbözeti képességét bemutató mintakód használatát, tekintse meg az [Azure-Managed Disks biztonsági mentések másolása másik régióba a növekményes Pillanatképek különbözeti képességével](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)című témakört.