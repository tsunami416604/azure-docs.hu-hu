---
title: Adatok átalakítása a Hadoop Hive-tevékenység használatával
description: Megtudhatja, hogyan használhatja a Hive-tevékenységet egy Azure-adat-előállítóban a Hive-lekérdezések futtatásához egy igény szerinti/saját HDInsight-fürtön.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: b4af3f897a12c71d73962735d3fe68d95f138cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912912"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Adatok átalakítása a Hadoop-struktúra használatával az Azure Data Factoryban

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hive-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-hive.md)

A DATA [Factory-folyamat](concepts-pipelines-activities.md) HDInsight-struktúra működése hive-lekérdezéseket hajt végre [a saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy igény szerinti [HDInsight-fürtön.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

Ha most jön az Azure Data Factory, olvassa el a Bevezetés az [Azure Data Factory,](introduction.md) és nem az [oktatóanyag: az adatok átalakítása](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

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
| Tulajdonság            | Leírás                                                  | Kötelező |
| ------------------- | ------------------------------------------------------------ | -------- |
| név                | A tevékenység neve                                         | Igen      |
| leírás         | A tevékenységet leíró szöveg                | Nem       |
| type                | Hive-tevékenység esetén a tevékenység típusa HDinsightHive        | Igen      |
| linkedServiceName   | Hivatkozás a Data Factory csatolt szolgáltatásként regisztrált HDInsight-fürtre. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| parancsfájlLinkedService | Hivatkozás egy Azure Storage-alapú szolgáltatás tárolására használt Hive-parancsfájl t. Ha nem adja meg ezt a csatolt szolgáltatást, a HDInsight csatolt szolgáltatásban definiált Azure Storage Csatolt szolgáltatás lesz használva. | Nem       |
| scriptPath          | Adja meg a script file tárolt parancsfájl a scriptLinkedService által hivatkozott parancsfájl elérési útját. A fájlnév nem imitáta a kis- és nagybetűk között. | Igen      |
| getDebugInfo        | Itt adható meg, hogy a naplófájlok at a HDInsight-fürt (vagy) által a scriptLinkedService által megadott által használt Azure Storage-ba másolja-e a rendszer. Engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| Érvek           | Egy Hadoop-feladat argumentumainak tömbjét adja meg. Az argumentumok parancssori argumentumként kerülnek átadásra az egyes tevékenységekhez. | Nem       |
| Meghatározza             | Adja meg a paramétereket kulcs/érték párokként a Hive-parancsfájlon belüli hivatkozáshoz. | Nem       |
| queryTimeout        | Lekérdezési időtúlérték (percben). Akkor alkalmazható, ha a HDInsight-fürt vállalati biztonsági csomaggal van engedélyezve. | Nem       |

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat: 

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
