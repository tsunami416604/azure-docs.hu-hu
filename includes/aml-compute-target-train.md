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
ms.openlocfilehash: c45b30fb16293652e169b89a6d93520509777a40
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561647"
---
**A számítási célok az egyik betanítási feladatokból a következőre használhatók.** Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is. Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) .

A legtöbb feladathoz a következő források bármelyikét használhatja egy képzési számítási célra. Nem minden erőforrás használható az automatikus gépi tanuláshoz, a gépi tanulási folyamatokhoz vagy a tervezőhöz.

|Képzési &nbsp; célok|[Automatizált gépi tanulás](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning-folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Igen | Igen | Igen |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Igen (az SDK-n keresztül)  | Igen |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Igen  | Igen | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Igen (csak SDK helyi mód) | Igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Igen | &nbsp; |