---
title: Az Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal | Microsoft dokumentumok
description: Ismerje meg, hogyan integrálható az Azure Data Lake Storage Gen1 más Azure-szolgáltatásokkal
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 43024b63a355646f607adbb6623cc6c349374ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535595"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Az Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal
Az Azure Data Lake Storage Gen1 más Azure-szolgáltatásokkal együtt is használható a forgatókönyvek szélesebb körének engedélyezéséhez. A következő cikk felsorolja azokat a szolgáltatásokat, amelyekkel a Data Lake Storage Gen1 integrálható.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>A Data Lake Storage Gen1 használata az Azure HDInsight segítségével
Kiépíthet egy [Azure HDInsight-fürtöt,](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) amely a Data Lake Storage Gen1-et használja HDFS-kompatibilis tárolóként. Ebben a kiadásban a Hadoop és a Storm fürtök Windows és Linux esetén a Data Lake Storage Gen1 csak további tárolóként használható. Ezek a fürtök továbbra is az Azure Storage (WASB) alapértelmezett tárolóként. A Windows és Linux hbase-fürtök esetében azonban használhatja a Data Lake Storage Gen1 alapértelmezett tárolóként vagy további tárolóként, vagy mindkettőt.

A HDInsight-fürtök Data Lake Storage Gen1 szolgáltatással történő kiépítéséről a következő témakörben talál útmutatást:

* [HDInsight-fürt kiépítése a Data Lake Storage Gen1 szolgáltatással az Azure Portal használatával](data-lake-store-hdinsight-hadoop-use-portal.md)
* [HDInsight-fürt kiépítése a Data Lake Storage Gen1 alapértelmezett tárolóként az Azure PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [HDInsight-fürt kiépítése a Data Lake Storage Gen1 használatával további tárhelyként az Azure PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>A Data Lake Storage Gen1 használata az Azure Data Lake Analytics szolgáltatással
[Az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) lehetővé teszi, hogy a Big Data-adatokfelhőben. Dinamikusan biztosít erőforrásokat, és lehetővé teszi, hogy analit terabájt, vagy akár exabájt nyi adatok, amelyek számos támogatott adatforrások, amelyek közül az egyik a Data Lake Storage Gen1. A Data Lake Analytics kifejezetten a Data Lake Storage Gen1 szolgáltatással való együttműködésre van optimalizálva , amely a legmagasabb szintű teljesítményt, átviteli teljesítményt és párhuzamosítást biztosítja a big data-számítási feladatokszámára.

A Data Lake Analytics data lake storage gen1 használatával kapcsolatos tudnivalókról az [Első lépések a Data Lake Storage Gen1 használatával.](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>A Data Lake Storage Gen1 használata az Azure Data Factory segítségével
[Az Azure Data Factory](https://azure.microsoft.com/services/data-factory/) segítségével adatokat dolgozhat be az Azure-táblákból, az Azure SQL Database-ből, az Azure SQL DataWarehouse-ból, az Azure Storage Blobs-ból és a helyszíni adatbázisokból. Az Azure Data Factory első osztályú állampolgárként az azure-beli data factory segítségével vezényelheti az adatok betöltését ezekből a forrásból a Data Lake Storage Gen1-be.

Az Azure Data Factory data lake storage gen1 használatával kapcsolatos tudnivalókért olvassa el az [Adatok áthelyezése a Data Lake Storage Gen1 szolgáltatásba és a Data Factory szolgáltatásból című](../data-factory/connector-azure-data-lake-store.md)témakört.

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage Blobs szolgáltatásból a Data Lake Storage Gen1 szolgáltatásba
Az Azure Data Lake Storage Gen1 egy parancssori eszközt, az AdlCopy-t biztosítja, amely lehetővé teszi az azure blob storage-ból származó adatok másolását egy Data Lake Storage Gen1 fiókba. További információ: [Adatok másolása az Azure Storage Blobs from Data Lake Storage Gen1 című témakörben.](data-lake-store-copy-data-azure-storage-blob.md)

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Adatok másolása az Azure SQL Database és a Data Lake Storage Gen1 között
Az Apache Sqoop segítségével adatokat importálhat és exportálhat az Azure SQL Database és a Data Lake Storage Gen1 között. További információ: [Adatok másolása a Data Lake Storage Gen1 és az Azure SQL-adatbázis között a Sqoop használatával.](data-lake-store-data-transfer-sql-sqoop.md)

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>A Data Lake Storage Gen1 használata a Stream Analytics szolgáltatással
A Data Lake Storage Gen1-et az Azure Stream Analytics használatával streamelt adatok tárolására használhatja. További információ: [Adatok streamelése az Azure Storage Blobból a Data Lake Storage Gen1 szolgáltatásba az Azure Stream Analytics használatával](data-lake-store-stream-analytics.md)című témakörben talál.

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>A Data Lake Storage Gen1 használata a Power BI-val
A Power BI segítségével adatokat importálhat egy Data Lake Storage Gen1 fiókból az adatok elemzéséhez és megjelenítéséhez. További információt az [Adatok elemzése a Data Lake Storage Gen1 szolgáltatásban a Power BI használatával című témakörben talál.](data-lake-store-power-bi.md)

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>A Data Lake Storage Gen1 használata adatkatalógussal
A Data Lake Storage Gen1 adatai regisztrálhatók az Azure Data Catalog-ba, hogy az adatok felderíthetők legyenek a szervezeten belül. További információ: [Adatok regisztrálása a Data Lake Storage Gen1 szolgáltatásból az Azure Data Catalog szolgáltatásban.](data-lake-store-with-data-catalog.md)

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>A Data Lake Storage Gen1 használata az SQL Server Integration Services (SSIS) szolgáltatással
Az SSIS Data Lake Storage Gen1 kapcsolatkezelőjével ssis-csomagot csatlakoztathat a Data Lake Storage Gen1-hez. További információ: [Use Data Lake Storage Gen1 with SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>A Data Lake Storage Gen1 használata az SQL Data Warehouse segítségével
A PolyBase segítségével adatokat tölthet be a Data Lake Storage Gen1-ből az SQL Data Warehouse-ba. További információ: [Use Data Lake Storage Gen1 with SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>A Data Lake Storage Gen1 használata az Azure Event Hubs segítségével
Az Azure Data Lake Storage Gen1 segítségével archiválhatja és rögzítheti az Azure Event Hubs által fogadott adatokat. További információ: [Use Data Lake Storage Gen1 with Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Első lépések a Data Lake Storage Gen1 használatával a Portal használatával](data-lake-store-get-started-portal.md)
* [Első lépések a Data Lake Storage Gen1 használatával a PowerShell használatával](data-lake-store-get-started-powershell.md)  

