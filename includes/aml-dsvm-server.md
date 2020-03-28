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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486033"
---
1. [Hozzon létre egy Azure Machine Learning-munkaterületet.](../articles/machine-learning/how-to-manage-workspace.md)

1. Klónozza [a GitHub-tárházat.](https://aka.ms/aml-notebooks)

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Vegyen fel egy munkaterület-konfigurációs fájlt a klónozott könyvtárba az alábbi módszerek egyikével:

    * Az [Azure Portalon](https://ms.portal.azure.com)válassza **a Config.json letöltése lehetőséget** a munkaterület **Áttekintés szakaszában.** 

    ![Config.json letöltése](./media/aml-dsvm-server/download-config.png)

    * Hozzon létre egy új munkaterületet a klónozott könyvtárban lévő [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) jegyzetfüzetben lévő kód használatával.

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```bash
    jupyter notebook
    ```