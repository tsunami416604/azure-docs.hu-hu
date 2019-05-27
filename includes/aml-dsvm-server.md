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
ms.openlocfilehash: 02ef0d6c7c8ddc7088938d9c8ea379e3b97f3045
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158724"
---
1. [Hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](../articles/machine-learning/service/setup-create-workspace.md).

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Munkaterület konfigurációs fájl hozzáadása a klónozott könyvtárra, az alábbi módszerek egyikével:

    * Az a [az Azure portal](https://ms.portal.azure.com)válassza **config.json letöltése** a a **áttekintése** szakasz a munkaterület. 

    ![Töltse le a config.json](./media/aml-dsvm-server/download-config.png)

    * Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) jegyzetfüzetet a klónozott könyvtárra.


1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.
    
    ```shell
    jupyter notebook
    ```