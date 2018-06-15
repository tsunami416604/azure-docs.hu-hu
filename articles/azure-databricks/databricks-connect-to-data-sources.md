---
title: Csatlakozás Azure Databricks különböző forrásokból |} Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a különböző adatforrásokhoz Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: nitinme
ms.openlocfilehash: 865313a7c6eabd847529b88ff5fff0b7db438fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174276"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Kapcsolódás az adatforrásokhoz való Azure Databricks

Ez a cikk összes a különböző forrásokból, amely Azure Databricks lehet csatlakoztatni az Azure-ban mutató hivatkozásokat tartalmaz. Ezek a hivatkozások, ha adatokat szeretne kinyerni az Azure-adatforrással (például Azure Blob Storage, Azure Event Hubs, stb.) az Azure Databricks fürtbe a példák, feladatok és futtatása analitikai rajtuk. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks munkaterületet, és egy Spark-fürt. Kövesse az utasításokat, [Ismerkedés az Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks adatforrása

Az alábbi lista ismerteti az adatforrásokat, Azure Databricks használható az Azure-ban. Az Azure Databricks használható adatforrások teljes listájáért lásd: [Azure Databricks adatforrásait](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Az Azure SQL-adatbázis](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Ez a hivatkozás SQL Database-adatbázisok JDBC és olvasási műveletek a JDBC felületen párhuzamosságát használatával történő kapcsolódáshoz a DataFrame API alkalmazást biztosít. Ez a témakör részletes példák a Scala API használatával rövidített Python és a Spark SQL példák végén.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Ez a hivatkozás példákat az Azure Active Directory szolgáltatás egyszerű használatával a Data Lake Store való hitelesítéshez szükséges. A Data Lake Store az adatok elérésével az Azure Databricks útmutatásokat is tartalmaz.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Ez a hivatkozás példákat közvetlenül elérje Azure Blob Storage Azure Databricks való hozzáférési kulcs vagy a SAS használatával az adott tárolóban. A hivatkozás is biztosít adatokat az Azure Blob Storage Azure Databricks eléréséhez RDD API használatával.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Ez a hivatkozás útmutatás használata a [Azure Cosmos DB Spark összekötő](https://github.com/Azure/azure-cosmosdb-spark) a hozzáférési adatok Azure Cosmos DB Azure Databricks.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Ez a hivatkozás útmutatás használata a [Azure Event Hubs Spark összekötő](https://github.com/Azure/azure-event-hubs-spark) a hozzáférési adatok az Azure Event Hubs Azure Databricks.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Ez a hivatkozás útmutatás Azure Databricks a csatlakozás az Azure SQL Data Warehouse-összekötő használatával.
    

## <a name="next-steps"></a>További lépések

Importálhat adatokat az Azure Databricks erőforrásait, lásd: [Azure Databricks adatforrásait](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


