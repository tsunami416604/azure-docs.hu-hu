---
title: Adatátalakítás az Hadoop-struktúra tevékenységével
description: Megtudhatja, hogyan használhatja a kaptár tevékenységeket egy Azure-beli adatgyárban a kaptár-lekérdezések futtatásához egy igény szerinti vagy saját HDInsight-fürtön.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 877c1719a76f23f8446164b641dc2dac84261e0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849280"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Az adatátalakítás a Hadoop-struktúra tevékenységgel Azure Data Factory

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hive-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Data Factory [folyamat](concepts-pipelines-activities.md) HDInsight-struktúrájának tevékenysége a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az [oktatóanyagot: az adatátalakítást](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Szintaxis részletei
| Tulajdonság            | Leírás                                                  | Kötelező |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | A tevékenység neve                                         | Yes      |
| leírás         | A tevékenység által használt szöveg leírása                | No       |
| típus                | A kaptár tevékenység esetén a tevékenység típusa HDinsightHive.        | Yes      |
| linkedServiceName   | Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Yes      |
| Scriptlinkedservice szolgáltatás | Hivatkozás egy Azure Storage-beli társított szolgáltatásra, amely a végrehajtandó struktúra parancsfájljának tárolására szolgál. Itt csak az **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** és **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** társított szolgáltatások támogatottak. Ha nem megadja ezt a társított szolgáltatást, a rendszer a HDInsight társított szolgáltatásban definiált Azure Storage társított szolgáltatást használja.  | No       |
| scriptPath          | Adja meg a Scriptlinkedservice szolgáltatás által hivatkozott Azure Storage-ban tárolt parancsfájl elérési útját. A fájl neve megkülönbözteti a kis-és nagybetűket. | Yes      |
| getDebugInfo        | Megadja, hogy a rendszer mikor másolja a naplófájlokat a Scriptlinkedservice szolgáltatás által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | No       |
| argumentumok           | Argumentumok tömbjét adja meg egy Hadoop feladatokhoz. Az argumentumok parancssori argumentumként lesznek átadva az egyes feladatokhoz. | No       |
| meghatározza             | Adja meg a paramétereket kulcs/érték párokként a kaptár-parancsfájlon belüli hivatkozáshoz. | No       |
| queryTimeout        | Lekérdezés időtúllépési értéke (percben). Akkor alkalmazható, ha a HDInsight-fürt Enterprise Security Package engedélyezve van. | Nem       |

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Batch-végrehajtási tevékenység Machine Learning](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
