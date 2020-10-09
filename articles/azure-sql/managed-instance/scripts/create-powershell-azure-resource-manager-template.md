---
title: Felügyelt példány létrehozása (ARM-sablon & PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Ezt a Azure PowerShell példát használva felügyelt példányt hozhat létre.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 6758aed67949032bb93086527623b60af33e716e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079557"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Felügyelt példány létrehozása Azure Resource Manager sablonnal a PowerShell használatával

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Felügyelt példány létrehozásához használja a Azure PowerShell könyvtárat és Azure Resource Manager sablonokat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz Azure PowerShell 1.4.0 vagy újabb rendszerre van szükség. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, futtassa az parancsot `Connect-AzAccount` Az Azure-hoz való kapcsolódás létrehozásához.

Azure PowerShell parancsok előre definiált Azure Resource Manager sablonnal is elindíthatók. A sablonban a következő tulajdonságokat lehet megadni:

- Felügyelt példány neve
- SQL-rendszergazdai Felhasználónév és jelszó.
- A példány mérete (magok száma és a tárterület maximális mérete).
- A VNet és az alhálózat, ahová a példány kerül.
- A példány kiszolgáló szintű rendezése (előzetes verzió).

A példány neve, az SQL-rendszergazda felhasználóneve, a VNet/alhálózat és a rendezés később nem módosítható. Más példányok tulajdonságai módosíthatók.

## <a name="prerequisites"></a>Előfeltételek

Ez a példa feltételezi, hogy [létrehozott egy érvényes hálózati környezetet](../virtual-network-subnet-create-arm-template.md) , vagy [módosított egy meglévő VNet](../vnet-existing-add-subnet.md) a felügyelt példányhoz. Szükség esetén külön [Azure Resource Manager sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)is előkészítheti a hálózati környezetet. 


A minta a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) és a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)parancsmagot használja, ezért győződjön meg arról, hogy telepítette a következő PowerShell-modulokat:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon


Mentse a következő parancsfájlt egy. JSON fájlba, és jegyezze fel a fájl helyét: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Frissítse az alábbi PowerShell-parancsfájlt a korábban mentett. JSON fájl helyes elérési útjával, és módosítsa a parancsfájlban szereplő objektumok nevét:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

A parancsfájl befejeződése után a felügyelt példány az összes Azure-szolgáltatásból és a konfigurált IP-címről is elérhető.

## <a name="next-steps"></a>További lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/azure/).

Az Azure SQL felügyelt példányaihoz további PowerShell-szkriptek is megtalálhatók az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md).
