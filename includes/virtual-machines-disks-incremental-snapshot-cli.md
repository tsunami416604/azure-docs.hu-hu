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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343050"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>parancssori felület

Létrehozhat egy növekményes pillanatképet az Azure CLI-vel, szüksége lesz az Azure CLI legújabb verziójára. 

Windows rendszerben a következő parancs telepíti vagy frissíti a meglévő telepítést a legújabb verzióra:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux alatt a CLI telepítése az operációs rendszer verziójától függően változik.  Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) az adott Linux-verzióhoz.

Növekményes pillanatkép létrehozásához `--incremental` használja [az pillanatkép létrehozása](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) a paraméterrel.

A következő példa létrehoz egy `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`növekményes pillanatképet, a , a ,`<exampleDiskName>`és `<exampleLocation>` a saját értékeit, majd futtassa a példát:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Azonosíthatja a növekményes `SourceResourceId` pillanatképek ugyanabból a lemezről a és a `SourceUniqueId` pillanatképek tulajdonságait. `SourceResourceId`A szülőlemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId`A lemez `UniqueId` tulajdonságától örökölt érték. Ha töröl egy lemezt, majd új lemezt hoz létre ugyanazzal a névvel, a `UniqueId` tulajdonság értéke megváltozik.

Használhatja `SourceResourceId` és `SourceUniqueId` létrehozhatja az adott lemezhez társított összes pillanatkép listáját. A következő példa felsorolja az adott lemezhez társított összes növekményes pillanatképet, de némi beállítást igényel.

Ez a példa a jq az adatok lekérdezéséhez. A példa futtatásához [telepítenie](https://stedolan.github.io/jq/download/)kell a jq .

Cserélje `<yourResourceGroupNameHere>` `<exampleDiskName>` le és használja az értékeket, majd a következő példával listázhatja a meglévő növekményes pillanatképeket, feltéve, hogy telepítette a jq-t is:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
