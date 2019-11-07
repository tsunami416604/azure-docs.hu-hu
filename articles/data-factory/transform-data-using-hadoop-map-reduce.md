---
title: Az adatátalakítás a Hadoop MapReduce-tevékenységgel Azure Data Factory
description: Ismerje meg, hogyan dolgozhat fel az adatok egy Azure HDInsight-fürtön futó Hadoop MapReduce-programok Azure-beli adatgyárból való futtatásával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 49e00d9a47f92fb30a29e7051cba35f54bde3700
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683850"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Az adatátalakítás a Hadoop MapReduce-tevékenységgel Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-map-reduce.md)
> * [Aktuális verzió](transform-data-using-hadoop-map-reduce.md)

A Data Factory- [folyamat](concepts-pipelines-activities.md) HDInsight MapReduce-tevékenysége meghívja a MapReduce programot a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) fürtön. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről.

Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az oktatóanyagot: [oktatóanyag: az adatátalakítás](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt.

A Pig/kaptár-parancsfájlok HDInsight-fürtön való futtatásával kapcsolatos részletekért lásd a [Pig](transform-data-using-hadoop-pig.md) és a [kaptár](transform-data-using-hadoop-hive.md) című témakört a HDInsight Pig és a kaptár tevékenységek használatával.

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| név              | A tevékenység neve                     | Igen      |
| leírás       | A tevékenység által használt szöveg leírása | Nem       |
| type              | MapReduce tevékenység esetén a tevékenységtípus a következő: HDinsightMapReduce. | Igen      |
| linkedServiceName | Hivatkozás a Data Factory társított szolgáltatásként regisztrált HDInsight-fürtre. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Igen      |
| className         | A végrehajtandó osztály neve         | Igen      |
| jarLinkedService  | Hivatkozás egy, a JAR-fájlok tárolására használt Azure Storage társított szolgáltatásra. Ha nem megadja ezt a társított szolgáltatást, a rendszer a HDInsight társított szolgáltatásban definiált Azure Storage társított szolgáltatást használja. | Nem       |
| jarFilePath       | Adja meg a jarLinkedService által hivatkozott Azure Storage-ban tárolt JAR-fájlok elérési útját. A fájl neve megkülönbözteti a kis-és nagybetűket. | Igen      |
| jarlibs           | A jarLinkedService-ben definiált, az Azure Storage-ban tárolt feladatok által hivatkozott jar-függvénytárak elérési útjának karakterlánc-tömbje. A fájl neve megkülönbözteti a kis-és nagybetűket. | Nem       |
| getDebugInfo      | Megadja, hogy a rendszer mikor másolja a naplófájlokat a jarLinkedService által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |
| argumentumok         | Argumentumok tömbjét adja meg egy Hadoop feladatokhoz. Az argumentumok parancssori argumentumként lesznek átadva az egyes feladatokhoz. | Nem       |
| meghatározza           | Adja meg a paramétereket kulcs/érték párokként a kaptár-parancsfájlon belüli hivatkozáshoz. | Nem       |



## <a name="example"></a>Példa
A HDInsight MapReduce tevékenység használatával bármilyen MapReduce jar-fájlt futtathat egy HDInsight-fürtön. A folyamat alábbi JSON-definíciójában a HDInsight tevékenység Mahout JAR-fájl futtatására van konfigurálva.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
A MapReduce program argumentumai a **argumentumok** szakaszban adhatók meg. Futásidőben a MapReduce-keretrendszer néhány további argumentuma (például: MapReduce. job. Tags) jelenik meg. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, érdemes lehet mindkét beállítást és értéket argumentumként használni az alábbi példában látható módon (-s,--bemenet,--output stb.).

## <a name="next-steps"></a>További lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást:

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Batch-végrehajtási tevékenység Machine Learning](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
