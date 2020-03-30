---
title: 'Csatlakozás az Azure Databricks különböző adatforrásaihoz '
description: Megtudhatja, hogyan csatlakozhat az Azure SQL Database- hez, az Azure Data Lake Store-hoz, a blobstorage-hoz, a Cosmos DB-hoz, az Event Hubs-hoz és az Azure SQL Data Warehouse-hoz az Azure Databricks-ből.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129386"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Csatlakozás az Azure Databricks adatforrásaihoz

Ez a cikk az Azure Databricks-hez csatlakoztatható azure Databricks-hez csatlakoztatható összes különböző adatforrásra mutató hivatkozásokat tartalmaz. Kövesse az alábbi hivatkozásokban található példákat az Azure-adatforrásokból (például az Azure Blob Storage, az Azure Event Hubs stb.) egy Azure Databricks-fürtbe történő kinyeréséhez, és elemzési feladatok futtatásához. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks munkaterület és egy Spark-fürt. Kövesse az [Azure Databricks első lépései című útmutatóutasításait.](quickstart-create-databricks-workspace-portal.md)

## <a name="data-sources-for-azure-databricks"></a>Az Azure Databricks adatforrásai

Az alábbi lista az Azure Databricks-szel használható adatforrásokat tartalmazza. Az Azure Databricks-szel használható adatforrások teljes listáját az [Azure Databricks adatforrásai](/azure/databricks/data/data-sources/index)ban.

- [Azure SQL-adatbázis](/azure/databricks/data/data-sources/sql-databases)

    Ez a hivatkozás biztosítja a DataFrame API-t a JDBC használatával az SQL-adatbázisokhoz való csatlakozáshoz, valamint az olvasások párhuzamosságának szabályozásához a JDBC felületen keresztül. Ez a témakör részletes példákat a Scala API használatával, rövidített Python és Spark SQL példák a végén.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Ez a hivatkozás példákat tartalmaz arra, hogyan használhatja az Azure Active Directory egyszerű szolgáltatás hitelesítéséhez az Azure Data Lake Storage használatával. Azt is ismerteti, hogyan érheti el az adatokat az Azure Data Lake Storage-ban az Azure Databricks.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Ez a hivatkozás példákat tartalmaz arra, hogyan érheti el közvetlenül az Azure Blob Storage-t az Azure Databricks-ből hozzáférési kulcs vagy egy adott tároló SAS használatával. A hivatkozás azt is tartalmazza, hogyan érheti el az Azure Blob Storage-ot az Azure Databricks rdd API használatával.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Ez a hivatkozás útmutatást nyújt az [Azure Cosmos DB Spark-összekötő](https://github.com/Azure/azure-cosmosdb-spark) azure Databricks használatával az Azure Cosmos DB-ben lévő adatok eléréséhez.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Ez a hivatkozás útmutatást nyújt az [Azure Event Hubs Spark-összekötő](https://github.com/Azure/azure-event-hubs-spark) azure Databricks használatával az Azure Event Hubs-adatok eléréséhez.

- [Azure SQL adattárház](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Ez a hivatkozás útmutatást nyújt az Azure SQL Data Warehouse-összekötő használatával az Azure Databricks-ből való csatlakozáshoz.
    

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a források, ahonnan adatokat importálhat az Azure Databricks, olvassa el [az Adatforrások az Azure Databricks.](/azure/databricks/data/data-sources/index)


