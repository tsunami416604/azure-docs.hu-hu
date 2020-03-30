---
title: Adatok átalakítása a Pig-tevékenység használatával az Azure Data Factoryban
description: Megtudhatja, hogyan használhatja a Pig-tevékenységet egy Azure-beli adatgyárban a Pig-parancsfájlok igény szerinti/saját HDInsight-fürtön való futtatásához.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b8e7201a6239ef1fe83fb89d4b361995e305bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703195"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Adatok átalakítása a Pig-tevékenység használatával az Azure Data Factoryban
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatok átalakítása a Data Factory Pig-tevékenységhasználatával című témakört.](../transform-data-using-hadoop-pig.md)


A Data [Factory-folyamat](data-factory-create-pipelines.md) HDInsight Pig-tevékenysége saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre [a](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) [Pig-lekérdezéseket.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Ez a cikk az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

> [!NOTE] 
> Ha most jön az Azure Data Factory, olvassa el [az Azure Data Factory bevezetés](data-factory-introduction.md) című, és nem az oktatóanyag: [Készítse el az első adatfolyamat](data-factory-build-your-first-pipeline.md) a cikk elolvasása előtt. 

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

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| név |A tevékenység neve |Igen |
| leírás |A tevékenységet leíró szöveg |Nem |
| type |HDinsightPig |Igen |
| Bemenetek |A Pig tevékenység által felhasznált egy vagy több bemenet |Nem |
| Kimenetek |A Pig tevékenység által előállított egy vagy több kimenet |Igen |
| linkedServiceName |Hivatkozás a Data Factory ban csatolt szolgáltatásként regisztrált HDInsight-fürtre |Igen |
| szkriptet. |A Pig parancsfájl szövegközi megadása |Nem |
| scriptPath |A Pig-parancsfájlt egy Azure blob-tárolóban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév nem imitáta a kis- és nagybetűk között. |Nem |
| Meghatározza |Paraméterek megadása kulcs-/értékpárokként a Pig parancsfájlon belüli hivatkozáshoz |Nem |

## <a name="example"></a>Példa
Vegyünk egy példát a játéknaplók elemzésére, ahol meg szeretné határozni a vállalat által indított játékokat játszó játékosok által eltöltött időt.

A következő minta játék napló egy vesszővel (,) elválasztott fájl. A következő mezőket tartalmazza : ProfileID, SessionStart, Duration, SrcIPAddress és GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

A **Pig szkript** az adatok feldolgozásához:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Ha ezt a Pig-parancsfájlt egy Data Factory-folyamatban szeretné végrehajtani, hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy összekapcsolt szolgáltatást [saját HDInsight számítási fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) regisztrálásához vagy az igény szerinti [HDInsight számítási fürt konfigurálásához.](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Nevezzük ezt a linkelt szolgáltatást **HDInsightLinkedService**.
2. Hozzon létre egy [összekapcsolt szolgáltatást](data-factory-azure-blob-connector.md) az adatokat tároló Azure Blob storage-hoz való csatlakozás konfigurálásához. Nevezzük ezt a linked szolgáltatás **StorageLinkedService**.
3. Hozzon létre [adatkészleteket,](data-factory-create-datasets.md) amelyek a bemeneti és a kimeneti adatokra mutatnak. Hívjuk meg a **bemeneti adatkészletet PigSampleIn** és a kimeneti **adatkészletPigSampleOut**.
4. Másolja a Pig lekérdezést egy fájlba, amelyet az Azure Blob Storage #2 lépésben konfigurált. Ha az adatokat tároló Azure-tároló eltér a lekérdezési fájlt tárolótól, hozzon létre egy külön Azure Storage-kapcsolt szolgáltatást. Tekintse meg a csatolt szolgáltatást a tevékenységkonfigurációban. A **scriptPath** segítségével adja meg a sertés parancsfájl és a **scriptLinkedService**elérési útját. 
   
   > [!NOTE]
   > A Pig parancsfájlt a tevékenységdefinícióban is megadhatja a **parancsfájltulajdonság** használatával. Azonban nem javasoljuk ezt a megközelítést, mivel a parancsfájl összes speciális karakterét meg kell kerülni, és hibakeresési problémákat okozhat. A legjobb gyakorlat a #4 lépés ének követése.
   >
   >
5. Hozza létre a folyamatot a HDInsightPig tevékenységgel. Ez a tevékenység a bemeneti adatokat a PIG parancsfájl HDInsight-fürtön futtatásával dolgozza fel.

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
6. Telepítse a folyamatot. A részleteket a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. 
7. Figyelje a folyamatot az adat-előállító figyelési és felügyeleti nézetek használatával. A data [factory folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md) című cikkben a részletekről olvashat.

## <a name="specifying-parameters-for-a-pig-script"></a>Pig parancsfájl paramétereinek megadása
Vegye figyelembe a következő példát: a játéknaplók naponta kerülnek az Azure Blob Storage-ba, és a dátum és az idő alapján particionált mappában tárolódnak. Szeretné paraméterezni a Pig parancsfájlt, és a bemeneti mappa helyét dinamikusan átadni futásidőben, és a dátumot és az időt particionált kimenetet is létre kívánja adni.

A paraméterezett Pig parancsfájl használatához tegye a következőket:

* Adja meg a paramétereket **a definíciókban.**

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
* A Pig Scriptben tekintse meg a **"$parameterName**" paramétereit a következő példában látható módon:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Lásd még:
* [Hive-tevékenység](data-factory-hive-activity.md)
* [MapReduce tevékenység](data-factory-map-reduce.md)
* [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
* [Spark-programok meghívása](data-factory-spark.md)
* [R-szkriptek meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
