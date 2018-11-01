---
title: 'Csatlakozás Azure Databricks a különböző adatforrások '
description: Ismerje meg, hogyan csatlakozhat Azure databricksből különböző adatforrásokból.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 8055b5d7e6c53abc385a99d9190a38603ebb968b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417446"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Az Azure Databricksből csatlakozhat adatforrásokhoz

Ez a cikk különböző adatforrásokból az Azure-ban, mely csatlakoztatható az Azure databricks mutató hivatkozásokat tartalmaz. Kövesse az alábbi hivatkozásokat az Azure-adatforrás (például az Azure Blob Storage, Azure Event Hubs stb.) egy Azure Databricks-fürtön az adatok kinyerése példák, és elemzési feladatok futtatásával rajtuk. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks-munkaterület és a egy Spark-fürtöt. Kövesse az utasításokat, [Azure Databricks használatának első lépései](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Az Azure Databricks-adatforrások

Az alábbi lista tartalmazza az Azure-ban az Azure Databricks használható adatforrások. Az Azure Databricks használható adatforrások teljes listáját lásd: [Azure Databricks-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Az Azure SQL database](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Ezt a hivatkozást a DataFrame API-t biztosít az SQL-adatbázisok használatával, JDBC, és hogyan szabályozhatja a párhuzamosság olvasások a JDBC-felületen keresztül csatlakozik. Ez a témakör részletes példák a Scala API használatával rövidített pythonnal és a Spark SQL-példák a végén.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Ez a hivatkozás példákat az Azure Active Directory egyszerű szolgáltatás használatával történő hitelesítés a Data Lake Store. Az Azure Databricks a Data Lake Store az adatok elérésével utasításokat is biztosít.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Ez a hivatkozás példákat közvetlenül hozzáférhet az Azure Blob Storage Azure databricksből való tárelérési kulccsal vagy SAS használatával egy adott tárolóhoz. A hivatkozás is nyújt adatait az Azure Blob Storage Azure databricksből való eléréséhez az RDD-API-val.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Ez a hivatkozás használata útmutatás a [Azure Cosmos DB Spark-összekötő](https://github.com/Azure/azure-cosmosdb-spark) adatok elérését az Azure Cosmos DB Azure databricksből.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Ez a hivatkozás használata útmutatás a [Azure Event Hubs Spark-összekötő](https://github.com/Azure/azure-event-hubs-spark) adatok elérését az Azure Event Hubs Azure databricksből.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Ez a hivatkozás útmutatás csatlakozni az Azure Databricks az Azure SQL Data Warehouse-összekötő használatával.
    

## <a name="next-steps"></a>További lépések

Importálhat adatokat az Azure Databricksbe forrásainak kapcsolatos további információkért lásd: [Azure Databricks-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


