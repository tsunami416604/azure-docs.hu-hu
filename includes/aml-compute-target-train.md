---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 2c275e1d8f54191e12454f7e97a8dd59e7c84be2
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539384"
---
A **számítási célok az egyik betanítási feladatokból a következőre használhatók**. Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is.  Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) .

|&nbsp;célok betanítása|[Automatikus ML](../articles/machine-learning/concept-automated-ml.md) | [ML-folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-set-up-training-targets.md#local)| igen | &nbsp; | &nbsp; |
|[Azure Machine Learning számítási példány (előzetes verzió)](../articles/machine-learning/concept-compute-instance.md) | | igen |  |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Igen & <br/>hiperparaméter&nbsp;finomhangolása | igen | igen |
|[Távoli virtuális gép](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| igen | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | igen | &nbsp; |
