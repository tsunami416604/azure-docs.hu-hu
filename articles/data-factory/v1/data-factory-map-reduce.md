---
title: MapReduce program meghívása az Azure Data Factory-ból
description: Ismerje meg, hogyan dolgozhat fel adatokat a MapReduce programok azure-beli HDInsight-fürtön egy Azure-adatfeldolgozóból történő futtatásával.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 598a16d25ba375b984a966cba190181edbda3d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703157"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>MapReduce programok meghívása adatgyárból
> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Hive-tevékenység](data-factory-hive-activity.md) 
> * [Sertés tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Szikratevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az adatok átalakítása a Data Factory használatával című [témakört.](../transform-data-using-hadoop-map-reduce.md)


A DATA [Factory-folyamat](data-factory-create-pipelines.md) HDInsight MapReduce tevékenysége a MapReduce programokat [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürtön hajtja végre. Ez a cikk az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha most jön az Azure Data Factory, olvassa el [az Azure Data Factory bevezetés](data-factory-introduction.md) című, és nem az oktatóanyag: [Készítse el az első adatfolyamat](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt.  

## <a name="introduction"></a>Bevezetés
Az Azure-adat-előállító kúra az összekapcsolt tárolási szolgáltatásokban lévő adatokat összekapcsolt számítási szolgáltatások használatával dolgozza fel. Olyan tevékenységek sorozatát tartalmazza, ahol minden tevékenység egy adott feldolgozási műveletet hajt végre. Ez a cikk a HDInsight MapReduce tevékenység használatát ismerteti.

A [Pig](data-factory-pig-activity.md) és [a Hive](data-factory-hive-activity.md) című témakörben részletesen ismerteti, hogy fut-e Pig/Hive parancsfájlwindows/Linux-alapú HDInsight-fürtön egy folyamatról a HDInsight Pig és a Hive-tevékenységek használatával. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON a HDInsight MapReduce tevékenységhez
A HDInsight-tevékenység JSON-definíciójában: 

1. Állítsa a **tevékenység** **típusát** **HDInsight**.
2. Adja meg a **ClassName** tulajdonság osztályának nevét.
3. Adja meg a JAR-fájl elérési útját, beleértve a **jarFilePath** tulajdonság fájlnevét is.
4. Adja meg a csatolt szolgáltatás, amely az Azure Blob Storage, amely tartalmazza a **JARFileService** tulajdonság JAR-fájlt.   
5. Adja meg a MapReduce program **argumentumait az argumentumok** szakaszban. Futásidőben néhány további argumentum (például mapreduce.job.tags) a MapReduce keretrendszerből. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, fontolja meg mind a beállítás, mind az érték argumentumként való használatát a következő példában látható módon (-s, --input, --output stb., olyan beállítások, amelyeket közvetlenül az értékeik követnek).

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
   A HDInsight MapReduce tevékenység segítségével futtathatja a MapReduce jar fájlt egy HDInsight-fürtön. A következő minta JSON-definíciója egy folyamat, a HDInsight-tevékenység van konfigurálva egy Mahout JAR fájl futtatásához.

## <a name="sample-on-github"></a>Minta a GitHubon
A HDInsight MapReduce tevékenység használatával példát tölthet le a következő ből: [Data Factory Samples on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>A Word Count program futtatása
Ebben a példában a folyamat futtatja a Word Count Map/Reduce programot az Azure HDInsight-fürtön.   

### <a name="linked-services"></a>Társított szolgáltatások
Először hozzon létre egy összekapcsolt szolgáltatást az Azure HDInsight-fürt által használt Azure Storage és az Azure-adatgyár összekapcsolásához. Ha másolja/illessze be a következő kódot, ne felejtse el lecserélni a **fiók nevét** és **a fiókkulcsot** az Azure Storage nevére és kulcsára. 

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

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight-kapcsolt szolgáltatás
Ezután hozzon létre egy összekapcsolt szolgáltatást az Azure HDInsight-fürt és az Azure-adatgyár összekapcsolásához. Ha a következő kódot másolja/illeszti be, cserélje le a **HDInsight-fürt nevét** a HDInsight-fürt nevére, és módosítsa a felhasználónév és a jelszó értékeit.   

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
Ebben a példában a folyamat nem vesz fel minden bemenetet. A HDInsight MapReduce tevékenységhez megadhat egy kimeneti adatkészletet. Ez az adatkészlet csak egy dumi adatkészlet, amely a folyamat ütemezésének meghajtója.  

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
Ebben a példában a folyamat csak egy tevékenység, amely nek típusa: HDInsightMapReduce. Néhány fontos tulajdonsága a JSON-nak: 

| Tulajdonság | Megjegyzések |
|:--- |:--- |
| type |A típust **HDInsightMapReduce (HDInsightMapReduce )** beállításra kell állítani. |
| Osztálynév |Az osztály neve: **wordcount** |
| jarFilePath |Az osztályt tartalmazó jar fájl elérési útja. Ha a következő kódot másolja/illeszti be, ne felejtse el módosítani a fürt nevét. |
| jarLinkedService |A jar fájlt tartalmazó Azure Storage-kapcsolt szolgáltatás. Ez a csatolt szolgáltatás a HDInsight-fürthöz társított tárolóra hivatkozik. |
| Érvek |A wordcount program két argumentumot vesz igénybe, egy bemenetet és egy kimenetet. A bemeneti fájl a davinci.txt fájl. |
| frequency/interval |Ezeknek a tulajdonságoknak az értékei megegyeznek a kimeneti adatkészlettel. |
| linkedServiceName |a korábban létrehozott HDInsight-csatolt szolgáltatásra hivatkozik. |

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

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [Sertés tevékenység](data-factory-pig-activity.md)
* [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

