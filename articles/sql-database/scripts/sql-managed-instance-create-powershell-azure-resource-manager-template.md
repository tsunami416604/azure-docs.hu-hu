---
title: PowerShell-példa – az Azure SQL Database felügyelt példány létrehozása |} A Microsoft Docs
description: Az Azure PowerShell-példaszkript – az Azure SQL Database felügyelt példány létrehozása
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: dfd81735b7dfd95a38caf3934fe9057adbcde5a7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790009"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>A PowerShell használata az Azure Resource Manager-sablon az Azure SQL Database felügyelt példány létrehozása

Az Azure SQL Database felügyelt példány az Azure PowerShell-könyvtár és Azure Resource Manager-sablonok használatával is létrehozható.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz AZ PowerShell 1.4.0-s vagy újabb. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az Azure PowerShell-parancsok előre meghatározott Azure Resource Manager-sablonnal üzembe helyezés megkezdéséhez. A sablon a következő tulajdonságok adhatók meg:

- Példány neve
- SQL-rendszergazdai felhasználónevet és jelszót.
- A példány (magok számától és maximális tárméretének) mérete.
- Virtuális hálózatot és alhálózatot, ahol a példány kerül.
- Kiszolgálószintű rendezést a példány (előzetes verzió).

Példány neve, az SQL-rendszergazda felhasználóneve, virtuális hálózat/alhálózat és rendezési később nem módosítható. Egyéb példány tulajdonságai módosíthatók.

## <a name="prerequisites"></a>Előfeltételek

Ez a példa feltételezi, hogy [egy érvényes hálózati környezetben létrehozott](../sql-database-managed-instance-create-vnet-subnet.md) vagy [módosította a meglévő VNet](../sql-database-managed-instance-configure-vnet-subnet.md) a felügyelt példány. A példa a parancsmagok [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) és [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) ezért ügyeljen arra, hogy telepítette-e az alábbi PowerShell-modulokat:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Az alábbi tartalommal kell helyezni a példány létrehozásához használandó sablon képviselő fájl:

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

Feltételezzük, hogy a megfelelően konfigurált alhálózattal rendelkező Azure virtuális hálózat már létezik. Ha nem rendelkezik megfelelően konfigurált alhálózat, a hálózati környezet előkészítése a külön használatával [sablont az Azure-erőforrás felügyelt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) , amelyek egymástól függetlenül végre, vagy a sablonban található.

.JSON kiterjesztésű fájlt mentse a fájl tartalmát, helyezze a fájl elérési útja a következő PowerShell-parancsfájlt, és módosítsa a parancsfájl az objektumok nevét:

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
