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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79486033"
---
1. [Hozzon létre egy Azure Machine learning munkaterületet](../articles/machine-learning/how-to-manage-workspace.md).

1. [A GitHub-tárház](https://aka.ms/aml-notebooks)klónozása.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Vegyen fel egy munkaterület-konfigurációs fájlt a klónozott könyvtárba a következő módszerek bármelyikével:

    * A [Azure Portal](https://ms.portal.azure.com)a munkaterület **Áttekintés** szakaszában válassza a **config. JSON letöltése** lehetőséget. 

    ![A config. JSON fájl letöltése](./media/aml-dsvm-server/download-config.png)

    * Hozzon létre egy új munkaterületet a Code ( [konfiguráció. ipynb)](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) jegyzetfüzetben a klónozott címtárban.

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```bash
    jupyter notebook
    ```