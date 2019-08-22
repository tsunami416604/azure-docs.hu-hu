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
ms.date: 05/06/2019
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891625"
---
Egy kísérlet számítási célra, a munkaterülethez csatolt számítási erőforrásra fut.  A számítási cél létrehozása után újból felhasználhatja azt későbbi futtatásokhoz.

1. A kísérlet futtatásához kattintson a lenti **Futtatás** gombra.

1. Ha megjelenik a **számítási célok beállítása** párbeszédpanel, ha a munkaterületen már van egy számítási erőforrás, kiválaszthatja most.  Ellenkező esetben válassza az **új létrehozása**lehetőséget.

    > [!NOTE]
    > A vizuális felület csak Machine Learning Compute-célokon futtathat kísérleteket. A többi számítási cél nem jelenik meg.

1. Adja meg a számítási erőforrás nevét.

1. Válassza a **Futtatás** lehetőséget.

    ![Számítási cél beállítása](./media/aml-ui-create-training-compute/set-compute.png)

    Ekkor létrejön a számítási erőforrás. Tekintse meg az állapotot a kísérlet jobb felső sarkában. 

    > [!NOTE]
    > Számítási erőforrás létrehozásához körülbelül 5 percet vesz igénybe. Az erőforrás létrehozása után újra felhasználhatja azt, és kihagyhatja ezt a várakozási időt a jövőbeli futtatásokhoz.
    >
    > A számítási erőforrás automatikusan átméretezi a 0 csomópontot, amikor az üresjáratban van, hogy megőrizze a költségeket.  Ha a késést követően ismét felhasználja, előfordulhat, hogy a várakozási idő körülbelül 5 percet vesz igénybe.
