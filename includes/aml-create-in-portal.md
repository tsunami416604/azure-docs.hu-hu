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
ms.date: 09/24/2018
ms.openlocfilehash: 6d6e6a2aea867c5b603d950a4bbaa33f14695f45
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010970"
---
Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a használni kívánt Azure-előfizetés hitelesítő adataival. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Munkaterület irányítópultján a portálon csak Edge, Chrome és a Firefox böngésző esetében támogatott.

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

   ![Erőforrás létrehozása az Azure Portalon](./media/aml-create-in-portal/portal-create-a-resource.png)

Írja be a **Machine Learning** kifejezést a keresősávba. Válassza ki a keresési eredmények nevű **Machine Learning-munkaterület**.

   ![Keresse meg a munkaterület](./media/aml-create-in-portal/allservices-search.PNG)

Az a **Machine Learning-munkaterület** panelen görgessen lefelé, és válassza **létrehozás** megkezdéséhez.

   ![létrehozás](./media/aml-create-in-portal/portal-create-button.png)

Az a **ML-munkaterület** panelen konfigurálja a munkaterülethez.

   Mező|Leírás
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterülethez.  Docs-ws itt fogjuk használni. Nevének egyedinek kell lennie az erőforráscsoport. Olyan nevet, amely könnyen visszahívása és a mások által létrehozott munkaterületek megkülönböztessük használja.  
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás terhelve van.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésében, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol.  Docs-aml itt fogjuk használni. 
   Hely | Válassza ki a felhasználókhoz és az adatforrásokhoz legközelebb eső helyet. Ez az, ahol a munkaterület létrehozását.

   ![Munkaterület létrehozása](./media/aml-create-in-portal/workspace-create.png)

A **Létrehozás** lehetőség kiválasztásával indíthatja el a létrehozás folyamatát.  A munkaterület létrehozása egy kis ideig is eltarthat.

   Az üzembe helyezés állapotának ellenőrzéséhez válassza az értesítések ikont (Csengő) elemre az eszköztárban.

   ![Munkaterület létrehozása](./media/aml-create-in-portal/notifications.png)

   Amikor végzett, a központi telepítés a sikert jelző üzenet jelenik meg.  Is megtalálható az értesítések szakaszban.   Kattintson a **erőforrás megnyitása** gombra kattintva megtekintheti az új munkaterületet.
