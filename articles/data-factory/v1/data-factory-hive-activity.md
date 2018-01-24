---
title: "Hive tevékenység - Azure adatok átalakítása |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja a Hive tevékenység egy Azure data factoryban a-igény szerint vagy a saját HDInsight-fürtök a Hive-lekérdezések futtatásához."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 566773e9bc787bff4e92d86ec57fb0de3121b079
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory használatával Hive tevékenység adatok átalakítása 
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
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Hive tevékenység adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-hadoop-hive.md).

A HDInsight Hive tevékenység egy adat-előállítóban [csővezeték](data-factory-create-pipelines.md) Hive-lekérdezések végrehajtása a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](data-factory-introduction.md) hajtsa végre az oktatóanyag: [felépítheti első folyamatát adatok](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt. 

## <a name="syntax"></a>Szintaxis

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Szintaxis részletei
| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |A tevékenység neve. |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg |Nem |
| type |HDinsightHive |Igen |
| Bemenetek |A Hive tevékenység által felhasznált bemeneti |Nem |
| kimenetek |A Hive tevékenység által létrehozott kimenet |Igen |
| linkedServiceName |A HDInsight-fürthöz, a Data Factory kapcsolt szolgáltatásként regisztrált mutató hivatkozás |Igen |
| Parancsfájl |Adja meg a Hive parancsfájl beágyazott |Nem |
| parancsfájl elérési útja |A Hive-parancsfájl az Azure blob Storage tárolóban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév pedig kis-és nagybetűket. |Nem |
| határozza meg |Kulcs/érték párok paraméterek meghatározni a Hive-parancsfájl segítségével történő "hiveconf" belül hivatkozik |Nem |

## <a name="example"></a>Példa
Mérlegeljük, elemzés, ahol szeretné azonosítani a vállalat által elindított játékok felhasználók fordított idő játék naplók példát. 

A következő naplóban minta játék napló, amely vessző (`,`) elválasztva, és a következő mezőket – ProfileID, SessionStart, időtartam, SrcIPAddress és GameType tartalmazza.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

A **Hive parancsprogram** feldolgozni az adatokat:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

A Hive parancsprogram végrehajtása a Data Factory-folyamathoz, tegye a következőket kell

1. Hozzon létre egy kapcsolódó szolgáltatás regisztrálása [saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy konfigurálása [igény szerinti HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Most hívható "HDInsightLinkedService" szolgáltatásnak.
2. Hozzon létre egy [társított szolgáltatás](data-factory-azure-blob-connector.md) üzemeltető Azure Blob Storage a kapcsolat beállításához. Most a szolgáltatásnak "StorageLinkedService" hívása
3. Hozzon létre [adatkészletek](data-factory-create-datasets.md) mutat a bemeneti és kimeneti adatokat. Most hívható meg a bemeneti adatkészlet "HiveSampleIn" és "HiveSampleOut" kimeneti adatkészlet
4. Másolás a Hive-lekérdezést az Azure Blob Storage-fájlként #2. lépésben konfigurált. Ha az adatok tárolásához a tároló eltér a lekérdezés fájlt, hozzon létre egy külön Azure Storage társított szolgáltatást, és a tevékenység hivatkozik rá. Használjon **scriptPath** a hive lekérdezés fájljának elérési útja és **scriptLinkedService** adhatja meg, amely tartalmazza a parancsfájl az Azure storage. 
   
   > [!NOTE]
   > A Hive parancsfájl beágyazottan történjen-e a tevékenység definíciójának használatával is megadhatja a **parancsfájl** tulajdonság. A Microsoft nem javasolja ezt a módszert használja, a parancsfájl belül a JSON-dokumentum kell kell megjelölni a speciális karaktereket, és hibakeresési hibákat okozhat. A bevált gyakorlat követéséhez #4. lépés.
   > 
   > 
5. Hozzon létre egy folyamatot a HDInsightHive tevékenység. A tevékenység folyamatok/átalakítások az adatokat.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Az adatcsatornát. Lásd: [folyamatok létrehozása](data-factory-create-pipelines.md) cikkben alább. 
7. A data factory kiszolgálófigyelési és -kezelési nézetek segítségével folyamat figyelésére. Lásd: [figyelése és kezelése az adat-előállító adatcsatornák](data-factory-monitor-manage-pipelines.md) cikkben alább. 

## <a name="specifying-parameters-for-a-hive-script"></a>A Hive parancsprogram paramétereinek megadása
Ebben a példában játék naplók az Azure Blob Storage naponta okozhatnak, és egy dátum és idő tárolóhelyeinek mappába kerülnek. Szeretné parametrizálja a Hive-parancsfájl és futásidőben dinamikusan adja át a bemeneti mappa helyét és a kimeneti dátum és idő tárolóhelyeinek is készít.

A paraméteres Hive parancsfájl használatára, tegye a következőket

* Adja meg a paramétereket a **meghatározása**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* A Hive parancsfájl tekintse meg a paraméter használatával **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
## <a name="see-also"></a>Lásd még:
* [A Pig-tevékenység](data-factory-pig-activity.md)
* [MapReduce művelethez](data-factory-map-reduce.md)
* [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

