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
ms.date: 07/27/2018
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431083"
---
Hozza létre vagy aktiválja a Conda csomagkezelő környezetet NumPyval és Cythonnal egy parancssorban vagy felületi ablakban. Ez a példa a Python 3.6-ot használja.

  + Windows rendszeren:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + Linux vagy MacOS rendszeren:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

Telepítse az SDK-t.
   ```sh 
   pip install azureml-sdk
   ```
