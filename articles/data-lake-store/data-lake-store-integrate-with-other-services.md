---
title: Az Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatások |} A Microsoft Docs
description: Megismerheti, hogyan integrálható az Azure Data Lake Storage Gen1 más Azure-szolgáltatások
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: e377a29167ace21e021568f3c65cefd1098d1dab
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127139"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Az Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatások
Az Azure Data Lake Storage Gen1 széles körének forgatókönyvek engedélyezése más Azure-szolgáltatásokkal együtt is használható. A következő cikket a Data Lake Storage Gen1 integrálható legyen az szolgáltatásokat sorolja fel.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Az Azure HDInsight használata a Data Lake Storage Gen1
Telepíthet egy [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) fürtöt, amely a Data Lake Storage Gen1 használja a HDFS-kompatibilis tárolóként. Ebben a kiadásban a Windows és Linux rendszeren, a Hadoop és a Storm fürtökhöz használható Data Lake Storage Gen1 csak egy kiegészítő tárolóként. Ezek a fürtök az Azure Storage (WASB) továbbra is használhatja az alapértelmezett tárolóként. HBase-fürtök Windows és Linux rendszereken, viszont használhatja Data Lake Storage Gen1 az alapértelmezett tároló és/vagy további tárhelyet.

Létesítsen egy HDInsight-fürtöt a Data Lake Storage Gen1 kapcsolatos utasításokért lásd:

* [Létesítsen egy HDInsight-fürtöt a Data Lake Storage Gen1 az Azure Portal használatával](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az Azure PowerShell-lel alapértelmezett tároló üzembe helyezése a Data Lake Storage Gen1 egy HDInsight-fürtöt](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Egy HDInsight-fürtöt a Data Lake Storage Gen1 kiépítése az Azure PowerShell-lel kiegészítő tárolóként](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics használata a Data Lake Storage Gen1
[Az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) lehetővé teszi, hogy a big Data típusú adatok felhőbeli méretezéssel. Dinamikusan kiosztja az erőforrásokat, és lehetővé teszi több terabájtnyi vagy akár exabájtjain való elemzést, a támogatott adatforrások, az egyiket folyamatban van a Data Lake Storage Gen1 számos tárolható adatok elemzését. A Data Lake Analytics kifejezetten dolgozhat a Data Lake Storage Gen1 – így a legmagasabb szintű teljesítményt, átviteli sebesség és folyamatkezelést biztosítja a big data-számítási feladatokhoz van optimalizálva.

A Data Lake Analytics használata a Data Lake Storage Gen1 útmutatásért lásd: [Ismerkedés a Data Lake Analytics használatával a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Az Azure Data Factory használata a Data Lake Storage Gen1
Használhat [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) betölteni az adatokat az Azure-táblák, Azure SQL Database, Azure SQL-adattárház, Azure Storage-Blobok és a helyszíni adatbázisok. Folyamatban van egy polgár az Azure ökoszisztéma, Azure Data Factory segítségével vezényelnie Data Lake Storage Gen1 ezek forrásból származó adatokat.

Az Azure Data Factory használata a Data Lake Storage Gen1 útmutatásért lásd: [adatok importálására és a Data Lake Storage Gen1 Data Factory használatával](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1 be
Az Azure Data Lake Storage Gen1 parancssori eszköz, amely lehetővé teszi, hogy az adatok másolása az Azure Blob Storage-ból egy Data Lake Storage Gen1 fiókba, AdlCopy biztosít. További információkért lásd: [adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Adatok másolása az Azure SQL Database és a Data Lake Storage Gen1 között
Az Apache Sqoop használatával Azure SQL Database és a Data Lake Storage Gen1 közötti adatok importálása és exportálása. További információkért lásd: [adatmásolás a Data Lake Storage Gen1 és a Sqoop használata Azure SQL database közötti](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>A Stream Analytics használata a Data Lake Storage Gen1
Data Lake Storage Gen1 a kimenetek egyik segítségével adatokat streamelt az Azure Stream Analytics használatával tárolni. További információkért lásd: [adatok a Storage-blobból az Azure Data Lake Storage Gen1 be az Azure Stream Analytics Stream](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Data Lake Storage Gen1 használata a Power bi-ban
A Power BI segítségével adatokat importálhat egy Data Lake Storage Gen1-fiókot az adatok elemzése és megjelenítése. További információkért lásd: [Power BI használatával a Data Lake Storage Gen1 adatok elemzése](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>A Data Catalog használja a Data Lake Storage Gen1
Data Lake Storage Gen1 adatait az Azure Data catalogba, hogy az adatok a szervezetben felderíthető regisztrálhat. További információ: [regisztrálja a Data Lake Storage Gen1 adatait az Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Az SQL Server Integration Services (SSIS) használja a Data Lake Storage Gen1
SSIS a Data Lake Storage Gen1-kezelő használatával egy SSIS-csomag összekapcsolása a Data Lake Storage Gen1. További információkért lásd: [használata Data Lake Storage Gen1 az SSIS használatával](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Az SQL Data Warehouse használata a Data Lake Storage Gen1
A PolyBase használatával a Data Lake Storage Gen1 adatok betöltése az SQL Data Warehouse-bA. További információ: [használata Data Lake Storage Gen1 az SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Az Azure Event Hubs használata a Data Lake Storage Gen1
Az Azure Data Lake Storage Gen1 használhatja az Azure Event Hubs által fogadott adatok archiválása és rögzítése. További információ: [használata Data Lake Storage Gen1 az Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Get Started with Data Lake Storage Gen1 portál használatával](data-lake-store-get-started-portal.md)
* [Ismerkedés a Data Lake Storage Gen1 PowerShell-lel](data-lake-store-get-started-powershell.md)  

