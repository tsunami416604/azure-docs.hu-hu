---
title: Adatok átalakítása Hadoop Streamelési tevékenységben – az Azure használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Hadoop Streamelési tevékenységben az Azure data factory az adatok átalakítása Hadoop Streamelési programok egy a-igény és a saját HDInsight-fürtön való futtatásával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: dd00c0a2998009ce6c39ca19abb25a2548682cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523222"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Adatok átalakítása Hadoop Streamelési tevékenységben használatával az Azure Data Factoryban
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása Hadoop-tartalomközvetítő tevékenységgel a Data Factory használatával](../transform-data-using-hadoop-streaming.md).


A HDInsightStreamingActivity tevékenységeinek használatát egy Azure Data Factory-folyamatot a Hadoop Streamelési feladat indítása. A következő JSON-kódrészletben a HDInsightStreamingActivity a folyamat JSON-fájlban szintaxisát jeleníti meg. 

A HDInsight Streaming-tevékenység az adat-előállító [folyamat](data-factory-create-pipelines.md) Hadoop Streamelési programok lefut a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](data-factory-introduction.md) és végezze el az oktatóanyag: [Az első adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md) Ez a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-példa
A HDInsight-fürt automatikusan kitölti a rendszer például programok (wc.exe és cat.exe) és az adatokat (davinci.txt). Alapértelmezés szerint a tároló a HDInsight-fürt által használt név magának a fürtnek a nevét. Például ha a fürt neve myhdicluster, a társított blob-tároló neve lenne myhdicluster. 

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

1. Állítsa be a **linkedServiceName** a folyamatos átviteli mapreduce-feladatok futtatása a fürt, amely a HDInsight társított szolgáltatás neve.
2. Állítsa a tevékenység típusát **HDInsightStreaming**.
3. Az a **eseményleképező** tulajdonság, adja meg a teljesítményleképező végrehajtható fájl nevét. A példában cat.exe végrehajtható eseményleképező.
4. Az a **nyomáscsökkentő** tulajdonság, adja meg a nyomáscsökkentő végrehajtható fájl nevét. A példában wc.exe végrehajtható nyomáscsökkentő.
5. Az a **bemeneti** type tulajdonság, a teljesítményleképező adja meg a bemeneti fájlt (beleértve a helyet). A példában: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample blob-tárolóban, például/data/Gutenberg az a mappa, és davinci.txt a blobot.
6. Az a **kimeneti** type tulajdonság, a nyomáscsökkentő adja meg a kimeneti fájl (beleértve a helyet). A Hadoop Streamelési feladat kimenetének írt ehhez a tulajdonsághoz megadott helyen.
7. Az a **filePaths** területén adja meg a hozzárendelést és nyomáscsökkentő végrehajtható fájlok elérési útjait. A példában: "adfsample/example/apps/wc.exe", adfsample a blob-tároló, például/alkalmazások pedig a mappa, és wc.exe a végrehajtható fájlt.
8. Az a **fileLinkedService** tulajdonság, adja meg az Azure Storage társított szolgáltatás, amely az Azure storage filePaths szakaszában megadott fájlt tartalmazó jelöli.
9. Az a **argumentumok** tulajdonság, adja meg a folyamatos átviteli feladat argumenty.
10. A **getDebugInfo** tulajdonság nem kötelező eleme. Ha ezt a beállítást a hiba, a naplók letöltődnek csak a hibával kapcsolatban. Ha mindig van állítva, a naplók mindig letöltődnek attól függetlenül, a végrehajtási állapotot.

> [!NOTE]
> Ahogy a példában látható, a Hadoop Streamelési tevékenységben megadott egy kimeneti adatkészletet a **kimenete** tulajdonság. Ez az adatkészlet csak egy helyőrző adatkészletet, amely a folyamat ütemezését meghajtó szükséges. Nem kell minden olyan bemeneti adatkészlet a tevékenység esetében adja meg a **bemenetek** tulajdonság.  
> 
> 

## <a name="example"></a>Példa
Ebben az útmutatóban a folyamat az Azure HDInsight-fürtön futtatja a Word-Count streamelési Map/Reduce programot. 

### <a name="linked-services"></a>Társított szolgáltatások
#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Először hozzon létre egy társított szolgáltatást, az Azure Storage a az Azure HDInsight-fürt az Azure data factory által használt. Ha másolja/beilleszti az alábbi kódot, ne felejtse el lecseréli a nevét és kulcsát az Azure Storage-fiók nevét és kulcsát. 

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
Ezután hozzon létre egy társított szolgáltatást, az Azure HDInsight-fürt az Azure data Factory szolgáltatásban. Ha, másolja és illessze be a következő kódot, a HDInsight-fürt nevére cserélje le a HDInsight-fürt nevére, és felhasználói név és jelszó értékeinek módosítása. 

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
Ebben a példában a folyamat nem használ bemenetet. A HDInsight Streaming-tevékenység egy kimeneti adatkészletet adja meg. Ez az adatkészlet csak egy helyőrző adatkészletet, amely a folyamat ütemezését meghajtó szükséges. 

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
Ebben a példában szereplő folyamat egyetlen tevékenységgel rendelkezik, amely típusa: **HDInsightStreaming**. 

A HDInsight-fürt automatikusan kitölti a rendszer például programok (wc.exe és cat.exe) és az adatokat (davinci.txt). Alapértelmezés szerint a tároló a HDInsight-fürt által használt név magának a fürtnek a nevét. Például ha a fürt neve myhdicluster, a társított blob-tároló neve lenne myhdicluster.  

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
* [Pig-tevékenység](data-factory-pig-activity.md)
* [MapReduce-tevékenység](data-factory-map-reduce.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

