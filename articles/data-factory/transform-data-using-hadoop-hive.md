---
title: Hadoop Hive tevékenység használata az Azure Data Factory adatok átalakítása |} Microsoft Docs
description: Ismerje meg, hogyan használhatja a Hive tevékenység egy Azure data factoryban a-igény szerint vagy a saját HDInsight-fürtök a Hive-lekérdezések futtatásához.
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
ms.openlocfilehash: fc7c2c49de582a413b49d31c4b4e062d81e5e6ae
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051324"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Hadoop Hive tevékenység használata az Azure Data Factory adatok átalakítása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](v1/data-factory-hive-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-hive.md)

A HDInsight Hive tevékenység egy adat-előállítóban [csővezeték](concepts-pipelines-activities.md) Hive-lekérdezések végrehajtása a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](transform-data.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést.

Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](introduction.md) hajtsa végre a [oktatóanyag: adatok](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| type                | Hive tevékenység a tevékenység típusa nem HDinsightHive | Igen      |
| linkedServiceName   | A HDInsight-fürt mutató hivatkozás a Data Factory kapcsolt szolgáltatásként regisztrálva. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| scriptLinkedService | Egy Azure Storage társított szolgáltatást fogja tárolni a Hive-parancsfájl futtatását hivatkozás. A társított szolgáltatás nem adja meg, ha az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott szolgál. | Nem       |
| scriptPath          | Adja meg a fájl elérési útját a parancsfájl a scriptLinkedService által hivatkozott Azure Storage szolgáltatásban tárolja. A fájlnév pedig kis-és nagybetűket. | Igen      |
| getDebugInfo        | Itt adhatja meg, ha a naplófájlok kerülnek az Azure Storage HDInsight-fürt által használt (vagy) leírt módon scriptLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |
| argumentumok           | Hadoop-feladat argumentumok tömbjét adja meg. Az argumentumok parancssori argumentumként átadott minden egyes tevékenységhez. | Nem       |
| határozza meg             | Adja meg a paraméterek kulcs/érték párok való hivatkozás a Hive-parancsfájl belül. | Nem       |

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

