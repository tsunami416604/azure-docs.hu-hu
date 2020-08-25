---
title: Azure PowerShell – végpontok közötti titkosítás engedélyezése a virtuálisgép-gazdagépen
description: Az Azure-beli virtuális gépek végpontok közötti titkosításának engedélyezése a gazdagépen történő titkosítással.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6a352ecc2d2b02f03e2b55f7c5896ac905077921
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814792"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>A Azure PowerShell modullal engedélyezheti a végpontok közötti titkosítást a gazdagépen lévő titkosítás használatával

Amikor engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva maradnak a tárolási szolgáltatásba titkosított adatforgalomban. A gazdagépen található titkosítással, valamint az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos elméleti információkat lásd: [titkosítás a gazdagép-végpontok közötti titkosítással a virtuális gép adataihoz](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

A virtuális gépek méreteit programozott módon is megtalálhatja. Ha szeretné megtudni, hogyan kérheti le őket programozott módon, tekintse meg a [támogatott VM-méretek keresése](#finding-supported-vm-sizes) szakaszt.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz titkosítást lehessen használni a gazdagépen, be kell szereznie a funkciót az előfizetésében. Küldjön egy e-mailt a. com-ra az encryptionAtHost@microsoft előfizetési azonosítókkal, hogy a funkció engedélyezve legyen az előfizetésekhez.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault és DiskEncryptionSet létrehozása

Ha a szolgáltatás engedélyezve van, be kell állítania egy Azure Key Vault és egy DiskEncryptionSet, ha még nem tette meg.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Titkosítás engedélyezése a gazdagépen a virtuális gépekhez és virtuálisgép-méretezési csoportokhoz csatolt lemezek esetén

A gazdagépen a titkosítást a **2020-06-01** -es és újabb verziójú API-k használatával engedélyezheti a virtuális gépek vagy virtuálisgép-méretezési csoportok securityProfile alatti új tulajdonságának EncryptionAtHost.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Példa parancsfájlok

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Titkosítás engedélyezése a gazdagépen az ügyfél által felügyelt kulcsokkal rendelkező virtuális géphez csatolt lemezek esetén

Hozzon létre egy felügyelt lemezekkel rendelkező virtuális gépet a korábban létrehozott DiskEncryptionSet erőforrás-URI-ja használatával.

Cserélje le a,,,,, `<yourPassword>` `<yourVMName>` `<yourVMSize>` `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` , és `<yourRegion>` a parancsot, majd futtassa a parancsfájlt.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Titkosítás engedélyezése a gazdagépen a platform által felügyelt kulcsokkal rendelkező virtuális géphez csatlakoztatott lemezek esetén

Cserélje le a,, `<yourPassword>` `<yourVMName>` ,, `<yourVMSize>` `<yourResourceGroupName>` és `<yourRegion>` a parancsot, majd futtassa a parancsfájlt.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Támogatott virtuális gépek méretének keresése

A régi virtuálisgép-méretek nem támogatottak. A támogatott virtuálisgép-méretek listáját a következők valamelyikével érheti el:

Az [erőforrás-SKU API](/rest/api/compute/resourceskus/list) meghívása és annak ellenőrzése, hogy a képesség igaz értékre van-e `EncryptionAtHostSupported` állítva **True**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Vagy hívja meg a [Get-AzComputeResourceSku PowerShell-](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) parancsmagot.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta és konfigurálta ezeket az erőforrásokat, a segítségével biztonságossá teheti a felügyelt lemezeket. A következő hivatkozás olyan parancsfájlokat tartalmaz, amelyek mindegyike megfelelő forgatókönyvekkel rendelkezik, amelyek segítségével biztonságossá teheti a felügyelt lemezeket.

[Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
