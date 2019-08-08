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
ms.date: 01/25/2019
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846008"
---
- A Azure Machine Learning SDK for Python telepítve van. A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) utasításait követve tegye a következőket:


1. A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) utasításait követve tegye a következőket:
    * Miniconda-környezet létrehozása [Azure Machine Learning szolgáltatás-munkaterületek létrehozása és kezelése]
    * A Pythonhoz készült Azure Machine Learning SDK telepítése

1. Hozzon létre egy [Azure Machine learning szolgáltatás](../articles/machine-learning/service/how-to-manage-workspace.md)munkaterületet.

1. Írja be a [konfigurációs fájlt](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config. JSON**).

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```shell
    jupyter notebook
    ```