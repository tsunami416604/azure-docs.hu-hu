---
title: Resource Manager-sablonok a MongoDB Azure Cosmos DB API-hoz
description: Azure Resource Manager-sablonok használatával Azure Cosmos DB API-t hozhat létre és konfigurálhat a MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 408f29322af2199259f59be1f2c7e891a80a6b35
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980579"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB MongoDB API-erőforrások kezelése Azure Resource Manager-sablonok használatával

Ez a cikk bemutatja, hogyan hajthat végre különböző műveleteket a Azure Cosmos DB-fiókok,-adatbázisok és-tárolók felügyeletének automatizálásához Azure Resource Manager-sablonok használatával. Ez a cikk példákat tartalmaz a Azure Cosmos DB API-MongoDB csak az API-típusok más típusú fiókjainak megkereséséhez lásd: a Azure Resource Manager-sablonok használata a Azure Cosmos DB API-jával a [Cassandra](manage-cassandra-with-resource-manager.md), a [Gremlin](manage-gremlin-with-resource-manager.md), az [SQL](manage-sql-with-resource-manager.md)és a [Table](manage-table-with-resource-manager.md) cikkekhez.

## MongoDB-fiók, adatbázis és gyűjtemény létrehozása Azure Cosmos DB API-val<a id="create-resource"></a>

Azure Cosmos DB erőforrások létrehozása Azure Resource Manager sablon használatával. Ez a sablon egy Azure Cosmos-fiókot hoz létre a MongoDB API-hoz két olyan gyűjteménysel, amelyek a 400 RU/s átviteli sebességet használják az adatbázis szintjén. Másolja a sablont és az üzembe helyezést az alább látható módon, vagy látogasson el az [Azure Gyorsindítás galériába](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) , és telepítse a Azure Portal. Le is töltheti a sablont a helyi számítógépre, vagy létrehozhat egy új sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

> [!NOTE]
> A fióknév csak kisbetűket és 44 karaktert tartalmazhat.
> Az RU/s frissítéséhez küldje el újra a sablont a frissített átviteli tulajdonság-értékekkel.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-the-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

A Azure Resource Manager-sablon Azure CLI-vel történő üzembe helyezéséhez **másolja** a szkriptet, és válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**parancsot:

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

Az `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg az üzembe helyezés után. Ha úgy dönt, hogy a Cloud Shell használata helyett az Azure CLI helyileg telepített verzióját használja, tekintse meg az [Azure CLI](/cli/azure/) -cikket.

## <a name="next-steps"></a>Következő lépések

Néhány további információforrás:

- [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/)
- [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)