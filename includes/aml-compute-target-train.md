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
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806029"
---
**Tárolók is felhasználható egy betanítási feladat a következő számítási**. Például ha csatlakoztat egy távoli virtuális Gépen a munkaterülethez, felhasználhatja azt több feladat esetében.

|Képzési &nbsp;célok| GPU-támogatással |[Automatizált gépi tanulás](../articles/machine-learning/service/concept-automated-ml.md) | [Gépi Tanulási folyamatok](../articles/machine-learning/service/concept-ml-pipelines.md) | [Vizuális felhasználói felületet](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Talán | igen | &nbsp; | &nbsp; |
|[Az Azure Machine Learning Compute](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| igen | Igen & <br/>hiperparaméter&nbsp;hangolása | igen | igen |
|[Távoli virtuális Gépen](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |igen | Igen & <br/>hiperparaméter finomhangolása | igen | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | igen | igen | &nbsp; |
|[Az Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | igen | &nbsp; |
|[Az Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | igen | &nbsp; |
