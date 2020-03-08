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
ms.date: 03/05/2020
ms.openlocfilehash: e289cf7aea6e0ea46ff049f3ea8bf9e1517e8aaf
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673648"
---
1. [Hozzon létre egy Azure Machine learning munkaterületet](../articles/machine-learning/how-to-manage-workspace.md).

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