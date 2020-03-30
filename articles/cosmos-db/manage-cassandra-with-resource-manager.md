---
title: Erőforrás-kezelősablonok az Azure Cosmos DB Cassandra API-hoz
description: Azure Resource Manager-sablonok használatával hozhatja létre és konfigurálhatja az Azure Cosmos DB Cassandra API-t.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251894"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Az Azure Cosmos DB Cassandra API-erőforrások kezelése az Azure Resource Manager-sablonokkal

Ez a cikk ismerteti, hogyan hajthatja végre a különböző műveleteket az Azure Cosmos DB-fiókok, adatbázisok és tárolók felügyeletének automatizálására az Azure Resource Manager-sablonok használatával. Ez a cikk csak sql API-fiókok példákat tartalmaz, hogy példákat találjon más API-típusú fiókokra: használja az Azure Resource Manager-sablonokat az Azure Cosmos DB [SQL](manage-sql-with-resource-manager.md)API-jával, [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Table](manage-table-with-resource-manager.md) cikkek.

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Azure Cosmos-fiók, kulcstér és tábla létrehozása<a id="create-resource"></a>

Hozzon létre Azure Cosmos DB-erőforrásokat egy Azure Resource Manager-sablon használatával. Ez a sablon létrehoz egy Azure Cosmos-fiókot a Cassandra API-hoz két táblával, amelyek 400 RU/s átviteli sebességgel rendelkeznek a keyspace-szinten. Másolja a sablont, és telepítse az alábbiak szerint, vagy keresse fel az [Azure Gyorsútmutató galériát,](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) és telepítse az Azure Portalról. A sablont letöltheti a helyi számítógépre, vagy létrehozhat egy új `--template-file` sablont, és megadhatja a helyi elérési utat a paraméterrel.

> [!NOTE]
> A fiókneveknek kisbetűsnek és 44 vagy kevesebb karakternek kell lenniük.
> A RU/s frissítéséhez küldje el újra a sablont a frissített átviteli tulajdonságértékekkel.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure Resource Manager-sablon azure CLI használatával történő üzembe helyezéséhez másolja a **parancsfájlt,** és válassza a **Próbálja ki az** Azure Cloud Shell megnyitásához lehetőséget. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés parancsot:**

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

A `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg a kiépítés után. Ha úgy dönt, hogy az Azure CLI helyileg telepített verzióját használja a Cloud Shell használata helyett, tekintse meg az [Azure CLI](/cli/azure/) cikket.


## <a name="next-steps"></a>További lépések

Néhány további információforrás:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltatósémája](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB rövid útmutató sablonjai](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Gyakori Azure Resource Manager-telepítési hibák elhárítási hibáinak elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)
