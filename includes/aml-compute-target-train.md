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
ms.date: 12/04/2019
ms.openlocfilehash: 6ddff84de6b8ffd5bc8f7c7dcaa7cb4df3d71f81
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703898"
---
A **számítási célok az egyik betanítási feladatokból a következőre használhatók**. Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is.  Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) .

|Képzési &nbsp; célok|[Automatizált ML](../articles/machine-learning/concept-automated-ml.md) | [Gépi tanulási folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-set-up-training-targets.md#local)| igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| igen & <br/>hiperparaméter &nbsp; finomhangolása | igen | igen |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | igen & <br/>hiperparaméter finomhangolása | igen |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Igen (csak SDK helyi mód) | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | igen | &nbsp; |
