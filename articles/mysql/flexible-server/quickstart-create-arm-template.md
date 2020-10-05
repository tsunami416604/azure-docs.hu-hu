---
title: 'Gyors útmutató: Azure-adatbázis létrehozása a MySQL-hez – rugalmas kiszolgáló – ARM-sablon'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Database for MySQL rugalmas kiszolgálót ARM-sablon használatával.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 18366069fd7273afe33b538d2bd69ac8a99689c6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947210"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Gyors útmutató: ARM-sablon használata Azure Database for MySQL rugalmas kiszolgáló létrehozásához (előzetes verzió)


> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

Azure Database for MySQL – a rugalmas kiszolgáló (előzetes verzió) egy felügyelt szolgáltatás, amellyel a felhőben futtathatja, kezelheti és méretezheti a magasan elérhető MySQL-adatbázisokat. Az ARM-sablonokkal rugalmas kiszolgáló üzembe helyezésével több kiszolgálót vagy több adatbázist telepíthet a kiszolgálókon.

Az [ARM-sablon](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) egy JavaScript Object Notation- (JSON-) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon olyan deklaratív szintaxist használ, amellyel anélkül határozhatja meg, hogy mit szeretne üzembe helyezni, hogy ehhez programozási parancsok sorozatát kellene megírnia.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>A sablon áttekintése

Egy Azure Database for MySQL rugalmas kiszolgáló egy adott régióban lévő egy vagy több adatbázis szülő erőforrása. Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.

Ezek az erőforrások a sablonban vannak definiálva:

- Microsoft. DBforMySQL/flexibleServers

Hozzon létre egy ```mysql-flexible-server-template.json``` fájlt, és másolja a ```json``` parancsfájlt.

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
            "type": "Microsoft.DBforMySQL/flexibleServers",
            "apiVersion": "[variables('api')]",
            "name": "[parameters('serverName')]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tags')]",
            "sku": {
                "name": "Standard_D4ds_v4",
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
                "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
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

A [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)megnyitásához válassza a **kipróbálás** a következő PowerShell-kódból elemet.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="view-the-deployed-resources"></a>Az üzembe helyezett erőforrások megtekintése

A következő lépésekkel ellenőrizheti, hogy a kiszolgáló az Azure-ban lett-e létrehozva.

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **Azure Database for MySQL kiszolgálókat**.

2. Az adatbázis listában válassza ki az új kiszolgálót. Megjelenik az új Azure Database for MySQL-kiszolgáló **Áttekintés** lapja.

### <a name="powershell"></a>PowerShell

Meg kell adnia az új kiszolgáló nevét, hogy megtekintse a Azure Database for MySQL rugalmas kiszolgálójának adatait.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>parancssori felület

Meg kell adnia az új kiszolgáló nevét és erőforrás-csoportját, hogy megtekintse a Azure Database for MySQL rugalmas kiszolgáló részleteit.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg ezt az erőforráscsoportot, kiszolgálót és önálló adatbázist, ha a [következő lépésekre](#next-steps)szeretne lépni. A következő lépések bemutatják, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Az erőforráscsoport törlése:

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok**elemet.

2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.

3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése**elemet.

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>parancssori felület

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>További lépések

Az ARM-sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Az App Service alkalmazások MySQL-sel való létrehozásával kapcsolatos részletes oktatóanyagért lásd:

> [!div class="nextstepaction"]
>[PHP-alapú (Laravel-) Webalkalmazás létrehozása MySQL-sel](tutorial-php-database-app.md)
