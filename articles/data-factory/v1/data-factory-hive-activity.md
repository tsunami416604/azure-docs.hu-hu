---
title: Adatok átalakítása a Hive-tevékenység – az Azure használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Hive-tevékenység az Azure data factory a Hive-lekérdezések futtatásához egy a-igény és a saját HDInsight-fürtön.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4b622a5925aebd140fed2ac74eaf7cc186803b90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113755"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Adatok átalakítása a Hive-tevékenység használatával az Azure Data Factoryban 
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása a Hive-tevékenység használatával a Data Factory](../transform-data-using-hadoop-hive.md).

A HDInsight Hive tevékenység, egy Data factoryben [folyamat](data-factory-create-pipelines.md) a Hive-lekérdezéseket hajt végre [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-alapú HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

> [!NOTE] 
> Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](data-factory-introduction.md) és végezze el az oktatóanyag: [Az első adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md) Ez a cikk elolvasása előtt. 

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
| név |A tevékenység neve |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg |Nem |
| type |HDinsightHive |Igen |
| bemenetek |A Hive-tevékenység által felhasznált bemeneti |Nem |
| kimenetek |A Hive-tevékenység által előállított kimeneti |Igen |
| linkedServiceName |A HDInsight-fürt a Data Factory társított szolgáltatásként regisztrált mutató hivatkozás |Igen |
| parancsfájl |Adja meg a Hive-parancsfájl beágyazott |Nem |
| parancsprogram elérési útja |Store a Hive-szkript egy Azure blob Storage, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév formátuma a kis-és nagybetűket. |Nem |
| határozza meg |Adja meg a paramétereket a kulcs/érték párokként való belül a Hive-parancsfájl használatával "hiveconf: |Nem |

## <a name="example"></a>Példa
Vegyünk egy példát, játék-naplók analytics, ahol szeretné azonosítani a játékok indítja el a vállalati felhasználók töltött időt. 

A következő naplófájl, játék mintanapló, amelyeket egy vessző (`,`) választ el, és a következő mezőket – ProfileID, SessionStart, időtartamának, SrcIPAddress és GameType tartalmazza.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

A **Hive-parancsprogram** feldolgozni ezeket az adatokat:

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

A Hive-parancsfájl végrehajtása a Data Factory-folyamatok, tegye a következőket kell

1. Létrehoz egy társított szolgáltatást, regisztrálnia [a saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy konfigurálása [igény szerinti HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Adjuk a társított szolgáltatás "HDInsightLinkedService".
2. Hozzon létre egy [társított szolgáltatás](data-factory-azure-blob-connector.md) a adatok tárolásához Azure Blob Storage-kapcsolat konfigurálása. Adjuk a társított szolgáltatás "StorageLinkedService"
3. Hozzon létre [adatkészletek](data-factory-create-datasets.md) mutat a bemeneti és kimeneti adatokat. Adjuk a bemeneti adatkészlet "HiveSampleIn" és a kimeneti adatkészlet "HiveSampleOut"
4. Másolás a Hive-lekérdezést az Azure Blob Storage-fájlként #2. lépésben konfigurált. a storage, az adatok tárolásához eltér a lekérdezés fájl kiszolgálójára, ha egy külön Azure Storage társított szolgáltatás létrehozása, és a tevékenység hivatkozik rá. Használja **scriptPath** , adja meg a hive-lekérdezés fájl elérési útját és **scriptLinkedService** , adja meg az Azure storage, amely tartalmazza a parancsfájlban. 
   
   > [!NOTE]
   > A Hive-parancsfájl beágyazott a tevékenység meghatározásában használatával is megadhatja a **parancsfájl** tulajdonság. Hogy mint belül a JSON-dokumentum igényeinek kell megadni a parancsfájl speciális karaktereket nem ajánlott ezt a módszert, és hibakeresési problémákat okozhat. Az ajánlott eljárás, hogy #4. lépés.
   > 
   > 
5. A HDInsightHive tevékenység létrehoz egy folyamatot. A tevékenység folyamatok/átalakítások az adatokat.

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
6. A folyamat üzembe helyezéséhez. Lásd: [folyamatok létrehozása](data-factory-create-pipelines.md) részleteivel. 
7. Monitorozza a folyamatot a data factory figyelési és felügyeleti nézetek használatával. Lásd: [figyelés és kezelheti a Data Factory-folyamatok](data-factory-monitor-manage-pipelines.md) részleteivel. 

## <a name="specifying-parameters-for-a-hive-script"></a>Egy Hive-szkriptet paramétereinek megadása
Ebben a példában játék naplók naponta Azure Blob Storage-bA betöltött, és a egy particionált dátum és idő a mappa tárolja. Szeretné paraméterezni a Hive-szkript és a bemeneti mappa helye dinamikusan továbbítása során futásidejű és is előállíthat a kimeneti dátum és idő particionálni.

A paraméteres Hive-szkript használatához tegye a következőket

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
* A Hive-parancsfájl, tekintse meg a paraméter használatával **${hiveconf:parameterName}**. 
  
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
* [Pig-tevékenység](data-factory-pig-activity.md)
* [MapReduce-tevékenység](data-factory-map-reduce.md)
* [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

