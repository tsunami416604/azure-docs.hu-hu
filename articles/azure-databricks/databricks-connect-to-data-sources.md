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
ms.openlocfilehash: 2b6d234c3845c220fd63c24db5bdbdae0f7c63fd
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255075"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Kapcsolódás adatforrásokhoz Azure Databricks

Ez a cikk az Azure-beli különböző adatforrásokra mutató hivatkozásokat tartalmaz, amelyek Azure Databrickshoz csatlakoztathatók. Az alábbi hivatkozásokra kattintva az Azure-adatforrásokból (például Azure Blob Storage, Azure Event Hubs stb.) származó adatok kinyerése egy Azure Databricks-fürtön, valamint analitikus feladatok futtatása rajtuk. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks munkaterülettel és egy Spark-fürttel. Kövesse a [Azure Databricks első lépéseinek](quickstart-create-databricks-workspace-portal.md)utasításait.

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks adatforrásai

Az alábbi lista az Azure-ban használható adatforrásokat tartalmazza, amelyeket Azure Databricks használhat. A Azure Databricks használható adatforrások teljes listájáért tekintse meg a [Azure Databricks adatforrásait](/azure/databricks/data/data-sources/index)ismertető témakört.

- [Azure SQL Database](/azure/databricks/data/data-sources/sql-databases)

    Ez a hivatkozás a DataFrame API-t biztosítja a SQL Databasehoz való csatlakozáshoz a JDBC használatával, valamint a beolvasások párhuzamosságának vezérlését a JDBC felületen keresztül. Ez a témakör részletes példákat tartalmaz a Scala API-val, valamint a rövidített Python és Spark SQL-példákkal.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Ez a hivatkozás példákat tartalmaz arra, hogyan használhatja a Azure Active Directory egyszerű szolgáltatást a Azure Data Lake Storage való hitelesítéshez. Emellett útmutatást nyújt arról, hogyan férhet hozzá a Azure Data Lake Storage lévő adatokhoz Azure Databricksból.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Ez a hivatkozás példákat tartalmaz arra, hogyan lehet közvetlenül hozzáférni az Azure Blob Storagehoz a Azure Databricks hozzáférési kulccsal vagy SAS-vel egy adott tárolóhoz. A hivatkozás azt is ismerteti, hogyan férhet hozzá az Azure Blob Storage a Azure Databricks a RDD API használatával.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Ez a hivatkozás azt ismerteti, hogyan használható a [Azure Cosmos db Spark-összekötő](https://github.com/Azure/azure-cosmosdb-spark) a Azure Databricksból a Azure Cosmos db lévő adatok eléréséhez.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Ez a hivatkozás azt ismerteti, hogyan használható az [azure Event Hubs Spark-összekötő](https://github.com/Azure/azure-event-hubs-spark) Azure Databricks az adatok Azure-beli Event Hubs való eléréséhez.

- [Az Azure SQL Data Warehouse](/azure/synapse-analytics/sql-data-warehouse/)

    Ez a hivatkozás azt ismerteti, hogyan használható az Azure SQL Data Warehouse-összekötő a Azure Databricksból való csatlakozáshoz.
    

## <a name="next-steps"></a>További lépések

Az adatok Azure Databricksba való importálásának forrásairól a [Azure Databricks adatforrásaiban](/azure/databricks/data/data-sources/index)talál további információt.


