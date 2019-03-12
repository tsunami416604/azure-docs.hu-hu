---
title: Adatok átalakítása Hadoop MapReduce-tevékenység használatával az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan fel adatokat a Hadoop MapReduce programok futtatása az Azure HDInsight-fürtön az Azure data factoryt.
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
ms.openlocfilehash: ccc194dd4120762a30da3ad28cdabed6faf53ba2
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576395"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Adatok átalakítása Hadoop MapReduce-tevékenység használatával az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-map-reduce.md)
> * [Aktuális verzió](transform-data-using-hadoop-map-reduce.md)

A HDInsight MapReduce-tevékenység az adat-előállító [folyamat](concepts-pipelines-activities.md) hívja meg a MapReduce-programot a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](introduction.md) és végezze el az oktatóanyag: [Oktatóanyag: adatok átalakítása](tutorial-transform-data-spark-powershell.md) Ez a cikk elolvasása előtt.

Lásd: [Pig](transform-data-using-hadoop-pig.md) és [Hive](transform-data-using-hadoop-hive.md) Pig/Hive futtatásával kapcsolatos részleteket egy HDInsight-szkriptek a fürt egy folyamatból HDInsight Pig- és Hive-tevékenység használatával.

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
| név              | A tevékenység neve                     | Igen      |
| leírás       | Mire használható a tevékenységet leíró szöveg | Nem       |
| type              | A Mapreduce a tevékenység típusát HDinsightMapReduce | Igen      |
| linkedServiceName | Referencia a HDInsight-fürthöz társított szolgáltatásként a Data Factory regisztrálva. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| Osztálynév         | A futtatandó osztály neve         | Igen      |
| jarLinkedService  | Egy Azure Storage társított szolgáltatás a Jar-fájlok tárolására szolgáló hivatkozás. Ha nem adja meg ezt a társított szolgáltatást, az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott használja. | Nem       |
| jarFilePath       | Adja meg a jarLinkedService által hivatkozott Azure Storage-ban tárolt Jar-fájlok elérési útját. A fájlnév formátuma a kis-és nagybetűket. | Igen      |
| jarlibs           | A karakterlánc tömbje a Jar-könyvtár jarLinkedService meghatározott Azure Storage tárolja a feladat által hivatkozott fájlok elérési útját. A fájlnév formátuma a kis-és nagybetűket. | Nem       |
| getDebugInfo      | Itt adhatja meg, amikor a naplófájlok másolja az Azure Storage HDInsight-fürt által használt (vagy) jarLinkedService által megadott. Megengedett értékek: Nincs, mindig, vagy sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| argumentumok         | Argumenty Pro Hadoop-feladatok egy tömbjét adja meg. Az argumentumok adhatók be a parancssori argumentumok minden egyes tevékenységhez. | Nem       |
| határozza meg           | Kulcs/érték párokként paraméterek megadása a Hive-parancsfájl belül hivatkozik. | Nem       |



## <a name="example"></a>Példa
A HDInsight MapReduce-tevékenység segítségével bármely MapReduce jar-fájlt egy HDInsight-fürtön futtathat. Az alábbi példa JSON-definíciójában folyamat a HDInsight-tevékenység futtatni egy Mahout JAR van konfigurálva.

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
A MapReduce programhoz tartozó argumentumokat is megadhat a **argumentumok** szakaszban. Futásidőben, néhány további argumentumok láthatja (például: mapreduce.job.tags), a MapReduce keretrendszer. Az argumentumok a MapReduce argumentumokkal megkülönböztetéséhez, fontolja meg a beállítás és az értéket argumentumként a következő példában látható módon (- s használata esetén – azonnal követ értékekre bemeneti, a--output stb., opció).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon:

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
