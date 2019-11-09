---
title: Azure Resource Manager sablonok Azure Cosmos DB Cassandra API
description: Azure Cosmos DB-Cassandra API létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
ms.openlocfilehash: d71847da4b395be230bce5773a5ba7a472a412f4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832690"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Cassandra API erőforrások kezelése Azure Resource Manager sablonok használatával

Ez a cikk bemutatja, hogyan hajthat végre különböző műveleteket a Azure Cosmos DB-fiókok,-adatbázisok és-tárolók felügyeletének automatizálásához Azure Resource Manager-sablonok használatával. Ez a cikk csak SQL API-fiókokra vonatkozó példákat tartalmaz, amelyek más API-típusú fiókokra vonatkozó példákat tartalmaznak. lásd: Resource Manager-sablonok használata a Azure Cosmos DB API-val [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Table](manage-table-with-resource-manager.md) articles.

## Azure Cosmos-fiók,-térköz és-tábla létrehozása<a id="create-resource"></a>

Azure Cosmos DB erőforrások létrehozása Azure Resource Manager sablon használatával. Ez a sablon egy Azure Cosmos-fiókot hoz létre a Cassandra APIhoz két olyan táblázattal, amelyek 400 RU/s átviteli sebességet osztanak meg a szóköz szintjén. Másolja a sablont és az üzembe helyezést az alább látható módon, vagy látogasson el az [Azure Gyorsindítás galériába](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) , és telepítse a Azure Portal. Le is töltheti a sablont a helyi számítógépre, vagy létrehozhat egy új sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

> [!NOTE]
> A fiók nevének kisbetű és < 44 karakter közöttinek kell lennie.
> Az RU/s frissítéséhez küldje el újra a sablont a frissített átviteli tulajdonság-értékekkel.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure parancssori felületén keresztül

A Resource Manager-sablon Azure CLI használatával történő üzembe helyezéséhez **másolja** a szkriptet, és válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**parancsot:

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

Az `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg az üzembe helyezés után. Ha úgy dönt, hogy az Azure CLI helyileg telepített verzióját használja a Cloudshellben használata helyett, tekintse meg az [Azure parancssori felület (CLI)](/cli/azure/) című cikkét.


## <a name="next-steps"></a>További lépések

Íme néhány további erőforrás:

- [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/)
- [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)
