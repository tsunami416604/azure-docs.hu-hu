---
title: Azure Machine Learning-folyamatok végrehajtása
description: Ismerje meg, hogyan futtathatja az Azure Machine Learning-folyamatokat az Azure Data Factory-folyamatokban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419000"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Machine Learning-folyamatok végrehajtása az Azure Data Factoryban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Futtassa az Azure Machine Learning-folyamatokat az Azure Data Factory-folyamatok lépéseként. A Machine Learning végrehajtási folyamat tevékenység lehetővé teszi a kötegelt előrejelzési forgatókönyvek, például a lehetséges hitel alapértelmezett értékek azonosítását, a hangulat meghatározását és az ügyfelek viselkedési mintáinak elemzését.

Az alábbi videó tartalmaz egy hat perces bevezetése és bemutatása ezt a funkciót.

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

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | A folyamatban lévő tevékenység neve | Sztring | Igen
type | A tevékenység típusa "AzureMLExecutePipeline" | Sztring | Igen
linkedServiceName | Csatolt szolgáltatás az Azure Machine Learninghez | Csatolt szolgáltatás hivatkozása | Igen
ml PipelineId | A közzétett Azure Machine Learning-folyamat azonosítója | Karakterlánc (vagy kifejezés resultKarakterlánctal) | Igen
experimentName (kísérletezésneve) | A Machine Learning-folyamat futtatásának előzménykísérletének futtatása | Karakterlánc (vagy kifejezés resultKarakterlánctal) | Nem
mlPipelineParameters | Kulcs, érték párok át kell adni a közzétett Azure Machine Learning-folyamat végpont. A kulcsoknak meg kell egyezniük a közzétett Machine Learning-folyamatban definiált folyamatparaméterek nevével. | Kulcsérték-párokkal rendelkező objektum (vagy kifejezés resultType objektummal) | Nem
mlParentRunId | A szülő Azure Machine Learning-folyamat futtatási azonosítója | Karakterlánc (vagy kifejezés resultKarakterlánctal) | Nem
continueOnStepFailure | A Machine Learning-folyamat egyéb lépéseinek végrehajtásának folytatása, ha egy lépés sikertelen | logikai | Nem

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat:

* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
