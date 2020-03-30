---
title: Az Azure Data Lake Storage Gen2-t támogató Azure-szolgáltatások | Microsoft dokumentumok
description: Ismerje meg, hogy mely Azure-szolgáltatások integrálhatók az Azure Data Lake Storage Gen2 szolgáltatással
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0cf8a4a78894cb7f0e1ddcf7aa18bbb4006bbeeb
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345636"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatások

Az Azure-szolgáltatások segítségével adatokat vihet be, elemzéseket végezhet, és vizuális ábrázolásokat hozhat létre. Ez a cikk a támogatott Azure-szolgáltatások listáját tartalmazza, nyilvánosságra hozza azok támogatási szintjét, és hivatkozásokat tartalmaz olyan cikkekre, amelyek segítenek ezeket a szolgáltatásokat használni az Azure Data Lake Storage Gen2 használatával.

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

Ez a táblázat az Azure Data Lake Storage Gen2-vel használható Azure-szolgáltatásokat sorolja fel. Az ezekben a táblákban megjelenő elemek idővel változnak, ahogy a támogatás tovább bővül.

> [!NOTE]
> A támogatási szint csak arra vonatkozik, hogy a Szolgáltatás hogyan támogatja a Data Lake Storage Gen 2.

|Azure-szolgáltatás |Támogatási szint |Kapcsolódó cikkek |
|---------------|-------------------|---|
|Azure Data Factory|Általánosan elérhető|[Adatok betöltése az Azure Data Lake Storage Gen2 szolgáltatásba az Azure Data Factory segítségével](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Általánosan elérhető|[Használat az Azure Databricksszel](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Rövid útmutató: Adatok elemzése az Azure Data Lake Storage Gen2 szolgáltatásban az Azure Databricks használatával](data-lake-storage-quickstart-create-databricks-account.md) <br>[Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Oktatóanyag: A Data Lake Storage Gen2 adatainak elérése az Azure Databricks használatával a Spark használatával](data-lake-storage-use-databricks-spark.md)|
|Az Azure Event Hubs rögzítése| Általánosan elérhető|[Események rögzítése az Azure-eseményközpontokon keresztül az Azure Blob Storage-ban vagy az Azure Data Lake Storage-ban](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps|Általánosan elérhető|[Áttekintés – Mi az Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Általánosan elérhető|[Adatok elérése az Azure-tárolási szolgáltatásokban](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Általánosan elérhető|[Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Kilépés az Azure Data Lake Gen2-hez](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| Általánosan elérhető|[Az Azure Data Box használata adatok áttelepítése a helyszíni HDFS-áruházból az Azure Storage-ba](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | Általánosan elérhető|[Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[A HDFS CLI használata a Data Lake Storage Gen2-vel](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Apache Hive használatával az Azure HDInsighton](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub | Általánosan elérhető|[Az IoT Hub üzenet-útválasztásának használata eszközről felhőbe irányuló üzenetek küldésére különböző végpontokra](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| Általánosan elérhető|[Adatok elemzése a Data Lake Storage Gen2 szolgáltatásban a Power BI használatával](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Általánosan elérhető|[Használat az Azure SQL Data Warehouse-zal](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Általánosan elérhető|[Azure Storage-kapcsolatkezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Előzetes verzió|[Az Azure Data Lake Storage Gen2 dokumentumainak indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Előzetes verzió|[Adatok lekérdezése az Azure Data Lake szolgáltatásban az Azure Data Explorer használatával](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Még nem támogatott|[Az Azure Data Lake Storage Gen2 dokumentumainak indexelése és keresése (előzetes verzió)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Lásd még

- [Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással kapcsolatban](data-lake-storage-known-issues.md)
- [Az Azure Data Lake Storage Gen2-ben elérhető Blob-tárolási funkciók](data-lake-storage-supported-blob-storage-features.md)
- [Az Azure Data Lake Storage Gen2 szolgáltatást támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Többprotokollos hozzáférés az Azure Data Lake Storage szolgáltatásban](data-lake-storage-multi-protocol-access.md)