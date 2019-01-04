---
title: Adatok átalakítása a Pig-tevékenység használatával az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Pig-tevékenység az Azure data factory, a Pig-parancsfájlok futtatása egy a-igény és a saját HDInsight-fürtön.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 48cbd33d4cbaff5d362731d67bbd3d4041e26f89
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025644"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Adatok átalakítása a Pig-tevékenység használatával az Azure Data Factoryban
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása a Pig-tevékenység használatával a Data Factory](../transform-data-using-hadoop-pig.md).


A HDInsight Pig-tevékenység az adat-előállító [folyamat](data-factory-create-pipelines.md) a Pig-lekérdezéseket hajt végre [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](data-factory-introduction.md) és végezze el az oktatóanyag: [Az első adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md) Ez a cikk elolvasása előtt. 

## <a name="syntax"></a>Szintaxis

```JSON
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```
## <a name="syntax-details"></a>Szintaxis részletei
| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |A tevékenység neve |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg |Nem |
| type |HDinsightPig |Igen |
| bemenetek |Egy vagy több, a Pig-tevékenység által felhasznált bemeneti |Nem |
| kimenetek |Egy vagy több, a Pig-tevékenység által előállított kimeneti |Igen |
| linkedServiceName |A HDInsight-fürt a Data Factory társított szolgáltatásként regisztrált mutató hivatkozás |Igen |
| parancsfájl |Adja meg a Pig-parancsprogram beágyazott |Nem |
| parancsprogram elérési útja |A Pig-parancsprogram Store egy Azure blob Storage, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév formátuma a kis-és nagybetűket. |Nem |
| határozza meg |Adja meg a paramétereket a kulcs/érték párokként való belül a Pig-parancsprogram |Nem |

## <a name="example"></a>Példa
Vegyünk egy példát, játék-naplók analytics, ahol szeretné azonosítani a játékosok indítja el a vállalat a játékok által töltött időt.

A következő játék mintanapló egy olyan vesszővel (,) elválasztva fájl. A következő mezőket – ProfileID, SessionStart, időtartamának, SrcIPAddress és GameType tartalmazza.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

A **Pig-parancsprogram-alapú** feldolgozni ezeket az adatokat:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

A Pig-parancsprogram végrehajtása a Data Factory-folyamatok, tegye a következőket:

1. Létrehoz egy társított szolgáltatást, regisztrálnia [a saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy konfigurálása [igény szerinti HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Adjuk a társított szolgáltatás **HDInsightLinkedService**.
2. Hozzon létre egy [társított szolgáltatás](data-factory-azure-blob-connector.md) a adatok tárolásához Azure Blob Storage-kapcsolat konfigurálása. Adjuk a társított szolgáltatás **StorageLinkedService**.
3. Hozzon létre [adatkészletek](data-factory-create-datasets.md) mutat a bemeneti és kimeneti adatokat. Adjuk a bemeneti adatkészlet **PigSampleIn** és a kimeneti adatkészlet **PigSampleOut**.
4. A Pig lekérdezés másolása a #2. lépésben beállított, az Azure Blob Storage-fájlban. Ha az Azure storage, amelyen az adatok eltérő, amelyen a lekérdezés fájlt, hozzon létre egy külön Azure Storage társított szolgáltatás. Tekintse meg a társított szolgáltatást a tevékenység-konfigurációban. Használata ** scriptPath ** a pig-parancsfájl elérési útját és **scriptLinkedService**. 
   
   > [!NOTE]
   > A Pig-parancsfájl beágyazott a tevékenység meghatározásában használatával is megadhatja a **parancsfájl** tulajdonság. Azonban a Microsoft nem javasolja ezt a módszert, speciális karaktereket kell megadni a parancsfájl el kell, és hibakeresési problémákat okozhat. Az ajánlott eljárás, hogy #4. lépés.
   > 
   > 
5. A HDInsightPig tevékenységgel rendelkező folyamat létrehozása. Ez a tevékenység a bemeneti adatokat feldolgozó, Pig-parancsprogram futtatásával a HDInsight-fürtön.

    ```JSON   
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
        ]
      }
    } 
    ```
6. A folyamat üzembe helyezéséhez. Lásd: [folyamatok létrehozása](data-factory-create-pipelines.md) részleteivel. 
7. Monitorozza a folyamatot a data factory figyelési és felügyeleti nézetek használatával. Lásd: [figyelés és kezelheti a Data Factory-folyamatok](data-factory-monitor-manage-pipelines.md) részleteivel.

## <a name="specifying-parameters-for-a-pig-script"></a>A Pig-parancsprogram paramétereinek megadása
Vegye figyelembe az alábbi példa: játék naplók naponta elemezhető az Azure Blob Storage-ba, és egy mappa particionált alapján dátuma és időpontja. Szeretné paraméterezni a Pig-parancsprogram és a bemeneti mappa helye dinamikusan továbbítása során futásidejű és is előállíthat a kimeneti dátum és idő particionálni.

A paraméteres Pig-parancsprogram használatához tegye a következőket:

* Adja meg a paramétereket a **meghatározása**.

    ```JSON  
    {
        "name": "PigActivitySamplePipeline",
          "properties": {
        "activities": [
            {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                      {
                        "name": "PigSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "PigSampleOut"
                      }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      }
                },
                   "scheduler": {
                      "frequency": "Day",
                      "interval": 1
                }
              }
        ]
      }
    }
    ```  
* A Pig-parancsprogram, tekintse meg a paraméterek használatával "**$parameterName**" a következő példában látható módon:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [MapReduce-tevékenység](data-factory-map-reduce.md)
* [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

