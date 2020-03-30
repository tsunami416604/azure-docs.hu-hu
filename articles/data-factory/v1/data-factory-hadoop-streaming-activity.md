---
title: Adatok átalakítása a Hadoop-streamelési tevékenység használatával – Azure
description: Megtudhatja, hogy miként használhatja a Hadoop streamelési tevékenységet egy Azure-adat-előállítóban az adatok átalakításához a Hadoop streaming programok igény szerinti/saját HDInsight-fürtön való futtatásával.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7f07365da699a40f5b51917104a68a62affa3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703369"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Adatok átalakítása a Hadoop streamelési tevékenységével az Azure Data Factoryban
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatok átalakítása a Hadoop adatkezelési tevékenység használatával című témakört](../transform-data-using-hadoop-streaming.md)a Data Factory ban.


Használhatja a HDInsightStreamingActivity tevékenység meghívása egy Hadoop streamelési feladat egy Azure Data Factory-folyamat. Az alábbi JSON-kódrészlet a HDInsightStreamingActivity folyamatJSON-fájlban való használatának szintaxisát mutatja. 

A DATA [Factory-folyamat](data-factory-create-pipelines.md) HDInsight-streamelési tevékenysége saját vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürtön hajtja végre [a](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) Hadoop-streamelési programokat. Ez a cikk az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha most jön az Azure Data Factory, olvassa el [az Azure Data Factory bevezetés](data-factory-introduction.md) című, és nem az oktatóanyag: [Készítse el az első adatfolyamat](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-minta
A HDInsight-fürt automatikusan kitöltődik a példaprogramok (wc.exe és cat.exe) és az adatok (davinci.txt). Alapértelmezés szerint a HDInsight-fürt által használt tároló neve maga a fürt neve. Ha például a fürt neve myhdicluster, a társított blobtároló neve myhdicluster lesz. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Vegye figyelembe a következő szempontokat:

1. Állítsa be a **linkedServiceName** a neve a csatolt szolgáltatás, amely rámutat arra a HDInsight-fürtre, amelyen a streamelési mapreduce feladat fut.
2. Állítsa be a tevékenység típusát **a HDInsightStreaming**beállításra.
3. A **leképező** tulajdonsághoz adja meg a leképező végrehajtható fájl nevét. A példában a cat.exe a leképező végrehajtható.
4. A **szűkítő** tulajdonsághoz adja meg a szűkítő végrehajtható fájljának nevét. A példában a wc.exe a szűkítő végrehajtható fájlja.
5. A **bemeneti** típus tulajdonsághoz adja meg a leképező bemeneti fájlját (a helyet is beleértve). A példában: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample a blob tároló, példa/data/Gutenberg a mappa, és davinci.txt a blob.
6. A **kimeneti** típus tulajdonsághoz adja meg a szűkítő kimeneti fájlját (beleértve a helyet is). A Hadoop-streamelési feladat kimenete a tulajdonsághoz megadott helyre íródik.
7. A **filePaths** szakaszban adja meg a leképező és a szűkítő végrehajtható fájlelérési útjait. A példában: "adfsample/example/apps/wc.exe", adfsample a blob tároló, példa/alkalmazások a mappa, és wc.exe a végrehajtható.
8. A **fileLinkedService** tulajdonság, adja meg az Azure Storage csatolt szolgáltatás, amely az Azure storage, amely tartalmazza a filePaths szakaszban megadott fájlokat.
9. Az **arguments** tulajdonsághoz adja meg a streamelési feladat argumentumait.
10. A **getDebugInfo** tulajdonság nem kötelező elem. Ha hiba, a naplók csak hiba esetén töltődnek le. Ha mindig van beállítva, a naplók mindig letöltődnek a végrehajtási állapottól függetlenül.

> [!NOTE]
> Amint az a példában látható, adja meg a kimeneti adatkészletet a Hadoop streamelési tevékenység a **kimeneti** tulajdonság. Ez az adatkészlet csak egy dumi adatkészlet, amely a folyamat ütemezésének meghajtója. Nem kell megadnia a bemeneti adatkészletet a tevékenység hez a **bemeneti** tulajdonsághoz.  
> 
> 

## <a name="example"></a>Példa
A forgatókönyvben szereplő folyamat futtatja a Word-számláló streamelési/kicsinyítési programot az Azure HDInsight-fürtön. 

### <a name="linked-services"></a>Társított szolgáltatások
#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Először hozzon létre egy összekapcsolt szolgáltatást az Azure HDInsight-fürt által használt Azure Storage és az Azure-adatgyár összekapcsolásához. Ha másolja/illessze be a következő kódot, ne felejtse el lecserélni a fiók nevét és a fiókkulcsot az Azure Storage nevére és kulcsára. 

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
Ezután hozzon létre egy összekapcsolt szolgáltatást az Azure HDInsight-fürt és az Azure-adatgyár összekapcsolásához. Ha a következő kódot másolja/illeszti be, cserélje le a HDInsight-fürt nevét a HDInsight-fürt nevére, és módosítsa a felhasználónév és a jelszó értékeit. 

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
Ebben a példában a folyamat nem vesz fel minden bemenetet. Megkell adnia egy kimeneti adatkészletet a HDInsight streamelési tevékenységhez. Ez az adatkészlet csak egy dumi adatkészlet, amely a folyamat ütemezésének meghajtója. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
Ebben a példában a folyamat csak egy tevékenység, amely a típus: **HDInsightStreaming**. 

A HDInsight-fürt automatikusan kitöltődik a példaprogramok (wc.exe és cat.exe) és az adatok (davinci.txt). Alapértelmezés szerint a HDInsight-fürt által használt tároló neve maga a fürt neve. Ha például a fürt neve myhdicluster, a társított blobtároló neve myhdicluster lesz.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [Sertés tevékenység](data-factory-pig-activity.md)
* [MapReduce tevékenység](data-factory-map-reduce.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

