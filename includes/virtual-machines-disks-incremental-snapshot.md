---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ee8a711a867f8abdc831b0d1d9d0b504b1104955
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310128"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Növekményes pillanatkép (előzetes verzió) létrehozása a felügyelt lemezekhez

A növekményes Pillanatképek (előzetes verzió) olyan felügyelt lemezek időpontra vonatkozó biztonsági mentései, amelyek az utolsó pillanatkép óta csak az összes változást tartalmazzák. Ha egy növekményes pillanatkép letöltését vagy más módon történő használatát kísérli meg, a rendszer a teljes VHD-t használja. Ez az új képesség a felügyelt lemezek pillanatképei számára potenciálisan költséghatékony lehet, mivel már nem szükséges a teljes lemez tárolása az egyes Pillanatképek esetében, hacsak nem választja. A normál pillanatképekhez hasonlóan a növekményes Pillanatképek is használhatók teljes felügyelt lemezek létrehozásához, vagy rendszeres pillanatkép készítéséhez.

A növekményes pillanatfelvétel és a rendszeres Pillanatképek között néhány különbség van. A növekményes Pillanatképek mindig a standard HDD-tárolót használják, a lemez tárolási típusától függetlenül, míg a rendszeres Pillanatképek a prémium SSD-ket használhatják. Ha a virtuális gépek központi telepítésének vertikális felskálázásához Premium Storage rendszeresen pillanatképeket használ, javasoljuk, hogy az egyéni lemezképeket a [megosztott](../articles/virtual-machines/linux/shared-image-galleries.md)képkatalógusban használja a standard szintű tárolóban. Ezzel a megoldással nagyobb léptékben érhet el alacsonyabb költségeket. Emellett a növekményes Pillanatképek is jobb megbízhatóságot biztosítanak a [Zone-redundáns tárolással](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan a ZRS-t fogja használni. Ha a ZRS nem érhető el a régióban, akkor a pillanatkép alapértelmezett értéke [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Felülbírálhatja ezt a viselkedést, és kiválaszthat egyet manuálisan, de nem ajánlott.

A növekményes Pillanatképek egy különbözeti képességet is biztosítanak, amely egyedi módon elérhető a felügyelt lemezeken. Lehetővé teszik az azonos felügyelt lemezek két növekményes pillanatképének változását a blokk szintjére. Ezzel a képességgel csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.

Ha még nem regisztrált az előzetes verzióra, és szeretné megkezdeni a növekményes Pillanatképek használatát, küldjön AzureDisks@microsoft.com e-mailt a szolgáltatásnak, hogy hozzáférjen a nyilvános előzetes verzióhoz.

## <a name="restrictions"></a>Korlátozások

- A növekményes Pillanatképek jelenleg csak az USA nyugati középső régiójában érhetők el.
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

A telepítését követően jelentkezzen be a PowerShell-munkamenetbe `az login`a következővel:.

Ha Azure PowerShell használatával szeretne növekményes pillanatképet létrehozni, állítsa a konfigurációt a [New-AzSnapShotConfig](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) `-Incremental` paraméterrel, majd adja át az értékét a `-Snapshot` [New-AzSnapshot](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) paraméterrel.

Cserélje le `<yourDiskNameHere>`a, `<yourDesiredSnapShotNameHere>` a és az értékeket a következő parancsfájl használatával növekményes pillanatkép létrehozására: `<yourResourceGroupNameHere>`

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Az azonos lemezről `SourceResourceId` származó növekményes pillanatképeket a `SourceUniqueId` és a pillanatképek tulajdonságaival is azonosíthatja. `SourceResourceId`a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId`az érték a lemez `UniqueId` tulajdonságában örökölt. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, a `UniqueId` tulajdonság módosul.

`SourceResourceId` A és `SourceUniqueId` az használatával létrehozhat egy adott lemezhez társított összes pillanatkép listáját. Cserélje `<yourResourceGroupNameHere>` le az értéket az értékre, majd a következő példa használatával sorolja fel a meglévő növekményes pillanatképeket:

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

## <a name="cli"></a>parancssori felület

Növekményes pillanatképet is létrehozhat az Azure CLI-vel, az Azure CLI legújabb verziójára lesz szüksége. A következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:

```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```

Növekményes pillanatkép létrehozásához használja `--incremental` az [az Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) paramétert.

A következő példa egy növekményes pillanatképet hoz `<yourDesiredSnapShotNameHere>`létre `<yourResourceGroupNameHere>`,`<exampleDiskName>`és `<exampleLocation>` lecseréli a saját értékeit, majd futtatja a példát:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Az azonos lemezről `SourceResourceId` származó növekményes pillanatképeket a `SourceUniqueId` és a pillanatképek tulajdonságaival is azonosíthatja. `SourceResourceId`a szülő lemez Azure Resource Manager erőforrás-azonosítója. `SourceUniqueId`az érték a lemez `UniqueId` tulajdonságában örökölt. Ha töröl egy lemezt, majd létrehoz egy azonos nevű új lemezt, a `UniqueId` tulajdonság módosul.

`SourceResourceId` A és `SourceUniqueId` az használatával létrehozhat egy adott lemezhez társított összes pillanatkép listáját. Az alábbi példa felsorolja az adott lemezzel kapcsolatos összes növekményes pillanatképet, de szükség van rá néhány beállításra.

Ez a példa jQ használ az adatlekérdezéshez. A példa futtatásához [telepítenie](https://stedolan.github.io/jq/download/)kell a jQ.

Cserélje `<yourResourceGroupNameHere>` le `<exampleDiskName>` az értékeket, és az értékeit használva a következő példával listázhatja a meglévő növekményes pillanatképeket, feltéve, hogy a jQ is telepítette:

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

Ha még nem regisztrált az előzetes verzióra, és szeretné megkezdeni a növekményes Pillanatképek használatát, küldjön AzureDisks@microsoft.com e-mailt a szolgáltatásnak, hogy hozzáférjen a nyilvános előzetes verzióhoz.
