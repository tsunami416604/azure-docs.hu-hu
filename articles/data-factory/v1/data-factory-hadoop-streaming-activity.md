---
title: Adatok Hadoop Streamelési tevékenységben - Azure használatával |} Microsoft Docs
description: Ismerje meg, hogyan használhatja a Hadoop Streamelési tevékenységben egy Azure data factoryban adatok átalakítására használja a-igény szerint vagy a saját HDInsight-fürtök Hadoop Streamelési programok futtatásával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e405f99388f699b3f1e88b9e84f52f9a4380228c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621972"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory használatával Hadoop Streamelési tevékenységben adatok átalakítása
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
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Hadoop-Stream tevékenység adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-hadoop-streaming.md).


Használhatja a HDInsightStreamingActivity tevékenység meghívni egy Azure Data Factory-folyamat a Hadoop adatfolyam-feladatot. A következő JSON-részlet a HDInsightStreamingActivity használatát mutatja be egy adatcsatorna JSON-fájl szintaxisát jeleníti meg. 

A HDInsight Streamelési tevékenységben egy adat-előállítóban [csővezeték](data-factory-create-pipelines.md) Hadoop Streamelési programok végrehajtása a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](data-factory-introduction.md) hajtsa végre az oktatóanyag: [felépítheti első folyamatát adatok](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-mintát
A HDInsight-fürt automatikusan kitölti a rendszer például programok (wc.exe és cat.exe) és az adatokat (davinci.txt). Alapértelmezés szerint a tárolót a HDInsight-fürt által használt neve egyrészt a fürt nevét. Például ha a fürt neve myhdicluster, a társított blob-tároló neve lenne myhdicluster. 

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

1. Állítsa be a **linkedServiceName** nevére, a társított szolgáltatás mutat, a HDInsight fürt a folyamatos átviteli mapreduce-feladatok futtatásához.
2. Állítsa be a tevékenység típusát **HDInsightStreaming**.
3. Az a **leképező** tulajdonság, adja meg a leképező végrehajtható fájl nevét. A példában a cat.exe végrehajtható leképező.
4. Az a **nyomáscsökkentő** tulajdonság, adja meg a végrehajtható nyomáscsökkentő nevét. A példában a wc.exe végrehajtható nyomáscsökkentő.
5. Az a **bemeneti** írja be a tulajdonságot, adja meg a bemeneti fájl (például helye) a leképező. A példában: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample a blob-tároló, például/data/Gutenberg az a mappa, pedig davinci.txt a blob.
6. Az a **kimeneti** írja be a tulajdonságot, adja meg a kimeneti fájl (például helye) a nyomáscsökkentő. Az adatfolyam-Hadoop-feladat eredményének írása ehhez a tulajdonsághoz megadott helyre.
7. Az a **filePaths** területén adja meg a hozzárendelést és nyomáscsökkentő végrehajtható fájlok elérési útját. A példában: "adfsample/example/apps/wc.exe" adfsample a blob-tároló, például/alkalmazások az a mappa, pedig wc.exe a végrehajtható fájl.
8. Az a **fileLinkedService** tulajdonság, adja meg az Azure tárolás társított szolgáltatása, amely az Azure storage filePaths szakaszában megadott fájlt tartalmazó jelöli.
9. Az a **argumentumok** tulajdonság, a folyamatos átviteli feladat argumentumainak megadása.
10. A **getDebugInfo** tulajdonsága egy nem kötelező elemet. Hiba-ra van állítva, a naplók csak az hiba lesznek letöltve. Ha ezt a beállítást mindig, naplók mindig letöltődnek függetlenül a végrehajtási állapotot.

> [!NOTE]
> A példa szerint, állít be egy kimeneti adatkészlet esetében a Hadoop Streamelési tevékenységben a **kimenete** tulajdonság. Ez az adatkészlet csak egy üres adatkészlet szükséges ahhoz, hogy az adatcsatorna ütemezés meghajtó. Nem kell megadnia a bármely bemeneti adatkészlet a tevékenység a **bemenetek** tulajdonság.  
> 
> 

## <a name="example"></a>Példa
A kimenetátirányítási mechanizmusával Ez a forgatókönyv az Azure HDInsight-fürt a szószámot adatfolyam térkép/csökkentse programot futtatja. 

### <a name="linked-services"></a>Társított szolgáltatások
#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Először hozzon létre az Azure Storage Azure data factoryval való az Azure HDInsight-fürt által használt csatolásához összekapcsolt szolgáltatás. Ha Ön másolja és illessze be az alábbi kód, ne feledje cseréje a fióknevet és a fiókkulcsot nevét és az Azure Storage kulcsa. 

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
Ezután hozzon létre egy Azure HDInsight-fürtjéhez csatolása az Azure data factory társított szolgáltatás. Ha, másolja és illessze be az alábbi kód, cserélje le a HDInsight-fürt neve a HDInsight-fürt nevét, és módosítsa a felhasználói nevet és jelszót értékek. 

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
Ebben a példában az adatcsatorna nem veszi a bemeneti adatok. Egy kimeneti adatkészlet állít be a HDInsight Streamelési tevékenységben. Ez az adatkészlet csak egy üres adatkészlet szükséges ahhoz, hogy az adatcsatorna ütemezés meghajtó. 

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
Ebben a példában az adatcsatorna csak egy tevékenység, amelynek típusa van: **HDInsightStreaming**. 

A HDInsight-fürt automatikusan kitölti a rendszer például programok (wc.exe és cat.exe) és az adatokat (davinci.txt). Alapértelmezés szerint a tárolót a HDInsight-fürt által használt neve egyrészt a fürt nevét. Például ha a fürt neve myhdicluster, a társított blob-tároló neve lenne myhdicluster.  

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
* [A Pig-tevékenység](data-factory-pig-activity.md)
* [MapReduce művelethez](data-factory-map-reduce.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

