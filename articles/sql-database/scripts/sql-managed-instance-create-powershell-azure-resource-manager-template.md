---
title: Példa sablonra – felügyelt példány létrehozása Azure SQL Database
description: Azure PowerShell példa parancsfájlt a felügyelt példányok létrehozásához Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: be6aa73fe72568e9762e5b7249bedc2e8c7d3bf7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691436"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Felügyelt példány létrehozása Azure Resource Manager sablonnal a PowerShell használatával Azure SQL Database

Azure SQL Database felügyelt példány hozható létre Azure PowerShell kódtár és Azure Resource Manager sablonok használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Azure PowerShell parancsok előre definiált Azure Resource Manager sablonnal indíthatók el a telepítéssel. A sablonban a következő tulajdonságokat lehet megadni:

- Példány neve
- SQL-rendszergazdai Felhasználónév és jelszó.
- A példány mérete (magok száma és a tárterület maximális mérete).
- A VNet és az alhálózat, ahová a példány kerül.
- A példány kiszolgáló szintű rendezése (előzetes verzió).

A példány neve, az SQL-rendszergazda felhasználóneve, a VNet/alhálózat és a rendezés később nem módosítható. Más példányok tulajdonságai módosíthatók.

## <a name="prerequisites"></a>Előfeltételek

Ez a példa feltételezi, hogy [létrehozott egy érvényes hálózati környezetet](../sql-database-managed-instance-create-vnet-subnet.md) , vagy módosította a felügyelt példány [meglévő VNet](../sql-database-managed-instance-configure-vnet-subnet.md) . A minta a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) és a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) parancsmagot használja, ezért győződjön meg arról, hogy telepítette a következő PowerShell-modulokat:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A következő tartalmat olyan fájlba kell helyezni, amely a példány létrehozásához használni kívánt sablont jelöli:

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
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
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

Feltételezhető, hogy a megfelelően konfigurált alhálózattal rendelkező Azure-VNet már létezik. Ha nem rendelkezik megfelelően konfigurált alhálózattal, készítse elő a hálózati környezetet külön [Azure erőforrás-felügyelt sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) használatával, amely egymástól függetlenül végrehajtható, vagy ebben a sablonban is megtalálható.

Mentse a fájl tartalmát. JSON-fájlként, helyezze a fájl elérési útját a következő PowerShell-parancsfájlba, és módosítsa a parancsfájlban szereplő objektumok nevét:

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

Miután a szkript sikeresen lefutott, az SQL Database elérhető lesz az összes Azure-szolgáltatásból és a konfigurált IP-címről.

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
