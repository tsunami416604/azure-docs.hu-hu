---
title: Az Azure Cosmos DB Table API az Azure Resource Manager-sablonokkal
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB Table API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968822"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata az Azure Cosmos DB Table API-erőforrások kezelése

## Az Azure Cosmos-fiók és tábla létrehozása <a id="create-resource"></a>

Hozzon létre egy Azure Resource Manager-sablon használatával az Azure Cosmos DB-erőforrásokat. Ezzel a sablonnal hoz létre egy Azure Cosmos-fiók egy olyan táblát 400 RU/s adatátviteli kapacitással rendelkező tábla API-hoz. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Üzembe helyezés a PowerShell

A PowerShell-lel, Resource Manager-sablon üzembe helyezéséhez **másolási** a parancsfájlt, majd válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Ha az Azure Cloud shell a helyileg telepített verziója helyett a PowerShell használata mellett dönt, hogy [telepítése](/powershell/azure/install-az-ps) az Azure PowerShell modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Üzembe helyezés az Azure CLI

Az Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez **másolási** a parancsfájlt, és válasszon **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

A `az cosmosdb show` parancs megjeleníti az újonnan létrehozott Azure Cosmos-fiók után van kiépítve. Ha úgy dönt, hogy a cloud Shell használata helyett használhatja az Azure CLI helyileg telepített verzióját, [Azure parancssori felület (CLI)](/cli/azure/) cikk.

## Átviteli sebesség (RU/s), egy tábla frissítése <a id="table-ru-update"></a>

Az alábbi sablont frissíteni fogja az átviteli sebességet, illetve egy táblázat. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Tábla átviteli PowerShell-lel üzembe helyezése

A PowerShell-lel, Resource Manager-sablon üzembe helyezéséhez **másolási** a parancsfájlt, majd válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Azure CLI-n keresztül tábla sablon üzembe helyezése

Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány további erőforrást:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltató sémáján](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB-Quickstart-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager üzembe helyezési előforduló gyakori hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)