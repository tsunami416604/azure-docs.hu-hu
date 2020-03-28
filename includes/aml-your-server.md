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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485988"
---
1. Az Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) utasításainak használata az Azure Machine Learning SDK Pythonhoz történő telepítéséhez

1. Hozzon létre egy [Azure Machine Learning-munkaterületet.](../articles/machine-learning/how-to-manage-workspace.md)

1. [Konfigurációs fájl írása](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Klónozza [a GitHub-tárházat.](https://aka.ms/aml-notebooks)

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```bash
    jupyter notebook
    ```