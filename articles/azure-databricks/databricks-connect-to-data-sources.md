---
title: 'Kapcsolódás különböző adatforrásokhoz Azure Databricks '
description: Megtudhatja, hogyan csatlakozhat a Azure SQL Databasehoz, a Azure Data Lake Storehoz, a blob Storage-hoz, a Cosmos DBhoz, a Event Hubshoz és a Azure SQL Data Warehousehoz.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: c77d1d1a66d3ee92f5ad3f2016d2160831fa3ad9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299307"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Kapcsolódás adatforrásokhoz Azure Databricks

Ez a cikk az Azure-beli különböző adatforrásokra mutató hivatkozásokat tartalmaz, amelyek Azure Databrickshoz csatlakoztathatók. Az alábbi hivatkozásokra kattintva az Azure-adatforrásokból (például Azure Blob Storage, Azure Event Hubs stb.) származó adatok kinyerése egy Azure Databricks-fürtön, valamint analitikus feladatok futtatása rajtuk. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks munkaterülettel és egy Spark-fürttel. Kövesse a [Azure Databricks első lépéseinek](quickstart-create-databricks-workspace-portal.md)utasításait.

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks adatforrásai

Az alábbi lista az Azure-ban használható adatforrásokat tartalmazza, amelyeket Azure Databricks használhat. A Azure Databricks használható adatforrások teljes listájáért tekintse meg a [Azure Databricks adatforrásait](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)ismertető témakört.

- [Azure SQL Database](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Ez a hivatkozás a DataFrame API-t biztosítja az SQL-adatbázisokhoz való csatlakozáshoz a JDBC használatával, valamint az olvasások párhuzamosságának vezérlését a JDBC felületen keresztül. Ez a témakör részletes példákat tartalmaz a Scala API-val, valamint a rövidített Python és Spark SQL-példákkal.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Ez a hivatkozás példákat tartalmaz arra, hogyan használhatja a Azure Active Directory egyszerű szolgáltatást a Data Lake Store való hitelesítéshez. Emellett útmutatást nyújt arról, hogyan férhet hozzá a Data Lake Store lévő adatokhoz Azure Databricksból.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Ez a hivatkozás példákat tartalmaz arra, hogyan lehet közvetlenül hozzáférni az Azure Blob Storagehoz a Azure Databricks hozzáférési kulccsal vagy SAS-vel egy adott tárolóhoz. A hivatkozás azt is ismerteti, hogyan férhet hozzá az Azure Blob Storage a Azure Databricks a RDD API használatával.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Ez a hivatkozás azt ismerteti, hogyan használható a [Azure Cosmos db Spark-összekötő](https://github.com/Azure/azure-cosmosdb-spark) a Azure Databricksból a Azure Cosmos db lévő adatok eléréséhez.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Ez a hivatkozás azt ismerteti, hogyan használható az [azure Event Hubs Spark-összekötő](https://github.com/Azure/azure-event-hubs-spark) Azure Databricks az adatok Azure-beli Event Hubs való eléréséhez.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Ez a hivatkozás azt ismerteti, hogyan használható az Azure SQL Data Warehouse-összekötő a Azure Databricksból való csatlakozáshoz.
    

## <a name="next-steps"></a>Következő lépések

Az adatok Azure Databricksba való importálásának forrásairól a [Azure Databricks adatforrásaiban](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)talál további információt.


