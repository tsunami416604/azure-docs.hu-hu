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
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224578"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Növekményes pillanatkép (előzetes verzió) létrehozása a felügyelt lemezekhez

A növekményes Pillanatképek (előzetes verzió) olyan felügyelt lemezek időpontra vonatkozó biztonsági mentései, amelyek az utolsó pillanatkép óta csak az összes változást tartalmazzák. Ha egy növekményes pillanatkép letöltését vagy más módon történő használatát kísérli meg, a rendszer a teljes VHD-t használja. Ez az új képesség a felügyelt lemezek pillanatképei számára potenciálisan költséghatékony lehet, mivel már nem szükséges a teljes lemez tárolása az egyes Pillanatképek esetében, hacsak nem választja. A normál pillanatképekhez hasonlóan a növekményes Pillanatképek is használhatók teljes felügyelt lemezek létrehozásához, vagy rendszeres pillanatkép készítéséhez.

A növekményes pillanatfelvétel és a rendszeres Pillanatképek között néhány különbség van. A növekményes Pillanatképek mindig a standard HDD-tárolót használják, a lemez tárolási típusától függetlenül, míg a rendszeres Pillanatképek a prémium SSD-ket használhatják. Ha a virtuális gépek központi telepítésének vertikális felskálázásához Premium Storage rendszeresen pillanatképeket használ, javasoljuk, hogy az egyéni lemezképeket a [megosztott](../articles/virtual-machines/linux/shared-image-galleries.md)képkatalógusban használja a standard szintű tárolóban. Ezzel a megoldással nagyobb léptékben érhet el alacsonyabb költségeket. Emellett a növekményes Pillanatképek is jobb megbízhatóságot biztosítanak a [Zone-redundáns tárolással](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan a ZRS-t fogja használni. Ha a ZRS nem érhető el a régióban, akkor a pillanatkép alapértelmezett értéke [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Felülbírálhatja ezt a viselkedést, és kiválaszthat egyet manuálisan, de nem ajánlott.

A növekményes Pillanatképek egy különbözeti képességet is biztosítanak, amely egyedi módon elérhető a felügyelt lemezeken. Lehetővé teszik az azonos felügyelt lemezek két növekményes pillanatképének változását a blokk szintjére. Ezzel a képességgel csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.

Ha még nem regisztrált az előzetes verzióra, és szeretné megkezdeni a növekményes Pillanatképek használatát, küldjön e AzureDisks@microsoft.com -mailt a következő címre a nyilvános előzetes verzióhoz való hozzáféréshez.

## <a name="restrictions"></a>Korlátozások

- A növekményes Pillanatképek jelenleg nem hozhatók létre a lemez méretének módosítása után.
- A növekményes Pillanatképek jelenleg nem helyezhetők át az előfizetések között.
- Jelenleg csak egy adott pillanatkép-családhoz tartozó SAS URI-k hozhatók létre akár öt pillanatképből.
- Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez.
- A lemezenként legfeljebb hét növekményes pillanatkép hozható létre 5 percenként.
- Egyetlen lemezhez összesen 200 növekményes pillanatkép hozható létre.

## <a name="powershell"></a>PowerShell

Növekményes pillanatkép létrehozásához használhatja a Azure PowerShellt is. A PowerShell legújabb verzióját helyileg is telepítheti. A Azure PowerShell legújabb verziójára lesz szüksége, a következő parancs telepíti vagy frissíti a meglévő telepítését a legújabb verzióra:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

A telepítését követően jelentkezzen be a PowerShell-munkamenetbe `az login`a következővel:.

Cserélje le `<yourDiskNameHere>`a, `<yourDesiredSnapShotNameHere>` a és az értékeket a következő parancsfájl használatával növekményes pillanatkép létrehozására: `<yourResourceGroupNameHere>`

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

## <a name="cli"></a>parancssori felület

Növekményes pillanatképet az az [Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)paranccsal hozhat létre az Azure CLI-vel. Egy példaként szolgáló parancs a következőhöz hasonlóan néz ki:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Azt is megtudhatja, hogy mely Pillanatképek növekményes Pillanatképek a CLI `--query` -ben a használatával az az [Snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show)parancsban található paraméterrel. Ezt a paramétert használhatja a pillanatképek **sourceresourceid azonosítónak** és **SourceUniqueId** közvetlen lekérdezéséhez. A Sourceresourceid azonosítónak a szülő lemez Azure Resource Manager erőforrás-azonosítója. A **SourceUniqueId** a lemez **UniqueID** tulajdonságában örökölt érték. Ha töröl egy lemezt, majd létrehoz egy azonos nevű lemezt, akkor a **UniqueID** tulajdonság értéke módosul.

A lekérdezések egyik példája a következőhöz hasonló:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>További lépések

Ha még nem regisztrált az előzetes verzióra, és szeretné megkezdeni a növekményes Pillanatképek használatát, küldjön e AzureDisks@microsoft.com -mailt a következő címre a nyilvános előzetes verzióhoz való hozzáféréshez.
