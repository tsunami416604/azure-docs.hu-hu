---
title: Az Azure Data Lake Storage Gen2 integrálása más Azure-szolgáltatások |} A Microsoft Docs
description: Megismerheti, hogyan integrálható az Azure Data Lake Storage Gen2 más Azure-szolgáltatások
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885677"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Az Azure Data Lake Storage Gen2 integrálása más Azure-szolgáltatások

Használhatja az Azure-szolgáltatások betöltési, elemezheti és jelenítheti meg az adatokat az Azure Data Lake Storage Gen2 storage-fiókban. Válassza ki a szolgáltatásokat, amelyek a leginkább megfeleljen az adott feladatok elvégzéséhez próbált.

## <a name="ingest-data-into-your-data-lake"></a>Betölteni az adatokat a data lake az

Ezek a szolgáltatások segítséget feltöltése a data lake adatokkal.

### <a name="azure-data-factory"></a>Azure Data Factory

Használhat [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ezekből a forrásokból származó adatokat, hogy:

* Azure-táblák
* Az Azure SQL-adatbázisok
* Azure SQL DataWarehouse
* Azure Storage Blobs
* A helyszíni adatbázisok

Lásd: [adatok importálására és a Data Lake Storage Gen2 a Data Factory segítségével](../../data-factory/connector-azure-data-lake-store.md).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>A data Lake adatok elemzése és megjelenítése

Ezek a szolgáltatások Data Lake Storage Gen2 használhatja a storage-végponthoz.

### <a name="azure-hdinsight"></a>Azure HDInsight

Telepíthet egy [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) fürtöt, amely a Data Lake Storage Gen2 használja a HDFS-kompatibilis tárolóként. Ebben a kiadásban a Windows és Linux rendszeren, a Hadoop és a Storm fürtökhöz használható Data Lake Storage Gen2 csak egy kiegészítő tárolóként. Ezek a fürtök az Azure Storage (WASB) továbbra is használhatja az alapértelmezett tárolóként. Azonban a Windows és Linux rendszerekre a HBase fürtök esetén használhatja Data Lake Storage Gen2 az alapértelmezett tárolóként, és a kiegészítő tárolóként.

Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Használhat [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) felhőszinten big Data típusú adatokkal működnek. Dinamikusan kiosztja az erőforrásokat, és a több exabájt adat elemzését teszi lehetővé. A Data Lake Analytics használata a Data Lake Storage Gen2 adatforrásként van optimalizálva. 

Lásd: [Ismerkedés a Data Lake Analytics használatával a Data Lake Storage Gen2](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="copy-data-to-other-repositories"></a>Más tárházakban másolja az adatokat

Ezek a szolgáltatások használatával adatait átmásolhatja a data lake, és helyezze őket más tárházakban.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

A PolyBase használatával a Data Lake Storage Gen2 adatok betöltése az SQL Data Warehouse-bA. További információ: [használata Data Lake Storage Gen2 az SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake Storage Gen2 áttekintése](data-lake-storage-introduction.md)
* [Az Azure Data Lake Storage Gen2 használata big data-követelményekhez](data-lake-storage-data-scenarios.md)
