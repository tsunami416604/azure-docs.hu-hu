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
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79485988"
---
1. A Pythonhoz készült Azure Machine Learning SDK telepítéséhez [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) -ban található utasításokat használhatja.

1. Hozzon létre egy [Azure Machine learning munkaterületet](../articles/machine-learning/how-to-manage-workspace.md).

1. [Konfigurációs fájl](../articles/machine-learning/how-to-configure-environment.md#workspace) írása (**aml_config/config.JSON**).

1. [A GitHub-tárház](https://aka.ms/aml-notebooks)klónozása.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```bash
    jupyter notebook
    ```