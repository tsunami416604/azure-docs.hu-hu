---
title: Resource Manager-sablonok a Azure Cosmos DB Gremlin API-hoz
description: Azure Cosmos DB Gremlin API létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 77390d58412530208c2886a51460d3aab8114d27
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200768"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Gremlin API-erőforrások kezelése Azure Resource Manager-sablonok használatával

Ez a cikk bemutatja, hogyan hajthat végre különböző műveleteket a Azure Cosmos DB-fiókok,-adatbázisok és-tárolók felügyeletének automatizálásához Azure Resource Manager-sablonok használatával. Ez a cikk csak a Gremlin API-fiókok esetében tartalmaz példákat a más API-típusú fiókokra vonatkozó példák megkereséséhez lásd: Azure Resource Manager sablonok használata a Azure Cosmos DB API-jával a [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Table](manage-table-with-resource-manager.md) articles.

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>MongoDB-fiók, adatbázis és gyűjtemény létrehozása Azure Cosmos DB API-val<a id="create-resource"></a>

Azure Cosmos DB erőforrások létrehozása Azure Resource Manager sablon használatával. Ez a sablon létrehoz egy Azure Cosmos-fiókot a Gremlin API-hoz egy adatbázissal és egy 400 RU/s átviteli sebességgel rendelkező gráfmal. Másolja a sablont és az üzembe helyezést az alább látható módon, vagy látogasson el az [Azure Gyorsindítás galériába](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) , és telepítse a Azure Portal. Le is töltheti a sablont a helyi számítógépre, vagy létrehozhat egy új sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

> [!NOTE]
> A fióknév csak kisbetűket és 44 karaktert tartalmazhat.
> Az RU/s frissítéséhez telepítse újra a sablont a frissített átviteli tulajdonság-értékekkel.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
       "accountName": {
           "type": "string",
           "defaultValue": "",
           "metadata": {
               "description": "Cosmos DB account name"
           }
       },
       "location": {
           "type": "string",
           "defaultValue": "[resourceGroup().location]",
           "metadata": {
               "description": "Location for the Cosmos DB account."
           }
       },
       "primaryRegion": {
           "type": "string",
           "metadata": {
               "description": "The primary replica region for the Cosmos DB account."
           }
       },
       "secondaryRegion": {
           "type": "string",
           "metadata": {
               "description": "The secondary replica region for the Cosmos DB account."
           }
       },
       "defaultConsistencyLevel": {
           "type": "string",
           "defaultValue": "Session",
           "allowedValues": [
               "Eventual",
               "ConsistentPrefix",
               "Session",
               "BoundedStaleness",
               "Strong"
           ],
           "metadata": {
               "description": "The default consistency level of the Cosmos DB account."
           }
       },
       "maxStalenessPrefix": {
           "type": "int",
           "defaultValue": 100000,
           "minValue": 10,
           "maxValue": 1000000,
           "metadata": {
               "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
           }
       },
       "maxIntervalInSeconds": {
           "type": "int",
           "defaultValue": 300,
           "minValue": 5,
           "maxValue": 86400,
           "metadata": {
               "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
           }
       },
       "automaticFailover": {
           "type": "bool",
           "defaultValue": true,
           "allowedValues": [
               true,
               false
           ],
           "metadata": {
               "description": "Enable automatic failover for regions"
           }
       },
       "databaseName": {
           "type": "string",
           "defaultValue": "database1",
           "metadata": {
               "description": "The name for the Gremlin database"
           }
       },
       "graphName": {
           "type": "string",
           "defaultValue": "graph1",
           "metadata": {
               "description": "The name for the Gremlin graph"
           }
       },
       "throughput": {
           "type": "int",
           "defaultValue": 400,
           "minValue": 400,
           "maxValue": 1000000,
           "metadata": {
               "description": "Throughput for the Gremlin graph"
           }
       }
   },
   "variables": {
       "accountName": "[toLower(parameters('accountName'))]",
       "consistencyPolicy": {
           "Eventual": {
               "defaultConsistencyLevel": "Eventual"
           },
           "ConsistentPrefix": {
               "defaultConsistencyLevel": "ConsistentPrefix"
           },
           "Session": {
               "defaultConsistencyLevel": "Session"
           },
           "BoundedStaleness": {
               "defaultConsistencyLevel": "BoundedStaleness",
               "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
               "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
           },
           "Strong": {
               "defaultConsistencyLevel": "Strong"
           }
       },
       "locations": [
           {
               "locationName": "[parameters('primaryRegion')]",
               "failoverPriority": 0,
               "isZoneRedundant": false
           },
           {
               "locationName": "[parameters('secondaryRegion')]",
               "failoverPriority": 1,
               "isZoneRedundant": false
           }
       ]
   },
   "resources": [
       {
           "type": "Microsoft.DocumentDB/databaseAccounts",
           "name": "[variables('accountName')]",
           "apiVersion": "2020-03-01",
           "location": "[parameters('location')]",
           "kind": "GlobalDocumentDB",
           "properties": {
               "capabilities": [
                   {
                       "name": "EnableGremlin"
                   }
               ],
               "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
               "locations": "[variables('locations')]",
               "databaseAccountOfferType": "Standard",
               "enableAutomaticFailover": "[parameters('automaticFailover')]"
           }
       },
       {
           "type": "Microsoft.DocumentDB/databaseAccounts/gremlinDatabases",
           "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
           "apiVersion": "2020-03-01",
           "dependsOn": [
               "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]"
           ],
           "properties": {
               "resource": {
                   "id": "[parameters('databaseName')]"
               }
           }
       },
       {
           "type": "Microsoft.DocumentDb/databaseAccounts/gremlinDatabases/graphs",
           "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('graphName'))]",
           "apiVersion": "2020-03-01",
           "dependsOn": [
               "[resourceId('Microsoft.DocumentDB/databaseAccounts/gremlinDatabases', variables('accountName'),  parameters('databaseName'))]"
           ],
           "properties": {
               "resource": {
                   "id": "[parameters('graphName')]",
                   "indexingPolicy": {
                       "indexingMode": "consistent",
                       "includedPaths": [
                           {
                               "path": "/*"
                           }
                       ],
                       "excludedPaths": [
                           {
                               "path": "/myPathToNotIndex/*"
                           }
                       ]
                   },
                   "partitionKey": {
                       "paths": [
                           "/myPartitionKey"
                       ],
                       "kind": "Hash"
                   },
                   "options": {
                       "throughput": "[parameters('throughput')]"
                   }
               }
           }
       }
   ]
}
```

## <a name="deploy-with-the-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

A Azure Resource Manager-sablon Azure CLI-vel történő üzembe helyezéséhez **másolja** a szkriptet, és válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**parancsot:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graphName=$graphName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

A `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg az üzembe helyezés után. Ha úgy dönt, hogy a Cloud Shell használata helyett az Azure CLI helyileg telepített verzióját használja, tekintse meg az [Azure CLI](/cli/azure/) -cikket.

## <a name="next-steps"></a>További lépések

Néhány további információforrás:

- [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/)
- [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)