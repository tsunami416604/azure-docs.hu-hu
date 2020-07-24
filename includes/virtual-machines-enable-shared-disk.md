---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 62645e6252256079e27792b1905d60a073c1fa3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080224"
---
## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A megosztott lemezek több operációs rendszert támogatnak. Tekintse meg a támogatott operációs rendszerekkel kapcsolatos fogalmi cikk [Windows](../articles/virtual-machines/windows/disks-shared.md#windows) -és [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) -részeit.

## <a name="disk-sizes"></a>Lemezek mérete

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Megosztott lemezek telepítése

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Prémium SSD üzembe helyezése megosztott lemezként

Ha olyan felügyelt lemezt kíván üzembe helyezni, amelyen engedélyezve van a megosztott lemez szolgáltatás, használja az új tulajdonságot, `maxShares` és adjon meg egy 1-nél nagyobb értéket. Így a lemez több virtuális gépen is megosztható.

> [!IMPORTANT]
> A értéke `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez le van választva az összes virtuális gépről. Tekintse meg az engedélyezett értékek [lemezének méretét](#disk-sizes) `maxShares` .

#### <a name="cli"></a>parancssori felület
```azurecli

az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku PremiumSSD_LRS --max-shares 2

```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType PremiumSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager
A következő sablon használata előtt cserélje le a, a, a `[parameters('dataDiskName')]` `[resourceGroup().location]` és a `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` értéket a saját értékeire.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Ultra-lemez üzembe helyezése megosztott lemezként

Ha olyan felügyelt lemezt kíván üzembe helyezni, amelyen engedélyezve van a megosztott lemez szolgáltatás, módosítsa a `maxShares` paramétert 1-nél nagyobb értékre. Így a lemez több virtuális gépen is megosztható.

> [!IMPORTANT]
> A értéke `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez le van választva az összes virtuális gépről. Tekintse meg az engedélyezett értékek [lemezének méretét](#disk-sizes) `maxShares` .

#### <a name="cli"></a>parancssori felület
```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Ha a megosztott lemez funkciót engedélyező felügyelt lemezt kíván telepíteni, használja a tulajdonságot, `maxShares` és adjon meg egy 1-nél nagyobb értéket. Így a lemez több virtuális gépen is megosztható.

> [!IMPORTANT]
> A értéke `maxShares` csak akkor állítható be vagy módosítható, ha egy lemez le van választva az összes virtuális gépről. Tekintse meg az engedélyezett értékek [lemezének méretét](#disk-sizes) `maxShares` .

A következő sablon használata előtt cserélje le a,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` és `[parameters('diskMBpsReadOnly')]` értéket a saját értékeire.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Az Azure-beli megosztott lemezek használata a virtuális gépekkel

Miután telepített egy megosztott lemezt a `maxShares>1` használatával, csatlakoztathatja a lemezt egy vagy több virtuális géphez.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Támogatott SCSI PR-parancsok

Miután csatlakoztatta a megosztott lemezt a fürtben lévő virtuális gépekhez, létrehozhat kvórumot, és írási/olvasási lehetőséget is használhat a lemezre az SCSI PR használatával. Az Azure megosztott lemezek használatakor az alábbi PR-parancsok érhetők el:

A lemezzel való kommunikációhoz Kezdje a következőt: állandó – foglalás-művelet lista:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION vagy PR_RELEASE_RESERVATION használatakor adja meg a következő állandó fenntartási típus egyikét:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION vagy PR_RELEASE – foglalás használatakor állandó foglalási kulcsot kell megadnia.


## <a name="next-steps"></a>További lépések
