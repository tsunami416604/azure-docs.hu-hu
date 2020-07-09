---
title: Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások | Microsoft Docs
description: Ismerje meg, hogy mely Azure-szolgáltatások integrálva vannak Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1854c6f86e1a3578b6314b072f104174032344e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907584"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások

Az Azure-szolgáltatások az adatok betöltésére, az elemzések elvégzésére és a vizuális ábrázolások létrehozására használhatók. Ez a cikk felsorolja a támogatott Azure-szolgáltatásokat, felfedi a támogatási szintet, és olyan cikkekre mutató hivatkozásokat tartalmaz, amelyek segítenek ezeknek a szolgáltatásoknak a Azure Data Lake Storage Gen2 való használatához.

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

Ez a táblázat felsorolja a Azure Data Lake Storage Gen2 használható Azure-szolgáltatásokat. A táblázatokban megjelenő elemek idővel változnak, ahogy a támogatás továbbra is bővül.

> [!NOTE]
> A támogatási szint csak arra vonatkozik, hogy a szolgáltatás milyen módon támogatott a Data Lake Storage Gen 2 használatával.

|Azure-szolgáltatás |Támogatási szint |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Azure Data Factory|Általánosan elérhető|[Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Általánosan elérhető|[Használat az Azure Databricksszel](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Gyors útmutató: Azure Data Lake Storage Gen2ban lévő adatelemzés Azure Databricks használatával](data-lake-storage-quickstart-create-databricks-account.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése a Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Oktatóanyag: Data Lake Storage Gen2-adatelérés Azure Databricks a Spark használatával](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Általánosan elérhető|[Események rögzítése Azure-Event Hubs az Azure-ban Blob Storage vagy Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Általánosan elérhető|[Oktatóanyag: a Databricks-különbözeti tábla frissítéséhez a adattó rögzítési mintájának megvalósítása](data-lake-storage-events.md)|
|Azure Logic Apps|Általánosan elérhető|[Áttekintés – mi az Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Általánosan elérhető|[Az Azure Storage-szolgáltatásokban tárolt adathozzáférés](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Általánosan elérhető|[Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Kimenő Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Általánosan elérhető|[A helyszíni HDFS-tárolóból az Azure Storage-ba történő Migrálás Azure Data Box használata](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Általánosan elérhető|[Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[A HDFS CLI használata Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Általánosan elérhető|[Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Általánosan elérhető|[Data Lake Storage Gen2 adatai elemzése a Power BI használatával](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (korábban SQL Data Warehouse)|Általánosan elérhető|[Használat az Azure szinapszis Analytics szolgáltatással (korábban SQL Data Warehouse)](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Általánosan elérhető|[Azure Storage-kapcsolatkezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Előnézet|[Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Előnézet|[Azure Data Lake lekérdezése az Azure-Adatkezelő használatával](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Még nem támogatott|[Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [A blob Storage funkciói a Azure Data Lake Storage Gen2ban érhetők el](data-lake-storage-supported-blob-storage-features.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)