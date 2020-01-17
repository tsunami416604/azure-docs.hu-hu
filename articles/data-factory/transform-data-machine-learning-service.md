---
title: Azure Machine Learning folyamatok végrehajtása
description: Megtudhatja, hogyan futtathatja Azure Machine Learning folyamatait a Azure Data Factory folyamatokban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: b54504cf8ca7b32bf14bd4b7e0c561ffd56d4098
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155163"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Machine Learning folyamatok végrehajtása Azure Data Factory

Futtassa a Azure Machine Learning folyamatokat a Azure Data Factory folyamatok lépéseként. A Machine Learning folyamat végrehajtása tevékenység lehetővé teszi a kötegelt előrejelzési forgatókönyvek használatát, például a lehetséges kölcsönzési beállítások azonosítását, a hangulat meghatározását és az ügyfelek viselkedési mintáinak elemzését.

Az alábbi videó a szolgáltatás hat perces bevezetését és bemutatóját tartalmazza.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

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
linkedServiceName | Társított szolgáltatás Azure Machine Learning | Társított szolgáltatás leírása | Igen
mlPipelineId | A közzétett Azure Machine Learning folyamat azonosítója | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Igen
ExperimentName | A Machine Learning folyamat futtatási kísérletének neve | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem
mlPipelineParameters | A közzétett Azure Machine Learning folyamat-végpontnak átadandó kulcs-érték párok. A kulcsoknak meg kell egyezniük a közzétett Machine Learningi folyamatban megadott folyamat-paraméterek neveivel. | Kulcs értékű párokkal (vagy resultType objektummal rendelkező kifejezéssel) rendelkező objektum | Nem
mlParentRunId | A szülő Azure Machine Learning folyamat futtatási azonosítója | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem
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
