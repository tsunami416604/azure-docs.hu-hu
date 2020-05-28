---
title: 'Példány létrehozása (ARM-sablon & PowerShell) '
titleSuffix: Azure SQL Managed Instance
description: Ezzel a Azure PowerShell parancsfájllal létrehozhat egy felügyelt Azure SQL-példányt.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 55b0c8f569a91075d4cd87541af7aeff5da69f9a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053968"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-managed-instance"></a>A PowerShell használata Azure Resource Manager sablonnal egy felügyelt Azure SQL-példány létrehozásához
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példánya Azure PowerShell könyvtár és Azure Resource Manager sablonok használatával hozható létre.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz AZ AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, futtassa az parancsot `Connect-AzAccount` Az Azure-hoz való kapcsolódás létrehozásához.

Azure PowerShell parancsok előre definiált Azure Resource Manager sablonnal is elindíthatók. A sablonban a következő tulajdonságokat lehet megadni:

- SQL felügyelt példányának neve
- SQL-rendszergazdai Felhasználónév és jelszó.
- A példány mérete (magok száma és a tárterület maximális mérete).
- A VNet és az alhálózat, ahová a példány kerül.
- A példány kiszolgáló szintű rendezése (előzetes verzió).

A példány neve, az SQL-rendszergazda felhasználóneve, a VNet/alhálózat és a rendezés később nem módosítható. Más példányok tulajdonságai módosíthatók.

## <a name="prerequisites"></a>Előfeltételek

Ez a példa feltételezi, hogy [létrehozott egy érvényes hálózati környezetet](../virtual-network-subnet-create-arm-template.md) , vagy módosította az SQL felügyelt példányának [meglévő VNet](../vnet-existing-add-subnet.md) . A hálózati környezetet igény szerint külön [Azure erőforrás-felügyelt sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)is előkészítheti. 


A minta a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) és a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) parancsmagot használja, ezért győződjön meg arról, hogy telepítette a következő PowerShell-modulokat:

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

# Managed Instance properties
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

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

A parancsfájl befejeződése után az SQL felügyelt példánya az összes Azure-szolgáltatásból és a konfigurált IP-címről is elérhető.

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL felügyelt példányok PowerShell-parancsfájlokat az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md)találhat.
