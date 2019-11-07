---
title: Az adatátalakítás a Hadoop-struktúra tevékenységgel Azure Data Factory
description: Megtudhatja, hogyan használhatja a kaptár tevékenységeket egy Azure-beli adatgyárban a kaptár-lekérdezések futtatásához egy igény szerinti vagy saját HDInsight-fürtön.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: c423192624ecc76e839f9fee434956f4d57aefdc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683912"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Az adatátalakítás a Hadoop-struktúra tevékenységgel Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hive-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-hive.md)

A Data Factory [folyamat](concepts-pipelines-activities.md) HDInsight-struktúrájának tevékenysége a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az [oktatóanyagot: az adatátalakítást](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="syntax"></a>Szintaxis

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
| név                | A tevékenység neve                                         | Igen      |
| leírás         | A tevékenység által használt szöveg leírása                | Nem       |
| type                | A kaptár tevékenység esetén a tevékenység típusa HDinsightHive.        | Igen      |
| linkedServiceName   | Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Igen      |
| Scriptlinkedservice szolgáltatás | Hivatkozás egy Azure Storage-beli társított szolgáltatásra, amely a végrehajtandó struktúra parancsfájljának tárolására szolgál. Ha nem megadja ezt a társított szolgáltatást, a rendszer a HDInsight társított szolgáltatásban definiált Azure Storage társított szolgáltatást használja. | Nem       |
| scriptPath          | Adja meg a Scriptlinkedservice szolgáltatás által hivatkozott Azure Storage-ban tárolt parancsfájl elérési útját. A fájl neve megkülönbözteti a kis-és nagybetűket. | Igen      |
| getDebugInfo        | Megadja, hogy a rendszer mikor másolja a naplófájlokat a Scriptlinkedservice szolgáltatás által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |
| argumentumok           | Argumentumok tömbjét adja meg egy Hadoop feladatokhoz. Az argumentumok parancssori argumentumként lesznek átadva az egyes feladatokhoz. | Nem       |
| meghatározza             | Adja meg a paramétereket kulcs/érték párokként a kaptár-parancsfájlon belüli hivatkozáshoz. | Nem       |
| queryTimeout        | Lekérdezés időtúllépési értéke (percben). Akkor alkalmazható, ha a HDInsight-fürt Enterprise Security Package engedélyezve van. | Nem       |

## <a name="next-steps"></a>További lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Batch-végrehajtási tevékenység Machine Learning](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
