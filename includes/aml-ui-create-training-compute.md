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
ms.openlocfilehash: 3f969fb346ce341e694458dcc38bf193f012226b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792655"
---
Egy folyamat egy számítási célra fut, amely a munkaterülethez csatolt számítási erőforrás. A számítási cél létrehozása után újból felhasználhatja azt későbbi futtatásokhoz.

1. Válassza a **Futtatás** lehetőséget a vászon tetején a folyamat futtatásához.

1. Amikor megjelenik a **Beállítások** ablaktábla, válassza a **számítási cél kiválasztása**lehetőséget.

    Ha már van elérhető számítási cél, akkor kiválaszthatja a folyamat futtatásához.

    > [!NOTE]
    > A vizuális felület csak Machine Learning Compute-célokon futtathat kísérleteket. A többi számítási cél nem jelenik meg.

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
