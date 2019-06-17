---
title: Azure Cosmos DB API a mongodb-hez az Azure Resource Manager-sablonokkal
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB API a mongodb-hez.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968868"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata az Azure Cosmos DB MongoDB API-erőforrások kezelése

## Azure Cosmos DB API létrehozása a MongoDB-fiókot, adatbázist és gyűjteményt <a id="create-resource"></a>

Hozzon létre egy Azure Resource Manager-sablon használatával az Azure Cosmos DB-erőforrásokat. Ezzel a sablonnal hoz létre egy Azure Cosmos-fiók MongoDB API-hoz két a gyűjteményeket, amelyek megosztása a 400 RU/s átviteli sebesség, az adatbázis szintjén. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Üzembe helyezés az Azure CLI

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

## Átviteli sebesség (RU/s) az adatbázis frissítése <a id="database-ru-update"></a>

Az alábbi sablont frissíteni fogja az adatbázis átviteli sebességet. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Azure CLI-n keresztül az adatbázis-sablon üzembe helyezése

Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Átviteli sebesség (RU/s), a gyűjtemény frissítése <a id="collection-ru-update"></a>

Az alábbi sablont frissíti a gyűjtemény átviteli sebességének. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Azure CLI-n keresztül gyűjtemény sablon üzembe helyezése

Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány további erőforrást:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltató sémáján](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB-Quickstart-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager üzembe helyezési előforduló gyakori hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)