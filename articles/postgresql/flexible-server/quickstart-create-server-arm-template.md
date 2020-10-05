---
title: 'Rövid útmutató: Azure-adatbázis létrehozása a PostgresSQL rugalmas kiszolgálói ARM-sablonhoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure-adatbázist a rugalmas PostgresSQL-kiszolgálóhoz ARM-sablon használatával.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: a52f88f5ca325027cbac53d2c7e927bcf30da994
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947237"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Gyors útmutató: ARM-sablon használata Azure Database for PostgreSQL-rugalmas kiszolgáló létrehozásához

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A rugalmas kiszolgáló egy felügyelt szolgáltatás, amelyet a felhőben található, magasan elérhető PostgreSQL-adatbázisok futtatásához, kezeléséhez és méretezéséhez használhat. Az ARM-sablonokkal létrehozhat egy PostgreSQL rugalmas kiszolgálót, amely több kiszolgálót vagy több adatbázist helyez üzembe egy kiszolgálón.

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. Az üzembe helyezés után az erőforrások biztonságossá tételéhez és rendszerezéséhez olyan felügyeleti funkciókat használhat, mint a hozzáférés-vezérlés, a zárolások és a címkék.

Azure Resource Manager-sablonokkal kapcsolatos további tudnivalókért tekintse meg a [template Deployment áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>A sablon áttekintése

A PostgresSQL-kiszolgáló Azure-adatbázisa egy adott régióban található egy vagy több adatbázis szülő erőforrása. Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.

Ezek az erőforrások a sablonban vannak definiálva:

- Microsoft. DBforPostgreSQL/flexibleServers

Hozzon létre egy ```postgres-flexible-server-template.json``` fájlt, és másolja a ```json``` parancsfájlt.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "String"
        },
        "administratorLoginPassword": {
            "type": "SecureString"
        },
        "location": {
            "type": "String"
        },
        "serverName": {
            "type": "String"
        },
        "serverEdition": {
            "type": "String"
        },
        "vCores": {
            "type": "Int"
        },
        "storageSizeMB": {
            "type": "Int"
        },
        "standbyCount": {
            "type": "Int"
        },
        "availabilityZone": {
            "type": "String"
        },
        "version": {
            "type": "String"
        },
        "tags": {
            "defaultValue": {},
            "type": "Object"
        },
        "firewallRules": {
            "defaultValue": {},
            "type": "Object"
        },
        "vnetData": {
            "defaultValue": {},
            "type": "Object"
        },
        "backupRetentionDays": {
            "type": "Int"
        }
    },
    "variables": {
        "api": "2020-02-14-privatepreview",
        "firewallRules": "[parameters('firewallRules').rules]",
        "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
        "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"vnetId\": \"\", \"vnetName\": \"\", \"vnetResourceGroup\": \"\", \"subnetName\": \"\" }'), parameters('vnetData'))]",
        "finalVnetData": "[json(concat('{ \"DelegatedVnetID\": \"', variables('vnetDataSet').vnetId, '\", \"DelegatedVnetName\": \"', variables('vnetDataSet').vnetName, '\", \"DelegatedVnetResourceGroup\": \"', variables('vnetDataSet').vnetResourceGroup, '\", \"DelegatedSubnetName\": \"', variables('vnetDataSet').subnetName, '\"}'))]"
    },
    "resources": [
        {
            "type": "Microsoft.DBforPostgreSQL/flexibleServers",
            "apiVersion": "[variables('api')]",
            "name": "[parameters('serverName')]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tags')]",
            "sku": {
                "name": "GP_D4s_v3",
                "tier": "[parameters('serverEdition')]",
                "capacity": "[parameters('vCores')]"
            },
            "properties": {
                "version": "[parameters('version')]",
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "publicNetworkAccess": "[variables('publicNetworkAccess')]",
                "VnetInjArgs": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
                "standbyCount": "[parameters('standbyCount')]",
                "storageProfile": {
                    "storageMB": "[parameters('storageSizeMB')]",
                    "backupRetentionDays": "[parameters('backupRetentionDays')]"
                },
                "availabilityZone": "[parameters('availabilityZone')]"
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-08-01",
            "name": "[concat('firewallRules-', copyIndex())]",
            "dependsOn": [
                "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
                            "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
                            "apiVersion": "[variables('api')]",
                            "properties": {
                                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
                            }
                        }
                    ]
                }
            },
            "copy": {
                "name": "firewallRulesIterator",
                "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
                "mode": "Serial"
            },
            "condition": "[greater(length(variables('firewallRules')), 0)]"
        }
    ]
}
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Azure Cloud Shell megnyitásához válassza a **kipróbálás** a következő PowerShell-kódból elemet.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---
## <a name="view-the-deployed-resources"></a>Az üzembe helyezett erőforrások megtekintése

A következő lépésekkel ellenőrizheti, hogy a kiszolgáló az Azure-ban lett-e létrehozva.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **Azure Database for PostgreSQL rugalmas kiszolgálók (előzetes verzió)** lehetőséget.

2. Az adatbázis listáról válassza ki az új kiszolgálót, és tekintse meg a kiszolgáló felügyeletére szolgáló **Áttekintés** lapot.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Meg kell adnia az új kiszolgáló nevét, hogy megtekintse a Azure Database for MySQL rugalmas kiszolgálójának adatait.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Meg kell adnia az új kiszolgáló nevét és erőforrás-csoportját, hogy megtekintse a Azure Database for MySQL rugalmas kiszolgáló részleteit.

```azurecli-interactive
echo "Enter your Azure Database for MySQL Flexible server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL Flexible server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg ezt az erőforráscsoportot, kiszolgálót és önálló adatbázist, ha a [következő lépésekre](#next-steps)szeretne lépni. A következő lépések bemutatják, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Az erőforráscsoport törlése:

# <a name="portal"></a>[Portál](#tab/azure-portal)

A [portálon](https://portal.azure.com)válassza ki a törölni kívánt erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.
2. A törlés megerősítéséhez írja be az erőforráscsoport nevét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Következő lépések 
> [!div class="nextstepaction"]
> [Adatbázis migrálása a dump és a Restore használatával](../howto-migrate-using-dump-and-restore.md)
