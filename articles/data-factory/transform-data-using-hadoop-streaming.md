---
title: Adatok átalakítása a Hadoop-streamelési tevékenység használatával
description: A hadoop-streamelési tevékenység azure Data Factory ban az adatok átalakítása hadoop-fürtön való futtatásával történő átalakítása.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: c1bba6903fe1cb8cc5bae9a12153553594180b43
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418881"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Adatok átalakítása a Hadoop streamelési tevékenységével az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Data [Factory-folyamat](concepts-pipelines-activities.md) HDInsight-streamelési tevékenysége [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy igény szerinti [HDInsight-fürtön](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) hajtja végre a Hadoop-streamelési programokat. Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

Ha most jön az Azure Data Factory, olvassa el a Bevezetés az [Azure Data Factory,](introduction.md) és nem az [oktatóanyag: az adatok átalakítása](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="json-sample"></a>JSON-minta
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

| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| név              | A tevékenység neve                     | Igen      |
| leírás       | A tevékenységet leíró szöveg | Nem       |
| type              | A Hadoop streamelési tevékenység esetén a tevékenység típusa HDInsightStreaming | Igen      |
| linkedServiceName | Hivatkozás a Data Factory csatolt szolgáltatásként regisztrált HDInsight-fürtre. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| Mapper            | Megadja a leképező végrehajtható fájl nevét. | Igen      |
| Szűkítő           | Megadja a szűkítő végrehajtható fájljának nevét. | Igen      |
| Közösítő          | Megadja a kombájn végrehajtható fájlnevét. | Nem       |
| fájlLinkedService | Hivatkozás egy Azure Storage-alapú szolgáltatás tárolására használt mapper, Combiner és szűkítő programokat kell végrehajtani. Ha nem adja meg ezt a csatolt szolgáltatást, a HDInsight csatolt szolgáltatásban definiált Azure Storage Csatolt szolgáltatás lesz használva. | Nem       |
| filePath          | Adja meg a mapper, Combiner és a Reducer programok tárolt a fileLinkedService által hivatkozott Azure Storage elérési út. Az elérési út megkülönbözteti a kis- és nagybetűket. | Igen      |
| bemenet             | Megadja a mapper bemeneti fájljának WASB elérési útját. | Igen      |
| output            | Megadja a szűkítő kimeneti fájljának WASB elérési útját. | Igen      |
| getDebugInfo      | Itt adható meg, hogy a naplófájlok at a HDInsight-fürt (vagy) által a scriptLinkedService által megadott által használt Azure Storage-ba másolja-e a rendszer. Engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| Érvek         | Egy Hadoop-feladat argumentumainak tömbjét adja meg. Az argumentumok parancssori argumentumként kerülnek átadásra az egyes tevékenységekhez. | Nem       |
| Meghatározza           | Adja meg a paramétereket kulcs/érték párokként a Hive-parancsfájlon belüli hivatkozáshoz. | Nem       | 

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat: 

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
