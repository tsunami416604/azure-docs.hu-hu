---
title: fájlbefoglalás
description: fájlbefoglalás
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558330"
---
1. A Pythonhoz készült Azure Machine Learning SDK telepítéséhez [Azure Machine learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py)  -ban található utasításokat használhatja.

1. Hozzon létre egy [Azure Machine learning munkaterületet](../articles/machine-learning/how-to-manage-workspace.md).

1. [Konfigurációs fájl](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.js**) írása.

1. [A GitHub-tárház](https://aka.ms/aml-notebooks)klónozása.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```bash
    jupyter notebook
    ```