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
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929648"
---
Egy folyamat egy számítási célra fut, amely a munkaterülethez csatolt számítási erőforrás. A számítási cél létrehozása után újból felhasználhatja azt későbbi futtatásokhoz.

1. Válassza a **Futtatás** lehetőséget a vászon tetején a folyamat futtatásához.

1. Amikor megjelenik a **Beállítások** ablaktábla, válassza a **számítási cél kiválasztása**lehetőséget.

    Ha már van elérhető számítási cél, akkor kiválaszthatja a folyamat futtatásához.

    > [!NOTE]
    > A tervező csak Azure Machine Learning számítási célokon futtathat kísérleteket. Más számítási célok nem jelennek meg.

1. Adja meg a számítási erőforrás nevét.

1. Kattintson a **Mentés** gombra.

    ![Számítási cél beállítása](./media/aml-ui-create-training-compute/set-compute.png)

1. Válassza a **Futtatás** lehetőséget.

1. A **folyamat futtatásának beállítása** párbeszédpanelen válassza az **+ új kísérlet** a **kísérlethez**lehetőséget.

    > [!NOTE]
    > A kísérletek csoport hasonló folyamata együtt fut. Ha többször is futtat egy folyamatot, kiválaszthatja ugyanazt a kísérletet egymást követő futtatásokhoz.

    1. Adjon meg egy leíró nevet a **kísérlet neveként**.

    1. Válassza a **Futtatás** lehetőséget.
    
    A futtatási állapot és a részletek a vászon jobb felső sarkában tekinthetők meg.

    > [!NOTE]
    > Számítási erőforrás létrehozása körülbelül öt percet vesz igénybe. Az erőforrás létrehozása után újra felhasználhatja azt, és kihagyhatja ezt a várakozási időt a jövőbeli futtatásokhoz.
    >
    > A számítási erőforrás kiszámítja a nulla csomópontot, ha a költségeket nem lehet megtakarítani. Ha a késést követően ismét felhasználja, előfordulhat, hogy a várakozási idő körülbelül öt percet vesz igénybe.
