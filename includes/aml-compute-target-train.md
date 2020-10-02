---
title: fájlbefoglalás
description: fájlbefoglalás
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91644646"
---
A **számítási célok az egyik betanítási feladatokból a következőre használhatók**. Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is.  Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) .

|Képzési &nbsp; célok|[Automatizált ML](../articles/machine-learning/concept-automated-ml.md) | [Gépi tanulási folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| igen & <br/>hiperparaméter &nbsp; finomhangolása | igen | igen |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | Igen (az SDK-n keresztül) | igen |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| Igen (csak SDK helyi mód) | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | igen | &nbsp; |
