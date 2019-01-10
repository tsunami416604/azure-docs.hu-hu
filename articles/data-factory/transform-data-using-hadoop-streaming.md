---
title: Adatok átalakítása Hadoop Streamelési tevékenységben használatával az Azure Data Factoryban |} A Microsoft Docs
description: Ismerteti, hogyan Hadoop Streamelési tevékenységben az Azure Data Factoryban az adatok átalakítása Hadoop Streamelési programok egy Hadoop-fürtön való futtatásával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: b498e09e53f8b0844470bf3948a664d8ad4337b7
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022224"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Adatok átalakítása Hadoop Streamelési tevékenységben használatával az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-streaming.md)

A HDInsight Streaming-tevékenység az adat-előállító [folyamat](concepts-pipelines-activities.md) Hadoop Streamelési programok lefut a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](introduction.md) és végezze el a [oktatóanyag: adatok átalakítása](tutorial-transform-data-spark-powershell.md) Ez a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-példa
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| név              | A tevékenység neve                     | Igen      |
| leírás       | Mire használható a tevékenységet leíró szöveg | Nem       |
| type              | A Hadoop Streamelési tevékenységben a tevékenység típusa a HDInsightStreaming | Igen      |
| linkedServiceName | Referencia a HDInsight-fürthöz társított szolgáltatásként a Data Factory regisztrálva. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| eseményleképező            | A végrehajtható eseményleképező neve | Igen      |
| Nyomáscsökkentő           | A végrehajtható nyomáscsökkentő neve | Igen      |
| egyesítő          | A végrehajtható egyesítő neve | Nem       |
| fileLinkedService | Egy Azure Storage társított szolgáltatás hivatkozik a Teljesítményleképező egyesítő és nyomáscsökkentő programok futtatását tárolására használható. Ha nem adja meg ezt a társított szolgáltatást, az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott használja. | Nem       |
| filePath          | Adja meg a elérési út tömbjét Leképezőjét, egyesítő, hogy, és az Azure Storage-ban tárolt nyomáscsökkentő programok fileLinkedService által hivatkozott. Az elérési út megkülönbözteti a kis- és nagybetűket. | Igen      |
| bemenet             | Megadja a Teljesítményleképező a WASB-elérési út a bemeneti fájl. | Igen      |
| output            | Megadja a nyomáscsökkentő a WASB-elérési út a kimeneti fájlba. | Igen      |
| getDebugInfo      | Itt adhatja meg, amikor a naplófájlok másolja az Azure Storage HDInsight-fürt által használt (vagy) scriptlinkedservice szolgáltatás által megadott. Megengedett értékek: Nincs, mindig, vagy sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| argumentumok         | Argumenty Pro Hadoop-feladatok egy tömbjét adja meg. Az argumentumok adhatók be a parancssori argumentumok minden egyes tevékenységhez. | Nem       |
| határozza meg           | Kulcs/érték párokként paraméterek megadása a Hive-parancsfájl belül hivatkozik. | Nem       | 

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
