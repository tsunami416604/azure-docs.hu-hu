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
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875448"
---
A **számítási célok az egyik betanítási feladatokból a következőre használhatók**. Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is.  Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) .

|&nbsp;célok betanítása|[Automatikus ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML-folyamatok](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Igen & <br/>hiperparaméter&nbsp;finomhangolása | igen | igen |
|[Távoli virtuális gép](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | Igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| igen | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | igen | &nbsp; |
