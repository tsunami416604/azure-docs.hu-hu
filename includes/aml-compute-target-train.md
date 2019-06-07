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
ms.date: 05/30/2019
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752953"
---
|Képzési &nbsp;célok| GPU-támogatással |[Automatizált gépi tanulás](../articles/machine-learning/service/concept-automated-ml.md) | [Gépi Tanulási folyamatok](../articles/machine-learning/service/concept-ml-pipelines.md) | [Vizuális felhasználói felületet](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Talán | igen | &nbsp; | &nbsp; |
|[Az Azure Machine Learning Compute](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| igen | Igen & <br/>hiperparaméter&nbsp;hangolása | igen | igen |
|[Távoli virtuális Gépen](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |igen | Igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | igen | igen | &nbsp; |
|[Az Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | igen | &nbsp; |
|[Az Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | igen | &nbsp; |

**Célok több betanítási feladatokhoz felhasználható számítási**. Például ha csatlakoztat egy távoli virtuális Gépen a munkaterülethez, felhasználhatja azt több feladat esetében.