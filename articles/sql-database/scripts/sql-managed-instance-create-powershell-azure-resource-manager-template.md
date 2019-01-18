---
title: PowerShell-példa – hozzon létre egy Azure SQL Database felügyelt példányába |} A Microsoft Docs
description: Az Azure PowerShell példaszkript az Azure SQL Database-Managd példány létrehozása
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: bbb22f8d5eab3e60eba13bb642edb8f8d190cf67
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54387938"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-database-managed-instance"></a>A PowerShell használata az Azure Resource Manager-sablon létrehozása az Azure SQL Database felügyelt példánya

Az Azure SQL Database felügyelt példány az Azure PowerShell-könyvtár és Azure Resource Manager-sablonok használatával is létrehozható. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az Azure PowerShell-parancsok előre meghatározott Azure Resource Manager-sablonnal üzembe helyezés megkezdéséhez. A sablon a következő tulajdonságok adhatók meg:
- Példány neve
- SQL-rendszergazdai felhasználónevet és jelszót. 
- A példány (magok számától és maximális tárméretének) mérete.
- Virtuális hálózatot és alhálózatot, ahol a példány kerül.
- Kiszolgálószintű rendezést a példány (előzetes verzió).

Példány neve, az SQL-rendszergazda felhasználóneve, virtuális hálózat/alhálózat és rendezési később nem módosítható. Egyéb példány tulajdonságai módosíthatók.

## <a name="prerequisites"></a>Előfeltételek

Ez a példa feltételezi, hogy [egy érvényes hálózati környezetben létrehozott](../sql-database-managed-instance-create-vnet-subnet.md) vagy [módosította a meglévő VNet](../sql-database-managed-instance-configure-vnet-subnet.md) a felügyelt példány. A példa a parancsmagok [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) és [Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) ezért ügyeljen arra, hogy telepítette-e az alábbi PowerShell-modulokat:

```
Install-Module AzureRM.Network
Install-Module AzureRM.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Az alábbi tartalommal kell helyezni a példány létrehozásához használandó sablon képviselő fájl:
```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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
Feltételezzük, hogy a megfelelően konfigurált alhálózattal rendelkező Azure virtuális hálózat már létezik. Nem ez a helyzet, ha a hálózati környezet előkészítése a használatával külön [sablont az Azure-erőforrás felügyelt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) , amelyek egymástól függetlenül végre, vagy a sablonban található.

.JSON kiterjesztésű fájlt mentse a fájl tartalmát, helyezze a fájl elérési útja a következő PowerShell-parancsfájlt, és módosítsa a parancsfájl az objektumok nevét: 

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzureRmSubscription -SubscriptionId $subscriptionId

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
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzureRmResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```
Miután a szkript sikeresen lefutott, az SQL Database elérhető lesz az összes Azure-szolgáltatásból és a konfigurált IP-címről. 

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).



