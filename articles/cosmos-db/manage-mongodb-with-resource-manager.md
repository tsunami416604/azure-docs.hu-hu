---
title: Azure Cosmos DB API a mongodb-hez az Azure Resource Manager-sablonokkal
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB API a mongodb-hez.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: bcf9e0492e58de79efbb16f9ee13adbd0f27b572
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077770"
---
# <a name="create-azure-cosmos-db-api-for-mongodb-resources-from-a-resource-manager-template"></a>Azure Cosmos DB API létrehozása a MongoDB-erőforrások egy Resource Manager-sablonból

Ismerje meg, hogyan hozhat létre egy Azure Cosmos DB API a MongoDB-erőforrások egy Azure Resource Manager-sablon használatával. Az alábbi példa az Azure Cosmos DB-fiókot hoz létre a MongoDB API- [Azure gyorsindítási sablon](https://aka.ms/mongodb-arm-qs). Ezzel a sablonnal hoz létre egy Azure Cosmos-fiók MongoDB API-hoz két a gyűjteményeket, amelyek megosztása a 400 RU/s átviteli sebesség, az adatbázis szintjén.

Itt látható a sablon egy példányát:

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

## <a name="deploy-via-azure-cli"></a>Üzembe helyezés az Azure CLI

Az Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez **másolási** a parancsfájlt, és válasszon **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

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