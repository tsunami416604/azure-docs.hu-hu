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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391780"
---
1. Kövesse az utasításokat, [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](../articles/machine-learning/service/setup-create-workspace.md#portal) , tegye a következőket:
    * A Miniconda-környezet létrehozása
    * Telepítse az Azure Machine Learning SDK a Pythonhoz
    * Munkaterület létrehozása
    * Munkaterület konfigurációs fájl írása (**aml_config/config.json**).

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```shell
    jupyter notebook
    ```