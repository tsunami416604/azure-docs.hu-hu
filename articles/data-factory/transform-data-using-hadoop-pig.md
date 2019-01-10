---
title: Adatok átalakítása Hadoop Pig-tevékenység használatával az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Pig-tevékenység az Azure data factory, a Pig-parancsfájlok futtatása egy a-igény és a saját HDInsight-fürtön.
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
ms.openlocfilehash: a29bd64c6b18d41028c8952f531698bbfa9e01e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014709"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Adatok átalakítása Hadoop Pig-tevékenység használatával az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-pig-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-pig.md)

A HDInsight Pig-tevékenység az adat-előállító [folyamat](concepts-pipelines-activities.md) a Pig-lekérdezéseket hajt végre [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](introduction.md) és végezze el a [oktatóanyag: adatok átalakítása](tutorial-transform-data-spark-powershell.md) Ez a cikk elolvasása előtt. 

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
| név                | A tevékenység neve                     | Igen      |
| leírás         | Mire használható a tevékenységet leíró szöveg | Nem       |
| type                | A Hive-tevékenység a tevékenység típusát HDinsightPig | Igen      |
| linkedServiceName   | Referencia a HDInsight-fürthöz társított szolgáltatásként a Data Factory regisztrálva. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| scriptLinkedService | A Pig-parancsprogram végrehajtását tárolására használt Azure Storage társított szolgáltatás hivatkozik. Ha nem adja meg ezt a társított szolgáltatást, az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott használja. | Nem       |
| scriptPath          | Adja meg a scriptlinkedservice szolgáltatás által hivatkozott Azure Storage-ban tárolt a parancsfájl elérési útját. A fájlnév formátuma a kis-és nagybetűket. | Nem       |
| getDebugInfo        | Itt adhatja meg, amikor a naplófájlok másolja az Azure Storage HDInsight-fürt által használt (vagy) scriptlinkedservice szolgáltatás által megadott. Megengedett értékek: Nincs, mindig, vagy sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| argumentumok           | Argumenty Pro Hadoop-feladatok egy tömbjét adja meg. Az argumentumok adhatók be a parancssori argumentumok minden egyes tevékenységhez. | Nem       |
| határozza meg             | A Pig-parancsprogram belül való kulcs/érték párok paramétereket adja meg. | Nem       |

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
