---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471690"
---
## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Lemezméretek

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Azure-beli megosztott lemez telepítése

Ha olyan felügyelt lemezt szeretne telepíteni, amelyen `maxShares` a megosztott `>1`lemez szolgáltatás engedélyezve van, használja az új tulajdonságot, és adjon meg egy értéket. Ez lehetővé teszi a lemez megosztható több virtuális gép között.

> [!IMPORTANT]
> Az érték `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez nincs csatlakoztatva az összes virtuális géphez. Tekintse meg a [lemezméretek](#disk-sizes) a megengedett értékeket. `maxShares`

A következő sablon használata `[parameters('dataDiskName')]` `[resourceGroup().location]`előtt `[parameters('dataDiskSizeGB')]`cserélje `[parameters('maxShares')]` le a , , és a saját értékeit.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Az Azure megosztott lemezei használata a virtuális gépekkel

Miután telepítette a megosztott `maxShares>1`lemezt a , csatlakoztathatja a lemezt egy vagy több virtuális géphez.

> [!IMPORTANT]
> A lemezen megosztott összes virtuális gépet ugyanabban a [közelségelhelyezési csoportban](../articles/virtual-machines/windows/proximity-placement-groups.md)kell telepíteni.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Támogatott SCSI PR-parancsok

Miután csatlakoztatta a megosztott lemezt a fürtben lévő virtuális gépekhez, kvórumot hozhat létre, és az SCSI PR használatával olvashat és olvashat a lemezre. Az Azure megosztott lemezei használata esetén a következő PR-parancsok érhetők el:

A lemezzel való interakcióhoz kezdje az állandó foglalási művelet listájával:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION vagy PR_RELEASE_RESERVATION használata esetén adja meg az alábbi állandó foglalási típusok egyikét:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

A PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION vagy PR_RELEASE-RESERVATION használatakor állandó foglalási kulcsot is meg kell adnia.


## <a name="next-steps"></a>További lépések

Ha érdekli a megosztott lemezek kipróbálása, [regisztráljon az előzetes verzióra.](https://aka.ms/AzureSharedDiskPreviewSignUp)