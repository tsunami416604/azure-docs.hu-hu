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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204636"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>parancssori felület

Növekményes pillanatképet is létrehozhat az Azure CLI-vel, az Azure CLI legújabb verziójára lesz szüksége. 

Windows rendszeren a következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux rendszeren a CLI telepítése az operációs rendszer verziójától függően eltérő lesz.  Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) az adott Linux-verzióra.

Növekményes pillanatkép létrehozásához használja az [az Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) `--incremental` paramétert.

A következő példa egy növekményes pillanatképet hoz létre, és lecseréli a `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>` `<exampleDiskName>` `<exampleLocation>` saját értékeit, majd futtatja a példát:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Az azonos lemezről származó növekményes pillanatképeket a `SourceResourceId` és a `SourceUniqueId` Pillanatképek tulajdonságaival is azonosíthatja. `SourceResourceId` a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId` az érték a `UniqueId` lemez tulajdonságában örökölt. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, a `UniqueId` tulajdonság módosul.

A `SourceResourceId` és az használatával `SourceUniqueId` létrehozhat egy adott lemezhez társított összes pillanatkép listáját. Az alábbi példa felsorolja az adott lemezzel kapcsolatos összes növekményes pillanatképet, de szükség van rá néhány beállításra.

Ez a példa jQ használ az adatlekérdezéshez. A példa futtatásához [telepítenie](https://stedolan.github.io/jq/download/)kell a jQ.

Cserélje le az `<yourResourceGroupNameHere>` `<exampleDiskName>` értékeket, és az értékeit használva a következő példával listázhatja a meglévő növekményes pillanatképeket, feltéve, hogy a jQ is telepítette:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
