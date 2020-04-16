---
title: Adatok átalakítása a Hadoop Pig tevékenységgel
description: Megtudhatja, hogyan használhatja a Pig-tevékenységet egy Azure-beli adatgyárban a Pig-parancsfájlok igény szerinti/saját HDInsight-fürtön való futtatásához.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 73ce8c670940a31af6a88f98bfd5880ede259e01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418898"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Adatok átalakítása a Hadoop Pig-tevékenység használatával az Azure Data Factoryban

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-pig-activity.md)
> * [Aktuális verzió](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Data [Factory-folyamat](concepts-pipelines-activities.md) HDInsight Pig-tevékenysége saját vagy igény szerinti HDInsight-fürtön hajtja végre [a](compute-linked-services.md#azure-hdinsight-linked-service) [Pig-lekérdezéseket.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.

Ha most jön az Azure Data Factory, olvassa el a Bevezetés az [Azure Data Factory,](introduction.md) és nem az [oktatóanyag: az adatok átalakítása](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

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

| Tulajdonság            | Leírás                              | Kötelező |
| ------------------- | ---------------------------------------- | -------- |
| név                | A tevékenység neve                     | Igen      |
| leírás         | A tevékenységet leíró szöveg | Nem       |
| type                | Hive-tevékenység esetén a tevékenység típusa HDinsightPig | Igen      |
| linkedServiceName   | Hivatkozás a Data Factory csatolt szolgáltatásként regisztrált HDInsight-fürtre. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| parancsfájlLinkedService | Hivatkozás egy Azure Storage-alapú szolgáltatás tárolására használt Pig parancsfájl t. Ha nem adja meg ezt a csatolt szolgáltatást, a HDInsight csatolt szolgáltatásban definiált Azure Storage Csatolt szolgáltatás lesz használva. | Nem       |
| scriptPath          | Adja meg a script file tárolt parancsfájl a scriptLinkedService által hivatkozott parancsfájl elérési útját. A fájlnév nem imitáta a kis- és nagybetűk között. | Nem       |
| getDebugInfo        | Itt adható meg, hogy a naplófájlok at a HDInsight-fürt (vagy) által a scriptLinkedService által megadott által használt Azure Storage-ba másolja-e a rendszer. Engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Alapértelmezett érték: Nincs. | Nem       |
| Érvek           | Egy Hadoop-feladat argumentumainak tömbjét adja meg. Az argumentumok parancssori argumentumként kerülnek átadásra az egyes tevékenységekhez. | Nem       |
| Meghatározza             | Adja meg a paramétereket kulcs/érték párként a Pig parancsfájlon belüli hivatkozáshoz. | Nem       |

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat: 

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
