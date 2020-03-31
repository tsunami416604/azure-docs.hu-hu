---
title: Adatok átalakítása a Hadoop MapReduce tevékenységgel
description: Ismerje meg, hogyan dolgozhat fel adatokat a Hadoop MapReduce programok azure-beli HDInsight-fürtön egy Azure-adatfeldolgozóból történő futtatásával.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912865"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Adatok átalakítása a Hadoop MapReduce tevékenység használatával az Azure Data Factoryban

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-map-reduce.md)
> * [Aktuális verzió](transform-data-using-hadoop-map-reduce.md)

A DATA [Factory-folyamat](concepts-pipelines-activities.md) HDInsight-hozzárendelési tevékenysége a MapReduce programot [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy igény szerinti [HDInsight-fürtön](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) hívja meg. Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

Ha most jön az Azure Data Factory, olvassa el a Bevezetés az [Azure Data Factory,](introduction.md) és ezt az oktatóanyag: [Oktatóanyag: átalakítása adatok](tutorial-transform-data-spark-powershell.md) elolvasása előtt ezt a cikket.

A [Pig](transform-data-using-hadoop-pig.md) és [a Hive](transform-data-using-hadoop-hive.md) című témakörben részletesen ismerteti, hogy fut-e Pig/Hive-parancsfájlok egy HDInsight-fürtön egy folyamatból a HDInsight Pig és a Hive-tevékenységek használatával.

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
| leírás       | A tevékenységet leíró szöveg | Nem       |
| type              | MapReduce tevékenység esetén a tevékenység típusa HDinsightMapReduce | Igen      |
| linkedServiceName | Hivatkozás a Data Factory csatolt szolgáltatásként regisztrált HDInsight-fürtre. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| Osztálynév         | A végrehajtandó osztály neve         | Igen      |
| jarLinkedService  | Hivatkozás egy Azure Storage csatolt szolgáltatás tárolására használt Jar fájlokat. Ha nem adja meg ezt a csatolt szolgáltatást, a HDInsight csatolt szolgáltatásban definiált Azure Storage Csatolt szolgáltatás lesz használva. | Nem       |
| jarFilePath       | Adja meg a jarLinkedService által hivatkozott Azure Storage-ban tárolt Jar-fájlok elérési útját. A fájlnév nem imitáta a kis- és nagybetűk között. | Igen      |
| jarlibs           | A Jar könyvtárfájljaielérési út karakterlánca, amelyet a jarLinkedService-ben definiált Azure Storage-ban tárolt feladat hivatkozik. A fájlnév nem imitáta a kis- és nagybetűk között. | Nem       |
| getDebugInfo      | Itt adható meg, hogy a naplófájlok at a jarLinkedService által megadott HDInsight-fürt (vagy) által használt Azure Storage-ba másolja-e a rendszer. Engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| Érvek         | Egy Hadoop-feladat argumentumainak tömbjét adja meg. Az argumentumok parancssori argumentumként kerülnek átadásra az egyes tevékenységekhez. | Nem       |
| Meghatározza           | Adja meg a paramétereket kulcs/érték párokként a Hive-parancsfájlon belüli hivatkozáshoz. | Nem       |



## <a name="example"></a>Példa
A HDInsight MapReduce tevékenység segítségével futtathatja a MapReduce jar fájlt egy HDInsight-fürtön. A következő minta JSON-definíciója egy folyamat, a HDInsight-tevékenység van konfigurálva egy Mahout JAR fájl futtatásához.

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
Az argumentumok szakaszban megadhatja a MapReduce program **argumentumait.** Futásidőben néhány további argumentum (például mapreduce.job.tags) a MapReduce keretrendszerből. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, fontolja meg mind a beállítás, mind az érték argumentumként való használatát a következő példában látható módon (-s, --input, --output stb., olyan beállítások, amelyeket közvetlenül az értékeik követnek).

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat:

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
