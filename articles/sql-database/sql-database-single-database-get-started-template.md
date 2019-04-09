---
title: 'Az Azure Resource Manager: Hozzon létre egy önálló adatbázis – Azure SQL Database |} A Microsoft Docs'
description: Önálló adatbázis létrehozása az Azure SQL Database az Azure Resource Manager-sablon használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: b28c947cb2ee3a60633fcaced18a8c353474ec9e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259729"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Gyors útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure Resource Manager-sablon használatával

Létrehozás egy [önálló adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb üzembehelyezési megoldás az Azure SQL Database-adatbázis létrehozása. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy önálló adatbázis az Azure Resource Manager-sablon használatával. További információkért lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Önálló adatbázis az egyik két számítási, memória, IO és tárolási erőforrások egy meghatározott készletével rendelkezik [vásárlási modellek](sql-database-purchase-models.md). Ha létrehoz egy adatbázist, akkor is definiálni egy [SQL Database-kiszolgáló](sql-database-servers.md) felügyelni, és helyezze belül [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy meghatározott régióban.

A következő JSON-fájlt az ebben a cikkben használt sablon. A sablon egy Azure Storage-fiók tárolva van. Több Azure SQL database sablonminták található [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serverName": {
      "type": "string",
      "defaultValue": "[concat('server-', uniqueString(resourceGroup().id, deployment().name))]",
      "metadata": {
        "description": "Name for the SQL server"
      }
    },
    "shouldDeployDb": {
      "type": "string",
      "allowedValues": [
        "Yes",
        "No"
      ],
      "defaultValue": "Yes",
      "metadata": {
        "description": "Whether an Azure SQL Database should be deployed under the server"
      }
    },
    "databaseName": {
      "type": "string",
      "defaultValue": "[concat('db-', uniqueString(resourceGroup().id, deployment().name), '-1')]",
      "metadata": {
        "description": "Name for the SQL database under the SQL server"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for server and optional DB"
      }
    },
    "emailAddresses": {
      "type": "array",
      "defaultValue": [
        "user1@example.com",
        "user2@example.com"
      ],
      "metadata": {
        "description": "Email addresses for receiving alerts"
      }
    },
    "adminUser": {
      "type": "string",
      "metadata": {
        "description": "Username for admin"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for admin"
      }
    }
  },
  "variables": {
    "databaseServerName": "[toLower(parameters('serverName'))]",
    "databaseName": "[parameters('databaseName')]",
    "shouldDeployDb": "[parameters('shouldDeployDb')]",
    "databaseServerLocation": "[parameters('location')]",
    "databaseServerAdminLogin": "[parameters('adminUser')]",
    "databaseServerAdminLoginPassword": "[parameters('adminPassword')]",
    "emailAddresses": "[parameters('emailAddresses')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "name": "[variables('databaseServerName')]",
      "location": "[variables('databaseServerLocation')]",
      "apiVersion": "2015-05-01-preview",
      "properties": {
        "administratorLogin": "[variables('databaseServerAdminLogin')]",
        "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
        "version": "12.0"
      },
      "tags": {
        "DisplayName": "[variables('databaseServerName')]"
      },
      "resources": [
        {
          "type": "securityAlertPolicies",
          "name": "DefaultSecurityAlert",
          "apiVersion": "2017-03-01-preview",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "state": "Enabled",
            "emailAddresses": "[variables('emailAddresses')]",
            "emailAccountAdmins": true
          }
        }
      ]
    },
    {
      "condition": "[equals(variables('shouldDeployDb'), 'Yes')]",
      "type": "Microsoft.Sql/servers/databases",
      "name": "[concat(string(variables('databaseServerName')), '/', string(variables('databaseName')))]",
      "location": "[variables('databaseServerLocation')]",
      "apiVersion": "2017-10-01-preview",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('databaseServerName'))]"
      ],
      "properties": {},
      "tags": {
        "DisplayName": "[variables('databaseServerName')]"
      }
    }
  ]
}
```

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatesql%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.  

    ![Resource Manager-sablon létrehozása az azure sql database](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    Ha a célgyűjtemény meg van adva, használja az alapértelmezett értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válasszon **új létrehozása**, adjon meg egy egyedi nevet az erőforráscsoport, és kattintson **OK**. 
    * **Hely**: válasszon ki egy helyet.  Ha például **USA középső RÉGIÓJA**.
    * **Rendszergazdai felhasználó**: Adjon meg egy SQL database server rendszergazdai jogosultságú felhasználónevet.
    * **Rendszergazdai jelszó**: Adjon meg egy rendszergazdai jelszót. 
    * **Elfogadom a fenti feltételek és kikötések állapot**: Kiválasztás.
3. Válassza a **Beszerzés** lehetőséget.

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis lekérdezése, lásd: [az adatbázis lekérdezése](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, adatbázis-kiszolgáló és önálló adatbázist, ha szeretne belépni a [további lépések](#next-steps). A következő lépések bemutatják, hogyan történő csatlakozásról és lekérdezésről különböző módszereket használ az adatbázis.

Az erőforráscsoport törlése az Azure parancssori felület vagy az Azure Powershell használatával:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálószintű tűzfalszabályt, amely az egyetlen adatbázis csatlakozhat a helyszíni vagy távoli eszközök. További információkért lásd: [hozzon létre egy kiszolgálószintű tűzfalszabályt](sql-database-server-level-firewall-rule.md).
- Miután létrehozott egy kiszolgálószintű tűzfalszabályt [csatlakozásról és lekérdezésről](sql-database-connect-query.md) számos különböző eszközöket és nyelveket használ az adatbázis.
  - [Csatlakozás és lekérdezés az SQL Server Management Studio használatával](sql-database-connect-query-ssms.md)
  - [Csatlakozás és lekérdezés az Azure Data Studio használatával](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI-vel egy önálló adatbázisok létrehozásához lásd: [Azure CLI-minták](sql-database-cli-samples.md).
- Azure PowerShell-lel egy önálló adatbázisok létrehozásához lásd: [Azure PowerShell-minták](sql-database-powershell-samples.md).
