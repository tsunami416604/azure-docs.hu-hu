---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008340"
---
## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Lemezméretek

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Megosztott lemezek telepítése

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Prémium szintű SSD telepítése megosztott lemezként

Ha olyan felügyelt lemezt szeretne telepíteni, amelyen `maxShares` engedélyezve van a megosztott lemez szolgáltatás, használja az új tulajdonságot, és adjon meg egy 1-nél nagyobb értéket. Ez lehetővé teszi a lemez megosztható több virtuális gép között.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Ultralemez központi telepítése megosztott lemezként

#### <a name="cli"></a>parancssori felület

Ha olyan felügyelt lemezt szeretne telepíteni, `maxShares` amelyen a megosztott lemez szolgáltatás engedélyezve van, módosítsa a paramétert 1-nél nagyobb értékre. Ez lehetővé teszi a lemez megosztható több virtuális gép között.

> [!IMPORTANT]
> Az érték `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez nincs csatlakoztatva az összes virtuális géphez. Tekintse meg a [lemezméretek](#disk-sizes) a megengedett értékeket. `maxShares`

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Ha olyan felügyelt lemezt szeretne telepíteni, amelyen a megosztott lemez szolgáltatás engedélyezve van, használja a tulajdonságot, `maxShares` és adjon meg egy 1-nél nagyobb értéket. Ez lehetővé teszi a lemez megosztható több virtuális gép között.

> [!IMPORTANT]
> Az érték `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez nincs csatlakoztatva az összes virtuális géphez. Tekintse meg a [lemezméretek](#disk-sizes) a megengedett értékeket. `maxShares`

A következő sablon használata `[parameters('dataDiskName')]` `[resourceGroup().location]`előtt `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]`cserélje `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]`le `[parameters('diskIOPSReadOnly')]`a `[parameters('diskMBpsReadOnly')]` , , , , , , , , és a saját értékeit.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
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