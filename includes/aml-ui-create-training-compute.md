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
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028515"
---
Egy kísérletet futtat egy számítási célnak, egy olyan számítási erőforrásra, amely a munkaterülethez van csatolva.  Miután létrehozott egy számítási célnak, későbbi futtatások felhasználhatja azt.

1. Válassza ki **futtatása** alján futtathatja a kísérletet.

     ![Kísérlet futtatása](./media/aml-ui-create-training-compute/run-experiment.png)

1. Ha a **telepítő számítási céljainak** párbeszédpanel jelenik meg, ha a munkaterület már van egy olyan számítási erőforrásra, kiválaszthatja azt most.  Ellenkező esetben válassza **új létrehozása**.

    > [!NOTE]
    > A vizuális felhasználói felületet csak a Machine Learning Compute célok kísérletek futnak. Más számítási célnak nem fognak megjelenni.

1. Adjon meg egy nevet a számítási erőforrás.

1. Válassza a **Futtatás** lehetőséget.

    ![A telepítő számítási célnak](./media/aml-ui-create-training-compute/set-compute.png)

    Most jön létre a számítási erőforrás. A kísérlet jobb felső sarkában a állapotának megtekintéséhez. 

    > [!NOTE]
    > Számítási erőforrás létrehozása körülbelül 5 percet vesz igénybe. Az erőforrás létrehozása után újra felhasználhatja, és hagyja ki a későbbi futtatások várakozási idő.
    >
    > A számítási erőforrás automatikus skálázási 0 csomópontra lesz, ha tétlen csökkenthetők a költségek.  Használatakor ez később újra tapasztalhat várakozási idő körülbelül 5 perc átméreteződik biztonsági közben.
