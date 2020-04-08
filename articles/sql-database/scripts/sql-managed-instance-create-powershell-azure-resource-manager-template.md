---
title: Példa sablonra – felügyelt példány létrehozása az Azure SQL Database-ben
description: Ezzel az Azure PowerShell-példaparancsfájllal hozzon létre egy felügyelt példányt az Azure SQL Database-ben.
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
ms.openlocfilehash: e5be8c9441be5ca441a5c0f7c4444c2edbdc7a95
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811213"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Felügyelt példány létrehozása az Azure Resource Manager sablonnal az Azure Resource Manager sablonnal

Az Azure SQL Database Felügyelt példány az Azure PowerShell-kódtár és az Azure Resource Manager-sablonok használatával hozható létre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az AZ PowerShell 1.4.0-s vagy újabb szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az Azure PowerShell-parancsok előre definiált Azure Resource Manager-sablonnal indíthatják el a központi telepítést. A sablonban a következő tulajdonságok adhatók meg:

- Példány neve
- SQL rendszergazdai felhasználónév és jelszó.
- A példány mérete (magok száma és maximális tárolási méret).
- Virtuális hálózat és alhálózat, ahol a példány kerül elhelyezésre.
- A példány kiszolgálószintű rendezése (előzetes verzió).

A példány neve, az SQL-rendszergazda felhasználóneve, a virtuális hálózat/alhálózat és a rendezés később nem módosítható. Más példánytulajdonságok módosíthatók.

## <a name="prerequisites"></a>Előfeltételek

Ez a minta feltételezi, hogy [létrehozott egy érvényes hálózati környezetet,](../sql-database-managed-instance-create-vnet-subnet.md) vagy módosította a felügyelt példány meglévő virtuális [hálózatát.](../sql-database-managed-instance-configure-vnet-subnet.md) A minta a [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) és a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) parancsmagokat használja, ezért győződjön meg arról, hogy telepítette a következő PowerShell-modulokat:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A következő tartalmat kell elhelyezni egy olyan fájlban, amely a példány létrehozásához használt sablont jelöli:

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

Feltételezés, hogy az Azure virtuális hálózat a megfelelően konfigurált alhálózat már létezik. Ha nem rendelkezik megfelelően konfigurált alhálózattal, készítse elő a hálózati környezetet külön [Azure Resource Managed sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) használatával, amely egymástól függetlenül vagy ebben a sablonban végrehajtható.

Mentse a fájl tartalmát .json fájlként, helyezze a fájl elérési útját a következő PowerShell-parancsfájlba, és módosítsa a parancsfájlban lévő objektumok nevét:

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
