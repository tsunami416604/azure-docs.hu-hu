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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302209"
---
1. Végezze el a [Azure Machine Learning Python rövid](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) munkaterület létrehozásához.  Nyugodtan hagyja ki a **a notebook használata** szakaszt, ha szeretné.
1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Ezen módszerek bármelyikével munkaterület konfigurációs fájl hozzáadása:
    * Másolás a **aml_config\config.json** a klónozott könyvtárba az előfeltételként szükséges rövid útmutató segítségével létrehozott fájlt.
    * Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) jegyzetfüzetet a klónozott könyvtárra.
1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.
    
    ```shell
    jupyter notebook
    ```