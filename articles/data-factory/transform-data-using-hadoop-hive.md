---
title: Adatok átalakítása Hadoop Hive-tevékenység használatával az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a Hive-tevékenység az Azure data factory a Hive-lekérdezések futtatásához egy a-igény és a saját HDInsight-fürtön.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: douglasl
ms.openlocfilehash: e77b8c89954064fb143f04f0f901d1512d4fc50d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491008"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Adatok átalakítása Hadoop Hive-tevékenység használatával az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-hive-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-hive.md)

A HDInsight Hive tevékenység, egy Data factoryben [folyamat](concepts-pipelines-activities.md) a Hive-lekérdezéseket hajt végre [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése.

Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](introduction.md) és végezze el a [oktatóanyag: adatok átalakítása](tutorial-transform-data-spark-powershell.md) Ez a cikk elolvasása előtt. 

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
| Tulajdonság            | Leírás                                                  | Szükséges |
| ------------------- | ------------------------------------------------------------ | -------- |
| név                | A tevékenység neve                                         | Igen      |
| leírás         | Mire használható a tevékenységet leíró szöveg                | Nem       |
| type                | A Hive-tevékenység a tevékenység típusát HDinsightHive        | Igen      |
| linkedServiceName   | Referencia a HDInsight-fürthöz társított szolgáltatásként a Data Factory regisztrálva. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| scriptLinkedService | A Hive-szkriptet futtatandó tárolására használt Azure Storage társított szolgáltatás hivatkozik. Ha nem adja meg ezt a társított szolgáltatást, az Azure Storage társított szolgáltatás a HDInsight társított szolgáltatásban meghatározott használja. | Nem       |
| scriptPath          | Adja meg a scriptlinkedservice szolgáltatás által hivatkozott Azure Storage-ban tárolt a parancsfájl elérési útját. A fájlnév formátuma a kis-és nagybetűket. | Igen      |
| getDebugInfo        | Itt adhatja meg, amikor a naplófájlok másolja az Azure Storage HDInsight-fürt által használt (vagy) scriptlinkedservice szolgáltatás által megadott. Megengedett értékek: Nincs, mindig, vagy sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| argumentumok           | Argumenty Pro Hadoop-feladatok egy tömbjét adja meg. Az argumentumok adhatók be a parancssori argumentumok minden egyes tevékenységhez. | Nem       |
| határozza meg             | Kulcs/érték párokként paraméterek megadása a Hive-parancsfájl belül hivatkozik. | Nem       |
| queryTimeout        | Lekérdezés időkorlátot (percben). Akkor alkalmazható, ha a HDInsight-fürtön vállalati biztonsági csomaggal engedélyezve van. | Nem       |

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
