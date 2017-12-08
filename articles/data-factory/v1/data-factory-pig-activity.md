---
title: "A Pig-tevékenység használata az Azure Data Factory adatok átalakítása |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja a Pig-tevékenység egy Azure data factoryban a-igény szerint vagy a saját HDInsight-fürtök Pig-parancsfájlok futtatásra."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 7612eda8e40cb0ff2b205c2dfe11c2bba1b05b6a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>A Pig-tevékenység használata az Azure Data Factory adatok átalakítása
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
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Pig tevékenység adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-hadoop-pig.md).


A HDInsight a Pig-tevékenység egy adat-előállítóban [csővezeték](data-factory-create-pipelines.md) Pig lekérdezések végrehajtása a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](data-factory-introduction.md) hajtsa végre az oktatóanyag: [felépítheti első folyamatát adatok](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt. 

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
| név |A tevékenység neve. |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg |Nem |
| type |HDinsightPig |Igen |
| Bemenetek |Egy vagy több, a Pig tevékenység által felhasznált bemeneti |Nem |
| kimenetek |Egy vagy több, a Pig tevékenység által létrehozott kimenet |Igen |
| linkedServiceName |A HDInsight-fürthöz, a Data Factory kapcsolt szolgáltatásként regisztrált mutató hivatkozás |Igen |
| Parancsfájl |Adja meg a Pig-parancsprogram beágyazott |Nem |
| parancsfájl elérési útja |A Pig-parancsprogram egy Azure blob Storage tárolóban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév pedig kis-és nagybetűket. |Nem |
| határozza meg |Kulcs/érték párok paraméterek meghatározni a Pig-parancsprogram belül hivatkozik |Nem |

## <a name="example"></a>Példa
Mérlegeljük, elemzés, ahol szeretné azonosítani a vállalat által elindított játékok játékosok szerint fordított idő játék naplók példát.

A következő játék mintanaplót egy olyan vesszővel (,) elválasztva fájl. A következő mezők – ProfileID, SessionStart, időtartam, SrcIPAddress és GameType tartalmaz.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

A **parancsfájl sertésfelmérés** feldolgozni az adatokat:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

A Pig-parancsprogram végrehajtása a Data Factory-folyamathoz, tegye a következőket:

1. Hozzon létre egy kapcsolódó szolgáltatás regisztrálása [saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy konfigurálása [igény szerinti HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Most hívható meg a társított szolgáltatás **HDInsightLinkedService**.
2. Hozzon létre egy [társított szolgáltatás](data-factory-azure-blob-connector.md) üzemeltető Azure Blob Storage a kapcsolat beállításához. Most hívható meg a társított szolgáltatás **StorageLinkedService**.
3. Hozzon létre [adatkészletek](data-factory-create-datasets.md) mutat a bemeneti és kimeneti adatokat. Most hívható meg a bemeneti adatkészlet **PigSampleIn** és a kimeneti adatkészlet **PigSampleOut**.
4. A Pig lekérdezés másolja az Azure Blob Storage #2. lépésben beállított fájlba. Ha az Azure storage az adatokat tároló eltérő, amelyen a lekérdezés fájlt, hozzon létre egy külön Azure tárolás társított szolgáltatása. A tevékenység konfigurációban kapcsolódó szolgáltatásra hivatkozhat. Használjon ** scriptPath ** adhatja meg a pig-parancsprogram fájl elérési útját és **scriptLinkedService**. 
   
   > [!NOTE]
   > A Pig parancsfájl beágyazottan történjen-e a tevékenység definíciójának használatával is megadhatja a **parancsfájl** tulajdonság. Azonban a Microsoft nem javasolja ezt a módszert használja, a parancsfájl igényeinek, meg kell jelölni a speciális karaktereket, és hibakeresési hibákat okozhat. A bevált gyakorlat követéséhez #4. lépés.
   > 
   > 
5. A folyamat létrehozása a HDInsightPig tevékenységet. Ez a tevékenység HDInsight-fürt Pig-parancsprogram futtatásával dolgozza fel a bemeneti adatok.

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
6. Az adatcsatornát. Lásd: [folyamatok létrehozása](data-factory-create-pipelines.md) cikkben alább. 
7. A data factory kiszolgálófigyelési és -kezelési nézetek segítségével folyamat figyelésére. Lásd: [figyelése és kezelése az adat-előállító adatcsatornák](data-factory-monitor-manage-pipelines.md) cikkben alább.

## <a name="specifying-parameters-for-a-pig-script"></a>A Pig-parancsprogram paramétereinek megadása
Tekintse meg a következő példát: játék naplók naponta okozhatnak az Azure Blob Storage és egy mappa a particionált alapján dátuma és időpontja. Szeretné parametrizálja a Pig-parancsprogram és futásidőben dinamikusan adja át a bemeneti mappa helyét, és a kimeneti dátum és idő tárolóhelyeinek is készít.

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
* A Pig-parancsprogram, tekintse meg a paramétereket, a "**$parameterName**" a következő példában látható módon:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [MapReduce művelethez](data-factory-map-reduce.md)
* [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

