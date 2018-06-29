---
title: Azure Data Factory használatával Hadoop Pig tevékenység adatok átalakítása |} Microsoft Docs
description: Ismerje meg, hogyan használhatja a Pig-tevékenység egy Azure data factoryban a-igény szerint vagy a saját HDInsight-fürtök Pig-parancsfájlok futtatásra.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 9e769cc436011defe89b12680150e6f9c3b3faf8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049317"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Azure Data Factory használatával Hadoop Pig tevékenység adatok átalakítása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](v1/data-factory-pig-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-pig.md)

A HDInsight a Pig-tevékenység egy adat-előállítóban [csővezeték](concepts-pipelines-activities.md) Pig lekérdezések végrehajtása a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](transform-data.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](introduction.md) hajtsa végre a [oktatóanyag: adatok](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Tulajdonság            | Leírás                              | Szükséges |
| ------------------- | ---------------------------------------- | -------- |
| név                | A tevékenység neve.                     | Igen      |
| leírás         | Mire használható a tevékenységet leíró szöveg | Nem       |
| type                | Hive tevékenység a tevékenység típusa nem HDinsightPig | Igen      |
| linkedServiceName   | A HDInsight-fürt mutató hivatkozás a Data Factory kapcsolt szolgáltatásként regisztrálva. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| scriptLinkedService | Egy Azure Storage társított szolgáltatást fogja tárolni a Pig-parancsprogram futtatását hivatkozás. A társított szolgáltatás nem adja meg, ha az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott szolgál. | Nem       |
| scriptPath          | Adja meg a fájl elérési útját a parancsfájl a scriptLinkedService által hivatkozott Azure Storage szolgáltatásban tárolja. A fájlnév pedig kis-és nagybetűket. | Nem       |
| getDebugInfo        | Itt adhatja meg, ha a naplófájlok kerülnek az Azure Storage HDInsight-fürt által használt (vagy) leírt módon scriptLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |
| argumentumok           | Hadoop-feladat argumentumok tömbjét adja meg. Az argumentumok parancssori argumentumként átadott minden egyes tevékenységhez. | Nem       |
| határozza meg             | Adja meg a paraméterek kulcs/érték párok való hivatkozás a Pig-parancsprogram belül. | Nem       |

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
