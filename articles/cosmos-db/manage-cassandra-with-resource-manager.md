---
title: Azure Cosmos DB Cassandra API az Azure Resource Manager-sablonokkal
description: Használja az Azure Resource Manager-sablonok létrehozása és konfigurálása az Azure Cosmos DB Cassandra API-hoz.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 9878939a461f3ba35d2b6c270de2a965f66204cc
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077545"
---
# <a name="create-azure-cosmos-db-cassandra-api-resources-from-a-resource-manager-template"></a>Azure Cosmos DB Cassandra API-erőforrások létrehozása a Resource Manager-sablonnal

Megtudhatja, hogyan hozhat létre Azure Cosmos-fiókot, Cassandra API-hoz egy Azure Resource Manager-sablon használatával. Az alábbi példa létrehoz egy Azure Cosmos DB Cassandra API-fiókot a a [Azure gyorsindítási sablon](https://aka.ms/cassandra-arm-qs). Ezzel a sablonnal hoz létre egy Azure Cosmos-fiók a Cassandra API-hoz két táblát, amelyek megosztása a kulcstér szintjén 400 RU/s átviteli. 

Itt látható a sablon egy példányát:

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

Az előző példában a githubon tárolt sablonból rendelkezik hivatkozott. Is a sablon letöltése a helyi számítógépen, vagy hozzon létre egy új sablont és a helyi elérési útját adja meg a `--template-file` paraméter.

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány további erőforrást:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltató sémáján](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB-Quickstart-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager üzembe helyezési előforduló gyakori hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)