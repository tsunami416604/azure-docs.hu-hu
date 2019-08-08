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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857397"
---
1. [Hozzon létre egy Azure Machine learning szolgáltatás](../articles/machine-learning/service/how-to-manage-workspace.md)munkaterületet.

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Vegyen fel egy munkaterület-konfigurációs fájlt a klónozott könyvtárba a következő módszerek bármelyikével:

    * A [Azure Portal](https://ms.portal.azure.com)a munkaterület **Áttekintés** szakaszában válassza a **config. JSON letöltése** lehetőséget. 

    ![A config. JSON fájl letöltése](./media/aml-dsvm-server/download-config.png)

    * Hozzon létre egy új munkaterületet a Code ( [konfiguráció. ipynb)](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) jegyzetfüzetben a klónozott címtárban.

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```shell
    jupyter notebook
    ```