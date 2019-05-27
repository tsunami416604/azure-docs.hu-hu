---
title: Azure Cosmos DB Cassandra API az Azure Resource Manager-sablonokkal
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB Cassandra API-hoz.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: db754adbe60acfa155400910c47de556db793eef
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968908"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata az Azure Cosmos DB Cassandra API-erőforrások kezelése

## Az Azure Cosmos-fiókot, kulcstér és tábla létrehozása <a id="create-resource"></a>

Hozzon létre egy Azure Resource Manager-sablon használatával az Azure Cosmos DB-erőforrásokat. Ezzel a sablonnal hoz létre egy Azure Cosmos-fiók a Cassandra API-hoz két táblát, amelyek megosztása a kulcstér szintjén 400 RU/s átviteli. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez **másolási** a parancsfájlt, és válasszon **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

A `az cosmosdb show` parancs megjeleníti az újonnan létrehozott Azure Cosmos-fiók után van kiépítve. Ha úgy dönt, hogy a cloud Shell használata helyett használhatja az Azure CLI helyileg telepített verzióját, [Azure parancssori felület (CLI)](/cli/azure/) cikk.

## Átviteli sebesség (RU/s) a egy kulcstér frissítése <a id="keyspace-ru-update"></a>

Az alábbi sablont frissíteni fogja a kulcstér átviteli kapacitást. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Azure CLI-n keresztül kulcstér sablon üzembe helyezése

Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Átviteli sebesség (RU/s), egy tábla frissítése <a id="table-ru-update"></a>

Az alábbi sablont frissíteni fogja az átviteli sebességet, illetve egy táblázat. Másolja ki a sablont, és üzembe helyezése a lent látható módon, vagy keresse fel [Azure gyorsindítási galéria](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) és üzembe helyezése az Azure Portalról. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Azure CLI-n keresztül tábla sablon üzembe helyezése

Azure CLI használatával a Resource Manager-sablon üzembe helyezéséhez válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány további erőforrást:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltató sémáján](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB-Quickstart-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager üzembe helyezési előforduló gyakori hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)