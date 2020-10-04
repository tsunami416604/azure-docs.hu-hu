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
ms.date: 09/17/2020
ms.openlocfilehash: 5d312e5ccc0eeed3e17ffa855c496779c5769c5c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707735"
---
A **számítási célok az egyik betanítási feladatokból a következőre használhatók**. Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is.  Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) .

Az alábbi erőforrások bármelyikét használhatja a betanítási számítási célra a legtöbb feladathoz.  Azonban nem minden erőforrás használható automatikus gépi tanuláshoz, gépi tanulási folyamatokhoz vagy tervezőhöz:

|Képzési &nbsp; célok|[Automatizált ML](../articles/machine-learning/concept-automated-ml.md) | [Gépi tanulási folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-attach-compute-targets.md#local)| igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| igen | igen | igen |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Igen (az SDK-n keresztül)  | igen |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | igen  | igen | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Igen (csak SDK helyi mód) | igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | igen | &nbsp; |
