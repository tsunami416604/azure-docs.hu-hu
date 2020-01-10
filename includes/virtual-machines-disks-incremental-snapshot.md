---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5b9c1d294cd984ca3cf062d3b657239995e5908
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752415"
---
A növekményes Pillanatképek (előzetes verzió) olyan felügyelt lemezek időpontra vonatkozó biztonsági mentései, amelyek az utolsó pillanatkép óta csak az összes változást tartalmazzák. Ha egy növekményes pillanatkép letöltését vagy más módon történő használatát kísérli meg, a rendszer a teljes VHD-t használja. Ez az új képesség a felügyelt lemezek pillanatképei számára potenciálisan költséghatékony lehet, mivel már nem szükséges a teljes lemez tárolása az egyes Pillanatképek esetében, hacsak nem választja. A normál pillanatképekhez hasonlóan a növekményes Pillanatképek is használhatók teljes felügyelt lemezek létrehozásához, vagy rendszeres pillanatkép készítéséhez.

A növekményes pillanatfelvétel és a rendszeres Pillanatképek között néhány különbség van. A növekményes Pillanatképek mindig a standard HDD-tárolót használják, a lemez tárolási típusától függetlenül, míg a rendszeres Pillanatképek a prémium SSD-ket használhatják. Ha a virtuális gépek központi telepítésének vertikális felskálázásához Premium Storage rendszeresen pillanatképeket használ, javasoljuk, hogy egyéni rendszerképeket használjon a [megosztott lemezképek](../articles/virtual-machines/linux/shared-image-galleries.md)katalógusában a standard szintű tárolóban. Ezzel a megoldással nagyobb léptékben érhet el alacsonyabb költségeket. Emellett a növekményes Pillanatképek is jobb megbízhatóságot biztosítanak a [Zone-redundáns tárolással](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan a ZRS-t fogja használni. Ha a ZRS nem érhető el a régióban, akkor a pillanatkép alapértelmezett értéke [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Felülbírálhatja ezt a viselkedést, és kiválaszthat egyet manuálisan, de nem ajánlott.

A növekményes Pillanatképek egy különbözeti képességet is biztosítanak, amely egyedi módon elérhető a felügyelt lemezeken. Lehetővé teszik az azonos felügyelt lemezek két növekményes pillanatképének változását a blokk szintjére. Ezzel a képességgel csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.

## <a name="restrictions"></a>Korlátozások

- A növekményes Pillanatképek jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában, az USA középső régiójában, az USA nyugati középső régiójában, Közép-Kanadában és Észak-Európában érhetők el.
- A növekményes Pillanatképek jelenleg nem hozhatók létre a lemez méretének módosítása után.
- A növekményes Pillanatképek jelenleg nem helyezhetők át az előfizetések között.
- Jelenleg csak egy adott pillanatkép-családhoz tartozó SAS URI-k hozhatók létre akár öt pillanatképből.
- Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez.
- A lemezenként legfeljebb hét növekményes pillanatkép hozható létre 5 percenként.
- Egyetlen lemezhez összesen 200 növekményes pillanatkép hozható létre.

## <a name="powershell"></a>PowerShell

Növekményes pillanatkép létrehozásához használhatja a Azure PowerShellt is. A Azure PowerShell legújabb verziójára lesz szüksége, a következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

A telepítését követően jelentkezzen be a PowerShell-munkamenetbe `az login`használatával.

Ha Azure PowerShell használatával szeretne növekményes pillanatképet létrehozni, állítsa a konfigurációt a [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) értékre a `-Incremental` paraméterrel, majd adja át ezt változóként a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) a `-Snapshot` paraméterrel.

Cserélje le a `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`és `<yourDesiredSnapShotNameHere>` értékeket az értékekkel, majd a következő parancsfájllal hozzon létre növekményes pillanatképet:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Az azonos lemezről származó növekményes pillanatképeket a `SourceResourceId` és a pillanatképek `SourceUniqueId` tulajdonságaival is azonosíthatja. `SourceResourceId` a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId` a lemez `UniqueId` tulajdonságában örökölt érték. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, akkor a `UniqueId` tulajdonság értéke megváltozik.

A `SourceResourceId` és a `SourceUniqueId` használatával létrehozhat egy adott lemezhez társított összes pillanatképet tartalmazó listát. Cserélje le a `<yourResourceGroupNameHere>` értéket az értékre, majd a következő példa használatával sorolja fel a meglévő növekményes pillanatképeket:

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

## <a name="cli"></a>CLI

Növekményes pillanatképet is létrehozhat az Azure CLI-vel, az Azure CLI legújabb verziójára lesz szüksége. 

Windows rendszeren a következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux rendszeren a CLI telepítése az operációs rendszer verziójától függően eltérő lesz.  Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) az adott Linux-verzióra.

Növekményes pillanatkép létrehozásához használja az [az Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) és a `--incremental` paramétert.

Az alábbi példa egy növekményes pillanatképet hoz létre, és lecseréli `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`és `<exampleLocation>` a saját értékeire, majd futtatja a példát:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Az azonos lemezről származó növekményes pillanatképeket a `SourceResourceId` és a pillanatképek `SourceUniqueId` tulajdonságaival is azonosíthatja. `SourceResourceId` a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId` a lemez `UniqueId` tulajdonságában örökölt érték. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, akkor a `UniqueId` tulajdonság értéke megváltozik.

A `SourceResourceId` és a `SourceUniqueId` használatával létrehozhat egy adott lemezhez társított összes pillanatképet tartalmazó listát. Az alábbi példa felsorolja az adott lemezzel kapcsolatos összes növekményes pillanatképet, de szükség van rá néhány beállításra.

Ez a példa jQ használ az adatlekérdezéshez. A példa futtatásához [telepítenie](https://stedolan.github.io/jq/download/)kell a jQ.

Cserélje le a `<yourResourceGroupNameHere>` és a `<exampleDiskName>` értékeket az értékekre, majd a következő példa használatával listázhatja a meglévő növekményes pillanatképeket, feltéve, hogy a jQ is telepítette:

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

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtekinteni a növekményes Pillanatképek különbözeti képességét bemutató mintakód használatát, tekintse meg az [Azure-Managed Disks biztonsági mentések másolása másik régióba a növekményes Pillanatképek különbözeti képességével](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)című témakört.
