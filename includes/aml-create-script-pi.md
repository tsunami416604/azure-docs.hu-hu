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
ms.openlocfilehash: e86f2cc9fd2da585ad13b088442ebef9b55f186a
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431183"
---
Hozzon létre egy szkriptet **pi.py** néven ugyanabban a könyvtárban, ahol az **aml_config/config.json** található.

Másolja az alábbi kódot ebbe a szkriptbe:
    
   ```python
   import random, math
   from azureml.core import Run
    
   run = Run.get_submitted_run()
    
   pi_counter = 0
   n_iter = 100000
   run.log("Number of iterations",n_iter)
    
   for i in range(1,n_iter):
       x = random.random()
       y = random.random()
       if x*x + y*y < 1.0:
           pi_counter += 1
       pi_value = 4.0*pi_counter / i
       if i%10000==0:
           error = math.pi-pi_value
           print(i, pi_value, error)
           run.log_row("Pi estimate",iteration=i,pi_value=pi_value)
           run.log_row("Error",iteration=i,error=error)
   ```

Figyelje meg a `log_row` hívásokat a végén.  A szkript futtatása ezeket az értékeket fogja látni a munkaterületén.