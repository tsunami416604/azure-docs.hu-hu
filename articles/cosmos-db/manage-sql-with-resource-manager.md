---
title: Azure Cosmos DB létrehozása és kezelése Resource Manager-sablonokkal
description: Azure Cosmos DB for Core (SQL) API létrehozása és konfigurálása Azure Resource Manager-sablonok használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 577bc34e5e4b01a234460e5e175c23fd8215743f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791204"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Azure Cosmos DB Core (SQL) API-erőforrások kezelése Azure Resource Manager-sablonokkal

Ebből a cikkből megtudhatja, hogyan telepítheti és kezelheti a Azure Cosmos DB-fiókjait, adatbázisait és tárolóit Azure Resource Manager sablonok használatával.

Ez a cikk csak az alapszintű (SQL) API-fiókok Azure Resource Manager sablonját mutatja be. A [Cassandra](manage-cassandra-with-resource-manager.md), a [Gremlin](manage-gremlin-with-resource-manager.md), a [MongoDB](manage-mongodb-with-resource-manager.md)és a [Table](manage-table-with-resource-manager.md) API-khoz példákat is találhat.

> [!IMPORTANT]
>
> * A fiókok neve 44 karakterből állhat, és az összes kisbetűs értékre van korlátozva.
> * Az átviteli sebesség értékeinek módosításához telepítse újra a sablont a frissített RU/s értékkel.
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, a többi tulajdonságot nem módosíthatja egyidejűleg. Ezeket a műveleteket külön kell elvégezni.

Az alábbi Azure Cosmos DB-erőforrások létrehozásához másolja a következő példa sablont egy új JSON-fájlba. Lehetőség van arra is, hogy hozzon létre egy JSON-fájlt, amelyet akkor használhat, ha ugyanazon erőforrás több példányát különböző nevekkel és értékekkel telepíti. A Azure Resource Manager-sablonok számos módon telepíthetők, például a [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), a [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és a [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Azure Cosmos-fiók az autoscale átviteli sebességgel

Ez a sablon egy Azure Cosmos-fiókot hoz létre két régióban a konzisztencia és a feladatátvétel lehetőségeivel, és az adatbázis és a tároló konfigurálva van a legtöbb házirend-beállítással rendelkező automatikusan méretezhető átviteli sebességhez. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>Azure Cosmos-fiók standard (manuális) átviteli sebességgel

Ez a sablon egy Azure Cosmos-fiókot hoz létre két régióban a konzisztencia és a feladatátvétel lehetőségével, a szabványos átviteli sebességhez konfigurált adatbázissal és tárolóval, amely a legtöbb házirend-beállítás engedélyezve van. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB tároló kiszolgálóoldali funkcióval

Ez a sablon egy Azure Cosmos-fiókot, egy adatbázist és egy tárolót hoz létre egy tárolt eljárással, triggerrel és felhasználó által definiált függvénnyel. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Ingyenes szintű Azure Cosmos DB fiók

Ez a sablon létrehoz egy ingyenes rétegbeli Azure Cosmos-fiókot és egy megosztott átviteli sebességgel rendelkező adatbázist, amely akár 25 tárolóval is megosztható. Ez a sablon egykattintásos üzembe helyezésre is használható az Azure Gyorsindítás sablonok katalógusból.

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>További lépések

Néhány további információforrás:

* [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/)
* [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)
