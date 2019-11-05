---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: f7cdfb8b9edbce5ef2b094cf6a603904d39a7404
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493570"
---
Egy folyamat egy számítási célra fut, amely a munkaterülethez csatolt számítási erőforrás. A számítási cél létrehozása után újból felhasználhatja azt későbbi futtatásokhoz.

1. Válassza a **Futtatás** lehetőséget a vászon tetején a folyamat futtatásához.

1. Amikor megjelenik a **Beállítások** ablaktábla, válassza a **számítási cél kiválasztása**lehetőséget.

    Ha már van elérhető számítási cél, akkor kiválaszthatja a folyamat futtatásához.

    > [!NOTE]
    > A tervező csak Machine Learning Compute célokon futtathat kísérleteket. A többi számítási cél nem jelenik meg.

1. Adja meg a számítási erőforrás nevét.

1. Kattintson a **Mentés** gombra.

    ![Számítási cél beállítása](./media/aml-ui-create-training-compute/set-compute.png)

1. Válassza a **Futtatás** lehetőséget.

1. A **folyamat futtatásának beállítása** párbeszédpanelen válassza az **+ új kísérlet** a **kísérlethez** lehetőséget.

    > [!NOTE]
    > A kísérletek csoport hasonló folyamata együtt fut. Ha többször is futtat egy folyamatot, kiválaszthatja ugyanazt a kísérletet egymást követő futtatásokhoz.

    * Adjon meg egy leíró **kísérlet nevét**

    * **Futtatás** kiválasztása
    
    A futtatási állapot és a részletek a vászon jobb felső sarkában tekinthetők meg.

    > [!NOTE]
    > Számítási erőforrás létrehozásához körülbelül 5 percet vesz igénybe. Az erőforrás létrehozása után újra felhasználhatja azt, és kihagyhatja ezt a várakozási időt a jövőbeli futtatásokhoz.
    >
    > A számítási erőforrás automatikusan átméretezi a 0 csomópontot, amikor az üresjáratban van, hogy megőrizze a költségeket.  Ha a késést követően ismét felhasználja, előfordulhat, hogy a várakozási idő körülbelül 5 percet vesz igénybe.
