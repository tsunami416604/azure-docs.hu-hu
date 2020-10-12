---
title: Resource Manager-sablonok a Azure Cosmos DB Cassandra API
description: Azure Cosmos DB-Cassandra API létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 8f7eda0615482509aeb3b38ce5b878af10fd9a41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86028215"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Cassandra API erőforrások kezelése Azure Resource Manager sablonok használatával

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe és kezelhet Azure Cosmos DB-fiókokat,-webhelyeket és-táblákat Azure Resource Manager-sablonok segítségével.

Ez a cikk csak a Cassandra API fiókokra mutat példákat más API-típusú fiókokra vonatkozó példák megkereséséhez lásd: Azure Resource Manager-sablonok használata az Azure Cosmos DB API-val az [SQL](manage-sql-with-resource-manager.md), a [Gremlin](manage-gremlin-with-resource-manager.md), a [MongoDB](manage-mongodb-with-resource-manager.md)és a [Table](manage-table-with-resource-manager.md) cikkekhez.

> [!IMPORTANT]
>
> * A fiókok neve 44 karakterből állhat, és az összes kisbetűs értékre van korlátozva.
> * Az átviteli sebesség értékeinek módosításához telepítse újra a sablont a frissített RU/s értékkel.
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, a többi tulajdonságot nem módosíthatja egyidejűleg. Ezeket a műveleteket külön kell elvégezni.

Az alábbi Azure Cosmos DB-erőforrások létrehozásához másolja a következő példa sablont egy új JSON-fájlba. Lehetőség van arra is, hogy hozzon létre egy JSON-fájlt, amelyet akkor használhat, ha ugyanazon erőforrás több példányát különböző nevekkel és értékekkel telepíti. A Azure Resource Manager-sablonok számos módon telepíthetők, például a [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és a [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Azure Cosmos-fiók a Cassandra-hoz az autoscale kiépített átviteli sebességgel

Ez a sablon két régióban hoz létre egy Azure Cosmos-fiókot, amely a konzisztencia és a feladatátvétel lehetőségeit tartalmazza, és egy szóköz és egy tábla van konfigurálva az automatikusan méretezhető átviteli sebességhez. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Azure Cosmos-fiók a Cassandra standard kiosztott átviteli sebességgel

Ez a sablon két régióban hoz létre egy Azure Cosmos-fiókot, amely a konzisztencia és a feladatátvétel beállításait tartalmazza, és a szabványos átviteli sebességhez a szóköz és a tábla van konfigurálva. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>További lépések

Néhány további információforrás:

* [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
* [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)
