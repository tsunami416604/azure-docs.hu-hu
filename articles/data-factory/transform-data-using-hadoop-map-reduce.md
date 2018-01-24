---
title: "Hadoop-MapReduce művelethez használata az Azure Data Factory adatok átalakítása |} Microsoft Docs"
description: "Megtudhatja, hogyan futtatásával Hadoop MapReduce programok az Azure HDInsight-fürtök egy az Azure data factory feldolgozni az adatokat."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: c1fbb6864629874ef116cdf81d48df4a9ed5af1f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Hadoop-MapReduce művelethez használata az Azure Data Factory adatok átalakítása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-map-reduce.md)
> * [2. verzió – Előzetes verzió](transform-data-using-hadoop-map-reduce.md)


A HDInsight MapReduce művelethez egy adat-előállítóban [csővezeték](concepts-pipelines-activities.md) MapReduce program meghívja a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](transform-data.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [MapReduce művelethez a V1](v1/data-factory-map-reduce.md).


Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](introduction.md) hajtsa végre az oktatóanyag: [oktatóanyag: adatok](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

Lásd: [Pig](transform-data-using-hadoop-pig.md) és [Hive](transform-data-using-hadoop-hive.md) Pig/Hive futtatásával kapcsolatos részletekért parancsfájlok a hdinsight fürt egy láncból tevékenységek HDInsight a Pig és a Hive használatával. 

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

| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| név              | A tevékenység neve.                     | Igen      |
| leírás       | Mire használható a tevékenységet leíró szöveg | Nem       |
| type              | A MapReduce művelethez a tevékenység típusa nem HDinsightMapReduce | Igen      |
| linkedServiceName | A HDInsight-fürt mutató hivatkozás a Data Factory kapcsolt szolgáltatásként regisztrálva. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| Osztálynév         | Végrehajtandó osztály neve         | Igen      |
| jarLinkedService  | Az Azure tárolás társított szolgáltatásának a Jar-fájlok tárolására használt hivatkozás. A társított szolgáltatás nem adja meg, ha az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott szolgál. | Nem       |
| jarFilePath       | Adja meg a jarLinkedService által hivatkozott Azure Storage-ban tárolt a Jar-fájlok elérési útját. A fájlnév pedig kis-és nagybetűket. | Igen      |
| jarlibs           | A fájlok elérési útját a Jar könyvtár a jarLinkedService meghatározott Azure Storage szolgáltatásban tárolja a feladat által hivatkozott tömbje karakterlánc. A fájlnév pedig kis-és nagybetűket. | Nem       |
| getDebugInfo      | Itt adhatja meg, ha a naplófájlok kerülnek az Azure Storage HDInsight-fürt által használt (vagy) leírt módon jarLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |
| Argumentumok         | Hadoop-feladat argumentumok tömbjét adja meg. Az argumentumok parancssori argumentumként átadott minden egyes tevékenységhez. | Nem       |
| határozza meg           | Adja meg a paraméterek kulcs/érték párok való hivatkozás a Hive-parancsfájl belül. | Nem       |



## <a name="example"></a>Példa
Bármely MapReduce jar-fájlra a HDInsight-fürtök futtatásához használhatja a HDInsight MapReduce művelethez. A következő minta JSON-definícióban adatcsatorna a HDInsight konfigurálta egy olyan Mahout JAR-fájlt.

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
A MapReduce programhoz tartozó egyetlen argumentumot is megadhat a **argumentumok** szakasz. Futásidőben, néhány további argumentumok látja (például: mapreduce.job.tags) a a MapReduce keretrendszer. A MapReduce argumentumok argumentumok megkülönböztetéséhez érdemes beállítás és value elemeit is argumentumként a következő példában látható módon (- s,--bemeneti,--kimeneti stb., amelyet közvetlenül az értékeik lehetőség áll).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
