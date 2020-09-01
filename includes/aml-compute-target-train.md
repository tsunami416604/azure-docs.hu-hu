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
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146733"
---
A **számítási célok az egyik betanítási feladatokból a következőre használhatók**. Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is.  Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) .

|Képzési &nbsp; célok|[Automatizált ML](../articles/machine-learning/concept-automated-ml.md) | [Gépi tanulási folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| igen & <br/>hiperparaméter &nbsp; finomhangolása | igen | igen |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | igen & <br/>hiperparaméter finomhangolása | igen |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| Igen (csak SDK helyi mód) | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | igen | &nbsp; |
