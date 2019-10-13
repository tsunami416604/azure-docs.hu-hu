---
title: Azure Machine Learning szolgáltatási folyamatok végrehajtása a Azure Data Factory-folyamatokban | Microsoft Docs
description: Ismerje meg, hogyan futtathatja Azure Machine Learning szolgáltatási folyamatait a Azure Data Factory folyamatokban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: ddaa020b3567f8f00cfd7fc7f7728f1160d9306b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301346"
---
# <a name="execute-azure-machine-learning-service-pipelines-in-azure-data-factory"></a>Azure Machine Learning szolgáltatási folyamatok végrehajtása a Azure Data Factory

Azure Machine Learning szolgáltatási folyamatait a Azure Data Factory-folyamatok lépéseként futtathatja. A Machine Learning folyamat végrehajtása tevékenység lehetővé teszi a kötegelt előrejelzési forgatókönyvek használatát, például a lehetséges kölcsönzési beállítások azonosítását, a hangulat meghatározását és az ügyfelek viselkedési mintáinak elemzését.

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | A folyamatban szereplő tevékenység neve | Sztring | Igen
type | A tevékenység típusa "AzureMLExecutePipeline". | Sztring | Igen
linkedServiceName | Társított szolgáltatás Azure Machine Learning szolgáltatáshoz | Társított szolgáltatás leírása | Igen
mlPipelineId | A közzétett Azure Machine Learning folyamat azonosítója | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Igen
experimentName | A Machine Learning folyamat futtatási kísérletének neve | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem
mlPipelineParameters | A közzétett Azure Machine Learning folyamat-végpontnak átadandó kulcs-érték párok. A kulcsoknak meg kell egyezniük a közzétett Machine Learningi folyamatban megadott folyamat-paraméterek neveivel. | Kulcs értékű párokkal (vagy resultType objektummal rendelkező kifejezéssel) rendelkező objektum | Nem
mlParentRunId | A szülő Azure Machine Learning szolgáltatási folyamat futtatási azonosítója | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem
continueOnStepFailure | Annak megadása, hogy folytatja-e a Machine Learning folyamat más lépéseinek végrehajtását, ha egy lépés meghiúsul | logikai | Nem

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást:

* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
