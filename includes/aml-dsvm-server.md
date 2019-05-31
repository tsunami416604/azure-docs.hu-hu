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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391792"
---
1. [Hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](../articles/machine-learning/service/setup-create-workspace.md).

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```CLI
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