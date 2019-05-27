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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123232"
---
1. Kövesse az utasításokat, [hozzon létre egy Azure Machine Learning szolgáltatás munkaterületet](../articles/machine-learning/service/setup-create-workspace.md#portal) való:
    * A Miniconda-környezet létrehozása
    * Telepítse az Azure Machine Learning SDK a Pythonhoz
    * Munkaterület létrehozása
    * Munkaterület konfigurációs fájl írása (**aml_config/config.json**).
    
1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Ezen módszerek bármelyikével munkaterület konfigurációs fájl hozzáadása:
    * Másolás a **aml_config/config.json** a klónozott könyvtárra 1. lépésben létrehozott fájlt.

    * Az a [az Azure portal](https://ms.portal.azure.com)válassza **config.json letöltése** a a **áttekintése** szakasz a munkaterület. 

    ![Töltse le a config.json](./media/aml-dsvm-server/download-config.png)

    * Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.
    
    ```shell
    jupyter notebook
    ```