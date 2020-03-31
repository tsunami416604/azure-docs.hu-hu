---
title: 'Azure Resource Manager: Egyetlen adatbázis létrehozása'
description: Hozzon létre egyetlen adatbázist az Azure SQL Database-ben az Azure Resource Manager sablon használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 7c42ff7f42dea049752f9f879abffffd0e7b3902
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531328"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Rövid útmutató: Egyetlen adatbázis létrehozása az Azure SQL Database-ben az Azure Resource Manager sablon használatával

Egyetlen [adatbázis](sql-database-single-database.md) létrehozása a leggyorsabb és legegyszerűbb üzembe helyezési lehetőség egy adatbázis létrehozásához az Azure SQL Database-ben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egyetlen adatbázist az Azure Resource Manager sablon használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Előfeltételek

None

## <a name="create-a-single-database"></a>Egyetlen adatbázis létrehozása

Egyetlen adatbázis számítási, memória-, IO- és tárolási erőforrások meghatározott készletével rendelkezik a két [beszerzési modell](sql-database-purchase-models.md)egyikének használatával. Ha egyetlen adatbázist hoz létre, definiáljon egy [SQL Database-kiszolgálót](sql-database-servers.md) is, amely kezeli, és egy adott régióban az [Azure erőforráscsoporton](../azure-resource-manager/management/overview.md) belül helyezi el.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-sql-logical-server/)

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Ezek az erőforrások a sablonban vannak definiálva:

- [**Microsoft.Sql/kiszolgálók**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft.Sql/servers/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft.Sql/servers/vulnerabilityAssessments**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft.Sql/servers/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)

További Azure SQL-adatbázissablon-minták találhatók az [Azure gyorsútmutató-sablonokban.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Válassza **a Próbálja ki** a következő PowerShell-kódblokkból az Azure Cloud Shell megnyitásához.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az adatbázis lekérdezéséhez olvassa el [az Adatbázis lekérdezése](./sql-database-single-database-get-started.md#query-the-database)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg ezt az erőforráscsoportot, adatbázis-kiszolgálót és egyetlen adatbázist, ha a Következő lépésekkel szeretne [továbblépni.](#next-steps) A következő lépések bemutatják, hogyan csatlakozhat és kérdezheti le az adatbázist különböző módszerekkel.

Az erőforráscsoport törlése:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálószintű tűzfalszabályt, amely a helyszíni vagy távoli eszközökről csatlakozik az egyetlen adatbázishoz. További információt a [Kiszolgálószintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md)című témakörben talál.
- A kiszolgálószintű tűzfalszabály létrehozása után több különböző eszközzel és nyelvvel [csatlakoztassa és kérdezze](sql-database-connect-query.md) le az adatbázist.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Ha egyetlen adatbázist szeretne létrehozni az Azure CLI használatával, tekintse meg az [Azure CLI-mintákcímű témakört.](sql-database-cli-samples.md)
- Ha egyetlen adatbázist szeretne létrehozni az Azure PowerShell használatával, tekintse meg az [Azure PowerShell-mintákcímű témakört.](sql-database-powershell-samples.md)
- Az Erőforrás-kezelő sablonok létrehozásáról az [Első sablon létrehozása című](../azure-resource-manager/templates/template-tutorial-create-first-template.md)témakörben olvashat.
