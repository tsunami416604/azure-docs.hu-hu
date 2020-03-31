---
title: Adatok átalakítása a Hive-tevékenység használatával – Azure
description: Megtudhatja, hogyan használhatja a Hive-tevékenységet egy Azure-adat-előállítóban a Hive-lekérdezések futtatásához egy igény szerinti/saját HDInsight-fürtön.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d153f8c316cbb76e063f07f7f823c8d9c4a21f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703357"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Adatok átalakítása az Azure Data Factory ban lévő Hive-tevékenység használatával 
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatok átalakítása a Data Factory hive-tevékenységhasználatával című témakört.](../transform-data-using-hadoop-hive.md)

A DATA [Factory-folyamat](data-factory-create-pipelines.md) HDInsight-struktúra működése [hive-lekérdezéseket](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) hajt végre a [saját](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön. Ez a cikk az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha most jön az Azure Data Factory, olvassa el [az Azure Data Factory bevezetés](data-factory-introduction.md) című, és nem az oktatóanyag: [Készítse el az első adatfolyamat](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt. 

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
| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| név |A tevékenység neve |Igen |
| leírás |A tevékenységet leíró szöveg |Nem |
| type |HDinsightHive |Igen |
| Bemenetek |A Hive-tevékenység által felhasznált bemenetek |Nem |
| Kimenetek |A Hive-tevékenység által termelt eredmények |Igen |
| linkedServiceName |Hivatkozás a Data Factory ban csatolt szolgáltatásként regisztrált HDInsight-fürtre |Igen |
| szkriptet. |Adja meg a Hive parancsfájl szövegközi |Nem |
| scriptPath |A Hive-parancsfájlt egy Azure blobstorage-ban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév nem imitáta a kis- és nagybetűk között. |Nem |
| Meghatározza |Paraméterek megadása kulcs-/értékpárokként a Hive-parancsfájlon belüli hivatkozáshoz a "hiveconf" használatával |Nem |

## <a name="example"></a>Példa
Vegyünk egy példát a játéknaplók elemzésére, ahol azonosítani szeretné a vállalat által indított játékokat játszó felhasználók által eltöltött időt. 

A következő napló egy minta játék napló,`,`amely vesszővel ( ) külön, és tartalmazza a következő mezőket - ProfileID, SessionStart, Időtartam, SrcIPAddress, és GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

A **Hive parancsfájl** az adatok feldolgozásához:

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

A Hive-parancsfájl data factory folyamatban való végrehajtásához a következőt kell tennie:

1. Hozzon létre egy összekapcsolt szolgáltatást [saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) regisztrálásához vagy az igény szerinti [HDInsight számítási fürt konfigurálásához.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Hívjuk ezt a kapcsolt szolgáltatást "HDInsightLinkedService"-nek.
2. Hozzon létre egy [összekapcsolt szolgáltatást](data-factory-azure-blob-connector.md) az adatokat tároló Azure Blob storage-hoz való csatlakozás konfigurálásához. Nevezzük ezt a kapcsolt szolgáltatást "StorageLinkedService"
3. Hozzon létre [adatkészleteket,](data-factory-create-datasets.md) amelyek a bemeneti és a kimeneti adatokra mutatnak. Hívjuk meg a bemeneti adatkészletet "HiveSampleIn" és a "HiveSampleOut" kimeneti adatkészletet
4. Másolja a Hive-lekérdezést fájlként az Azure Blob Storage-ba, amely #2 lépésben konfigurálva. ha az adatok tárolása eltér a lekérdezési fájlt tároló tárolótól, hozzon létre egy külön Azure Storage-kapcsolt szolgáltatást, és hivatkozzon rá a tevékenységben. A **scriptPath** segítségével adja meg a struktúra lekérdezési fájl és **scriptLinkedService** elérési útját a parancsfájlt tartalmazó Azure-tároló megadásához. 
   
   > [!NOTE]
   > A hive-parancsfájl inline a tevékenységdefinícióban is megadhat a **parancsfájl** tulajdonság használatával. Nem javasoljuk ezt a módszert, mivel a JSON-dokumentumban lévő parancsfájl összes speciális karakterét meg kell kerülni, és hibakeresési problémákat okozhat. A legjobb gyakorlat a #4 lépés ének követése.
   > 
   > 
5. Hozzon létre egy folyamatot a HDInsightHive tevékenységgel. A tevékenység feldolgozza/átalakítja az adatokat.

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
6. Telepítse a folyamatot. A részleteket a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. 
7. Figyelje a folyamatot az adat-előállító figyelési és felügyeleti nézetek használatával. A data [factory folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md) című cikkben a részletekről olvashat. 

## <a name="specifying-parameters-for-a-hive-script"></a>Hive-parancsfájl paramétereinek megadása
Ebben a példában a játéknaplók naponta kerülnek az Azure Blob Storage-ba, és egy dátummal és idővel particionált mappában tárolódnak. Szeretné paraméterezni a Hive-parancsfájlt, és adja át a bemeneti mappa helyét dinamikusan futásidőben, és a kimeneti particionált dátummal és idővel.

A paraméterezett Hive-parancsfájl használatához tegye a következőket:

* Adja meg a paramétereket **a definíciókban.**

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
* A Hive-parancsfájlban tekintse meg a paramétert a **${hiveconf:parameterName} használatával.** 
  
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
* [Sertés tevékenység](data-factory-pig-activity.md)
* [MapReduce tevékenység](data-factory-map-reduce.md)
* [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

