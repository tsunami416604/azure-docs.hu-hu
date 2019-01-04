---
title: Az Azure Data Factory a MapReduce-programot meghívása
description: Útmutató MapReduce-programok egy Azure HDInsight-fürtön való futtatásával egy Azure-beli adat-előállítót az adatok feldolgozása.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 309ddcf68d03f34ca3309d76d15cc3928037c667
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017446"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>A Data Factory MapReduce-programok meghívása
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md) 
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce-tevékenység](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása a MapReduce-tevékenység használatával a Data Factory](../transform-data-using-hadoop-map-reduce.md).


A HDInsight MapReduce-tevékenység az adat-előállító [folyamat](data-factory-create-pipelines.md) MapReduce-programok lefut a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](data-factory-introduction.md) és végezze el az oktatóanyag: [Az első adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md) Ez a cikk elolvasása előtt.  

## <a name="introduction"></a>Bevezetés
Az Azure data factory-folyamatot az adatok az társított storage-szolgáltatásokban csatolt számítási szolgáltatások használatával dolgozza fel. A tevékenységek, ahol minden egyes tevékenységhez meghatározott feldolgozási műveletet hajt végre egy feladatütemezési tartalmazza. Ez a cikk ismerteti, hogy a HDInsight MapReduce-tevékenység használatával.

Lásd: [Pig](data-factory-pig-activity.md) és [Hive](data-factory-hive-activity.md) Pig/Hive futtatásával kapcsolatos részleteket a Windows/Linux-alapú HDInsight-szkriptek a fürt egy folyamatból HDInsight Pig- és Hive-tevékenység használatával. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység a JSON
A HDInsight-tevékenység JSON-definíciójában: 

1. Állítsa be a **típus** , a **tevékenység** való **HDInsight**.
2. Adja meg az osztály nevét **className** tulajdonság.
3. Adja meg az elérési útját a JAR-fájlt, beleértve a fájlnevet **jarfilepath tulajdonságot** tulajdonság.
4. Adja meg a társított szolgáltatás, amely az Azure Blob Storage, amely tartalmazza a JAR-fájlra hivatkozik **jarLinkedService** tulajdonság.   
5. A MapReduce-programot az argumentumokat megadni a **argumentumok** szakaszban. Futásidőben, néhány további argumentumok láthatja (például: mapreduce.job.tags), a MapReduce keretrendszer. Az argumentumok a MapReduce argumentumokkal megkülönböztetéséhez, fontolja meg a beállítás és az értéket argumentumként a következő példában látható módon (- s használata esetén – azonnal követ értékekre bemeneti, a--output stb., opció).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
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
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
A HDInsight MapReduce-tevékenység segítségével bármely MapReduce jar-fájlt egy HDInsight-fürtön futtathat. Az alábbi példa JSON-definíciójában folyamat a HDInsight-tevékenység futtatni egy Mahout JAR van konfigurálva.

## <a name="sample-on-github"></a>Példa a Githubon
Egy mintát, a HDInsight MapReduce-tevékenység használatával töltheti le: [Data Factory-mintákat a Githubon](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>A Word-Count futtatása
Ebben a példában szereplő folyamat a Word-Count Map/Reduce programot futtatja, az Azure HDInsight-fürtön.   

### <a name="linked-services"></a>Társított szolgáltatások
Először hozzon létre egy társított szolgáltatást, az Azure Storage a az Azure HDInsight-fürt az Azure data factory által használt. Ha másolja/beilleszti az alábbi kódot, ne felejtse el lecserélni **fióknév** és **fiókkulcs** a nevét és kulcsát az Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Az Azure HDInsight társított szolgáltatás
Ezután hozzon létre egy társított szolgáltatást, az Azure HDInsight-fürt az Azure data Factory szolgáltatásban. Ha másolja/beilleszti az alábbi kódot, cserélje le a **HDInsight-fürt neve** a HDInsight-fürt és a felhasználói nevet és jelszót értékek módosítása nevére.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Adathalmazok
#### <a name="output-dataset"></a>Kimeneti adatkészlet
Ebben a példában a folyamat nem használ bemenetet. A HDInsight MapReduce-tevékenység egy kimeneti adatkészletet adja meg. Ez az adatkészlet csak egy helyőrző adatkészletet, amely a folyamat ütemezését meghajtó szükséges.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
Ebben a példában szereplő folyamat egyetlen tevékenységgel rendelkezik, amely típusa: HDInsightMapReduce. A fontos a JSON-tulajdonságok a következők: 

| Tulajdonság | Megjegyzések |
|:--- |:--- |
| type |A típus értékre kell állítani **HDInsightMapReduce**. |
| Osztálynév |Az osztály neve van: **wordcount** |
| jarFilePath |Az osztály tartalmazó jar-fájlt elérési útja. Ha, másolja és illessze be a következő kódot, ne felejtse el módosítani a fürt nevét. |
| jarLinkedService |Az Azure Storage társított szolgáltatás, amely tartalmazza a jar-fájlt. Ezt a társított szolgáltatást a tárolót a HDInsight-fürthöz társított hivatkozik. |
| argumentumok |A wordcount program két argumentumot, -bemenet és kimenet vesz igénybe. A bemeneti fájl nem a davinci.txt fájlt. |
| frequency/interval |Ezek a tulajdonságok értékeit felel meg a kimeneti adatkészlet. |
| linkedServiceName |a HDInsight társított szolgáltatást korábban hozta hivatkozik. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark-programok futtatása
A MapReduce tevékenység használatával Spark-programokat futtathat a HDInsight Spark-fürtökön. A részletekért lásd: [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Spark-programok meghívása az Azure Data Factory-ból).  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [Pig-tevékenység](data-factory-pig-activity.md)
* [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

