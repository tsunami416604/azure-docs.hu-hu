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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395667"
---
1. Kövesse az utasításokat, [hozzon létre egy Azure Machine Learning szolgáltatás munkaterületet](../articles/machine-learning/service/setup-create-workspace.md#portal) Miniconda környezet kialakításához, hozzon létre egy munkaterületet, és a munkaterület konfigurációs fájl írása (**aml_config/config.json**) .

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Ezen módszerek bármelyikével munkaterület konfigurációs fájl hozzáadása:
    * Másolás a **aml_config/config.json** a klónozott könyvtárba az előfeltételként szükséges rövid útmutató segítségével létrehozott fájlt.
    * Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.
    
    ```shell
    jupyter notebook
    ```