---
title: Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal | Microsoft Docs
description: Ismerje meg, hogy mely Azure-szolgáltatások integrálva vannak Azure Data Lake Storage Gen2okkal.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: c7223274417ef4c911c32acbcde1511682d6d9e0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796070"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal

Az Azure-szolgáltatások az adatok betöltésére, az elemzések elvégzésére és a vizuális ábrázolások létrehozására használhatók. Ez a cikk felsorolja a támogatott Azure-szolgáltatásokat, és olyan cikkekre mutató hivatkozásokat tartalmaz, amelyek segítenek a szolgáltatások Azure Data Lake Storage Gen2 való használatához.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások

A következő táblázat felsorolja a Azure Data Lake Storage Gen2 támogató Azure-szolgáltatásokat.

| Azure-szolgáltatás |  Kapcsolódó cikkek |
|---------------|-------------------|
|Azure Data Factory | [Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Használat Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Gyors útmutató: Azure Data Lake Storage Gen2ban lévő adatelemzés Azure Databricks használatával](data-lake-storage-quickstart-create-databricks-account.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése a Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Oktatóanyag: Data Lake Storage Gen2-adatelérés Azure Databricks a Spark használatával](data-lake-storage-use-databricks-spark.md) |
|Azure Event Hubs rögzítés| [Események rögzítése Azure-Event Hubs az Azure-ban Blob Storage vagy Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Áttekintés – mi az Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Az Azure Storage-szolgáltatásokban tárolt adathozzáférés](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data)|
|Azure Cognitive Search | [Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [Gyors útmutató: Stream Analytics-feladatok létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Kimenő Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [A helyszíni HDFS-tárolóból az Azure Storage-ba történő Migrálás Azure Data Box használata](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[A HDFS CLI használata Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT Hub | [Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Data Lake Storage Gen2 adatai elemzése a Power BI használatával](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Használat Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Azure Storage-kapcsolatkezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>További lépések

- További információ arról, hogy mely eszközök használhatók a adattárban tárolt adatfeldolgozáshoz. Lásd: [a Azure Data Lake Storage Gen2 használata Big Data követelményekhez](data-lake-storage-data-scenarios.md).

- További információ a Data Lake Storage Gen2ről és az első lépésekről. Lásd: [a Azure Data Lake Storage Gen2 bemutatása](data-lake-storage-introduction.md)
