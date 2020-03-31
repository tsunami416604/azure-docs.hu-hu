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
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156289"
---
**A számítási célok egyik képzési feladatról a másikra újra felhasználhatók.** Ha például egy távoli virtuális gép csatlakoztatása a munkaterülethez, újra felhasználhatja azt több feladathoz.  Gépi tanulási folyamatok esetén használja a megfelelő [folyamatlépés](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) minden számítási cél hoz.

|Képzési &nbsp;célok|[Automatizált ML](../articles/machine-learning/concept-automated-ml.md) | [Gépi tanulási folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Az Azure Machine Learning tervezője](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-set-up-training-targets.md#local)| igen | &nbsp; | &nbsp; |
|[Azure Machine Learning számítási fürt](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Igen& <br/>hiperparaméter-hangolás&nbsp; | igen | igen |
|[Távoli virtuális gép](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Igen& <br/>hiperparaméter-hangolás | igen | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| igen (csak SDK helyi módban) | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | igen | &nbsp; |
