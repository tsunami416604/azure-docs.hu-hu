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
ms.date: 05/21/2019
ms.openlocfilehash: 72f23b10047928f32886d9054f4dd1abdc569bd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66396931"
---
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) használhatja az Azure-előfizetés hitelesítő adataival. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. A portál bal felső sarkában válassza **erőforrás létrehozása**.

   ![Erőforrás létrehozása az Azure Portalon](./media/aml-create-in-portal/portal-create-a-resource-05-2019.png)

1. A keresősávba írja be a **Machine Learning**. Válassza ki a **Machine Learning szolgáltatás munkaterület** keresési eredmény.

   ![Keresse meg a munkaterület](./media/aml-create-in-portal/allservices-search-05-2019.png)

1. Az a **ML-munkaterület szolgáltatási** ablaktáblán válassza előbb **létrehozás** megkezdéséhez.

    ![Gomb létrehozása](./media/aml-create-in-portal/portal-create-button-05-2019.png)

1. Az a **ML-munkaterület szolgáltatási** panelen konfigurálja a munkaterülethez.

   Mező|Leírás
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterülethez. Ebben a példában használjuk **docs-ws**. Nevének egyedinek kell lennie az erőforráscsoport. Olyan nevet, amely könnyen visszahívása és mások által létrehozott munkaterületek megkülönböztessük használja.  
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésében, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ebben a példában használjuk **docs-aml**. 
   Location egység | Válassza ki a felhasználók és az adatforrásokhoz legközelebb eső helyet. Ez a hely, ahol a munkaterület létrehozását.

   ![Munkaterület létrehozása](./media/aml-create-in-portal/workspace-create-main-tab-05-2019.png)

1. A létrehozási folyamat indításához válassza **felülvizsgálat + létrehozás**.

    ![Hozzon létre](./media/aml-create-in-portal/workspace-create-main-review-button-05-2019.png)

1. Tekintse át a munkaterület-konfiguráció. Ha helyesek az adatok, válassza ki a **létrehozás**. A munkaterület létrehozása egy kis ideig is eltarthat.

    ![Hozzon létre](./media/aml-create-in-portal/workspace-create-review-tab-05-2019.png)

1. Az üzembe helyezés állapotának ellenőrzéséhez válassza az értesítések ikonjára **harang**, az eszköztáron.

1. A folyamat befejeződött, a telepítés sikeres üzenet jelenik meg. Is megtalálható az értesítések szakaszban. Az új munkaterület megtekintéséhez jelölje ki **erőforrás megnyitása**.

   ![Munkaterület létrehozási állapota](./media/aml-create-in-portal/notifications-05-2019.png)
