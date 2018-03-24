---
title: Azure Data Factory használatával Hadoop Streamelési tevékenységben adatok átalakítása |} Microsoft Docs
description: Adatok átalakítása Hadoop Streamelési programok futtatásában a Hadoop-fürt által használható az Azure Data Factory Hadoop Streamelési tevékenységben ismerteti.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 7c882e6fd826adb415b0452c9b441405d2ff90d7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory használatával Hadoop Streamelési tevékenységben adatok átalakítása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-hadoop-streaming-activity.md)
> * [2. verzió – Előzetes verzió](transform-data-using-hadoop-streaming.md)

A HDInsight Streamelési tevékenységben egy adat-előállítóban [csővezeték](concepts-pipelines-activities.md) Hadoop Streamelési programok végrehajtása a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](transform-data.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Hadoop Streamelési tevékenységben a V1](v1/data-factory-hadoop-streaming-activity.md).

Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](introduction.md) hajtsa végre a [oktatóanyag: adatok](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-mintát
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
| név              | A tevékenység neve.                     | Igen      |
| leírás       | Mire használható a tevékenységet leíró szöveg | Nem       |
| type              | A Hadoop Streamelési tevékenységben a tevékenység típusa nem HDInsightStreaming | Igen      |
| linkedServiceName | A HDInsight-fürt mutató hivatkozás a Data Factory kapcsolt szolgáltatásként regisztrálva. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| eseményleképező            | A végrehajtható hozzárendelő neve | Igen      |
| Nyomáscsökkentő           | A végrehajtható nyomáscsökkentő neve | Igen      |
| egyesítő          | A végrehajtható egyesítő neve | Nem       |
| fileLinkedService | Az Azure tárolás társított szolgáltatásának mutató hivatkozás Teljesítményleképező egyesítő és nyomáscsökkentő programok futtatását tárolására használható. A társított szolgáltatás nem adja meg, ha az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott szolgál. | Nem       |
| filePath          | Elérési út tömbjét adja meg a leképező egyesítő, és az Azure Storage-ban tárolt nyomáscsökkentő programok fileLinkedService hivatkozik. Az elérési út megkülönbözteti a kis- és nagybetűket. | Igen      |
| Bemeneti             | Meghatározza a bemeneti fájl WASB elérési útja a leképező. | Igen      |
| output            | Meghatározza a WASB a kimeneti fájl elérési útja a nyomáscsökkentő. | Igen      |
| getDebugInfo      | Itt adhatja meg, ha a naplófájlok kerülnek az Azure Storage HDInsight-fürt által használt (vagy) leírt módon scriptLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |
| Argumentumok         | Hadoop-feladat argumentumok tömbjét adja meg. Az argumentumok parancssori argumentumként átadott minden egyes tevékenységhez. | Nem       |
| határozza meg           | Adja meg a paraméterek kulcs/érték párok való hivatkozás a Hive-parancsfájl belül. | Nem       | 

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
