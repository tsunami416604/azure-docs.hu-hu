---
title: Az Azure Data Factory MapReduce Program meghívása
description: Megtudhatja, hogyan MapReduce programok futtatásával Azure HDInsight-fürtök az egy az Azure data factory feldolgozni az adatokat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6bddb3d5beca57852ef62361b278ec1fa8961f45
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621258"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Az adat-előállító MapReduce programok meghívása
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md) 
> * [A Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce művelethez](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [A Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [MapReduce művelethez adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-hadoop-map-reduce.md).


A HDInsight MapReduce művelethez egy adat-előállítóban [csővezeték](data-factory-create-pipelines.md) MapReduce programok végrehajtása a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](data-factory-introduction.md) hajtsa végre az oktatóanyag: [felépítheti első folyamatát adatok](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt.  

## <a name="introduction"></a>Bevezetés
Egy folyamatot egy az Azure data factory az adatokat a csatolt tárolószolgáltatások csatolt számítási szolgáltatások használatával dolgozza fel. Ha minden tevékenység egyedi feldolgozása műveletet hajt végre tevékenységek sorrendje tartalmaz. Ez a cikk ismerteti a használata a HDInsight MapReduce művelethez.

Lásd: [Pig](data-factory-pig-activity.md) és [Hive](data-factory-hive-activity.md) Pig/Hive futtatásával kapcsolatos részletekért parancsfájlok a Windows/Linux-alapú HDInsight fürt egy láncból tevékenységek HDInsight a Pig és a Hive használatával. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>A HDInsight MapReduce művelethez JSON
A HDInsight tevékenység JSON-definícióban: 

1. Állítsa be a **típus** , a **tevékenység** való **HDInsight**.
2. Adja meg az osztály nevét **osztálynév** tulajdonság.
3. Adja meg az elérési útját a JAR-fájlra, beleértve a fájlnevet **jarfilepath tulajdonságot** tulajdonság.
4. Adja meg a társított szolgáltatás, az Azure Blob Storage, amely tartalmazza a JAR-fájlra hivatkozó **jarLinkedService** tulajdonság.   
5. Adja meg a MapReduce programhoz tartozó argumentumai a **argumentumok** szakasz. Futásidőben, néhány további argumentumok látja (például: mapreduce.job.tags) a a MapReduce keretrendszer. A MapReduce argumentumok argumentumok megkülönböztetéséhez érdemes beállítás és value elemeit is argumentumként a következő példában látható módon (- s,--bemeneti,--kimeneti stb., amelyet közvetlenül az értékeik lehetőség áll).

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
Bármely MapReduce jar-fájlra a HDInsight-fürtök futtatásához használhatja a HDInsight MapReduce művelethez. A következő minta JSON-definícióban adatcsatorna a HDInsight konfigurálta egy olyan Mahout JAR-fájlt.

## <a name="sample-on-github"></a>Mintát a Githubon
Letöltheti a minta használata a HDInsight MapReduce tevékenységet: [Data Factory minták a Githubon](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>A Word-Count futtatása
Ebben a példában az adatcsatorna a térkép vagy csökkentse a szószámot programot futtatja az Azure HDInsight-fürt.   

### <a name="linked-services"></a>Összekapcsolt szolgáltatások
Először hozzon létre az Azure Storage Azure data factoryval való az Azure HDInsight-fürt által használt csatolásához összekapcsolt szolgáltatás. Ha Ön másolja és illessze be az alábbi kód, ne felejtse el lecserélni **fióknév** és **fiókkulcs** nevű és kulcsát az Azure Storage. 

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
Ezután hozzon létre egy Azure HDInsight-fürtjéhez csatolása az Azure data factory társított szolgáltatás. Ha Ön másolja és illessze be az alábbi kód, cserélje le **HDInsight-fürt neve** nevű, a HDInsight-fürtöt, és a felhasználói nevet és jelszót értékek módosítása.   

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
Ebben a példában az adatcsatorna nem veszi a bemeneti adatok. Egy kimeneti adatkészlet állít be a HDInsight MapReduce művelethez. Ez az adatkészlet csak egy üres adatkészlet szükséges ahhoz, hogy az adatcsatorna ütemezés meghajtó.  

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
Ebben a példában az adatcsatorna csak egy tevékenység, amelynek típusa van: HDInsightMapReduce. A JSON-ban fontos tulajdonságai vannak: 

| Tulajdonság | Megjegyzések |
|:--- |:--- |
| type |A típus értékre kell állítani **HDInsightMapReduce**. |
| Osztálynév |Az osztály neve: **wordcount** |
| jarFilePath |Az osztály tartalmazó jar-fájlt elérési útja. Ha Ön másolja és illessze be az alábbi kód, ne feledje módosítani a fürt nevét. |
| jarLinkedService |Az Azure tárolás társított szolgáltatása, amely tartalmazza a jar-fájlra. A társított szolgáltatás a tárolót a HDInsight-fürthöz társított hivatkozik. |
| Argumentumok |A wordcount program két argumentumot, bemeneti és egy kimeneti vesz igénybe. A bemeneti fájl a davinci.txt fájlt. |
| frequency/interval |Ezek a tulajdonságok értékeit felel meg a kimeneti adatkészletet. |
| linkedServiceName |a csatolt HDInsight-szolgáltatás volt korábban létrehozott hivatkozik. |

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

## <a name="run-spark-programs"></a>Spark programok futtatása
A MapReduce tevékenység használatával Spark-programokat futtathat a HDInsight Spark-fürtökön. A részletekért lásd: [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Spark-programok meghívása az Azure Data Factory-ból).  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [A Pig-tevékenység](data-factory-pig-activity.md)
* [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

