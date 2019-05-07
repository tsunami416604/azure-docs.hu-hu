---
title: Az Azure Cosmos DB az Azure Resource Manager-sablonok
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB-hez.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: f61a9246b1edc5ac10b64f32cc27fd51dcedde94
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077755"
---
# <a name="create-azure-cosmos-db-core-sql-api-resources-from-a-resource-manager-template"></a>Azure Cosmos DB Core (SQL) API-erőforrások létrehozása a Resource Manager-sablonnal

Ismerje meg, hogyan hozhat létre egy Azure Cosmos DB-erőforrások egy Azure Resource Manager-sablon használatával. Az alábbi példa létrehoz egy Azure Cosmos DB-fiókot a egy [Azure gyorsindítási sablon](https://aka.ms/sql-arm-qs). Ezzel a sablonnal hoz létre egy Azure Cosmos-fiók két tárolót használó 400 RU/s átviteli sebesség, az adatbázis szintjén.

Itt látható a sablon egy példányát:

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

## <a name="deploy-via-powershell"></a>Üzembe helyezés a PowerShell

A PowerShell-lel, Resource Manager-sablon üzembe helyezéséhez **másolási** a parancsfájlt, majd válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Ha az Azure Cloud shell a helyileg telepített verziója helyett a PowerShell használata mellett dönt, hogy [telepítése](/powershell/azure/install-az-ps) az Azure PowerShell modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. 

Az előző példában a githubon tárolt sablonból rendelkezik hivatkozott. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

## <a name="deploy-via-azure-cli"></a>Üzembe helyezés az Azure CLI

Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

A `az cosmosdb show` parancs megjeleníti az újonnan létrehozott Azure Cosmos-fiók után van kiépítve. Ha úgy dönt, hogy a cloud Shell használata helyett használhatja az Azure CLI helyileg telepített verzióját, [Azure parancssori felület (CLI)](/cli/azure/) cikk.

Az előző példában a githubon tárolt sablonból rendelkezik hivatkozott. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány további erőforrást:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltató sémáján](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB-Quickstart-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager üzembe helyezési előforduló gyakori hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)