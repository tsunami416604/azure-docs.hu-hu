---
title: "Data Lake Store más Azure-szolgáltatásokkal integrálja |} Microsoft Docs"
description: "Megérteni, hogyan integrálható a Data Lake Store más Azure-szolgáltatásokkal"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: d43459b900232612d83506438e6a70daa893eb80
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>A Data Lake Store integrálása más Azure-szolgáltatásokkal
Azure Data Lake Store más Azure-szolgáltatásokkal együtt használható a lehetséges forgatókönyvek bővítése engedélyezéséhez. Az alábbi cikket a Data Lake Store integrálható a szolgáltatásokat sorolja fel.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Használjon Data Lake Store az Azure hdinsightban
Megadhat egy [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) Data Lake Store a HDFS-kompatibilis tárolót használó fürtben. Ebben a kiadásban a Windows és Linux, a Hadoop és a Storm fürtök használhatja Data Lake Store további tárterületként csak. A fürtök továbbra is használja az alapértelmezett tároló Azure Storage (WASB). Azonban a Windows és Linux HBase fürtök esetén is használhatja Data Lake Store alapértelmezett tárolására, vagy további tárhely, vagy mindkettőt.

A Data Lake Store HDInsight-fürtök kiépítése utasításokért lásd:

* [HDInsight-fürtök kiépítése a Data Lake Store Azure portál használatával](data-lake-store-hdinsight-hadoop-use-portal.md)
* [A Data Lake Store HDInsight-fürtök kiépítése alapértelmezett tárolóként Azure PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [További tárhely az Azure PowerShell, a Data Lake Store HDInsight-fürtök kiépítése](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Használjon Data Lake Store az Azure Data Lake Analytics
[Az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) lehetővé teszi, hogy a felhőbeli skálázással Big Data típusú adatok alkalmazásában. Dinamikusan kiosztja az erőforrásokat, és lehetővé teszi terabájtjain vagy akár exabájtjain való elemzést által támogatott adatforrások, az egyik legyen a Data Lake Store alatt száma tárolhatja. A Data Lake Analytics kifejezetten arra optimalizálták, hogy működik az Azure Data Lake Store - biztosítása a legmagasabb szintű teljesítményt, adatátvitelt és párhuzamos folyamatkezelést biztosítja azt a big data számítási feladatokat.

A Data Lake Analytics használatával a Data Lake Store útmutatásért lásd: [az Data Lake Analytics Data Lake Store használatának első lépései](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Használjon Data Lake Store az Azure Data Factoryvel
Használhat [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) Azure-táblákban, az Azure SQL Database, Azure SQL-adattárház, Azure Storage Blobs és a helyszíni adatbázisok származó adatok. Az Azure-ökoszisztéma az első osztályú állampolgára alatt, Azure Data Factory-bA az Azure Data Lake Store ezek forrásból származó adatok adatfeldolgozást történő használható.

Azure Data Factory használatával a Data Lake Store útmutatásért lásd: [adatok áthelyezése a Data Factory használatával a Data Lake Store érkező vagy oda irányuló](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Adatok másolása az Azure Storage blobs szolgáltatásban a Data Lake Store
Azure Data Lake Store biztosítja a parancssori eszközzel, AdlCopy, amely lehetővé teszi az adatok másolása az Azure Blob Storage a Data Lake Store-fiók. További információkért lásd: [adatok másolása az Azure Storage Blobs Data Lake store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Adatok másolása az Azure SQL Database és a Data Lake Store között
Apache Sqoop használatával importálhat és exportálhat adatokat az Azure SQL Database és a Data Lake Store között. További információkért lásd: [Data Lake Store és a Sqoop használatával Azure SQL-adatbázis közötti adatok másolása](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Használjon Data Lake Store az Stream Analytics használ
Data Lake Store a kimenetek egyik segítségével Azure Stream Analytics segítségével adatátvitel adatainak tárolásához. További információkért lásd: [adatfolyam-adatokat az Azure Storage-Blobból az Azure Stream Analytics használata a Data Lake Store](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Használjon Data Lake Store a Power BI
A Power BI segítségével adatokat importálhat egy Data Lake Store-fiók elemzéséhez és az adatok megjelenítése. További információkért lásd: [elemezhetik a Power BI használatával a Data Lake Store](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Használjon Data Lake Store a Data Catalog
Data Lake Store adatait az Azure Data Catalog, hogy az adatok felderíthető a szervezet egésze számára történő regisztrálására. További információ: [Data Lake Store-ból adatokat regisztrálni kell az Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Használjon Data Lake Store az SQL Server Integration Services (SSIS)
SSIS az Azure Data Lake Store kezelője segítségével csatlakozzon az Azure Data Lake Store egy SSIS-csomag. További információkért lásd: [használata Data Lake Store az SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Használjon Data Lake Store az SQL Data Warehouse szolgáltatással
A PolyBase segítségével az adatok betöltése az Azure Data Lake Store az SQL Data Warehouse. További információ: [használata Data Lake Store az SQL Data Warehouse szolgáltatással](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Használjon Data Lake Store az Azure Event Hubs
Azure Event Hubs által fogadott rögzítése és archiválási adatok Azure Data Lake Store segítségével. További információ: [használata Data Lake Store az Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Lásd még:
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Ismerkedés a Data Lake Store-portál használatával](data-lake-store-get-started-portal.md)
* [Ismerkedés a Data Lake Store PowerShell használatával](data-lake-store-get-started-powershell.md)  

