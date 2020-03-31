---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299455"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Támogatott régiók
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Az Azure PowerShell segítségével növekményes pillanatképet hozhat létre. Szüksége lesz az Azure PowerShell legújabb verziójára, a következő parancs vagy telepíti, vagy frissíti a meglévő telepítést a legújabbra:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Ha ez telepítve van, jelentkezzen be `az login`a PowerShell-munkamenetbe a segítségével.

Növekményes pillanatkép létrehozásához az Azure PowerShell, állítsa `-Incremental` be a konfigurációt a [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) a paraméterrel, és adja át, hogy a változó a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) a `-Snapshot` paraméteren keresztül.

Cserélje `<yourDiskNameHere>` `<yourResourceGroupNameHere>`le `<yourDesiredSnapShotNameHere>` a , és az értékekre, majd a következő parancsfájl segítségével hozzon létre egy növekményes pillanatképet:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Azonosíthatja a növekményes `SourceResourceId` pillanatképek ugyanabból a lemezről a és a `SourceUniqueId` pillanatképek tulajdonságait. `SourceResourceId`A szülőlemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId`A lemez `UniqueId` tulajdonságától örökölt érték. Ha töröl egy lemezt, majd új lemezt hoz létre ugyanazzal a névvel, a `UniqueId` tulajdonság értéke megváltozik.

Használhatja `SourceResourceId` és `SourceUniqueId` létrehozhatja az adott lemezhez társított összes pillanatkép listáját. Cserélje `<yourResourceGroupNameHere>` le az értéket, majd a következő példa segítségével sorolja fel a meglévő növekményes pillanatképek:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

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

Az Azure Resource Manager-sablonok használatával növekményes pillanatképet is létrehozhat. Győződjön meg arról, hogy az apiVersion **értéke 2019-03-01,** és hogy a növekményes tulajdonság is igaz. A következő kódrészlet egy példa arra, hogyan hozhat létre növekményes pillanatképet az Erőforrás-kezelő sablonokkal:

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

Ha a .NET használatával a növekményes pillanatképek különbözeti képességét bemutató mintakódot szeretne látni, olvassa el az [Azure felügyelt lemezek biztonsági másolata egy másik régióba a növekményes pillanatképek különbözeti képességével rendelkező témakört.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
