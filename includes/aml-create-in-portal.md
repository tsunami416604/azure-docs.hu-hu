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
ms.openlocfilehash: 6f73b15ed16cfe26bf14e60a5206568e1a1564fd
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53594241"
---
Jelentkezzen be a [az Azure portal](https://portal.azure.com/) használhatja az Azure-előfizetés hitelesítő adataival. 

Munkaterület irányítópultján a portálon csak Edge, Chrome és a Firefox böngésző esetében támogatott.

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

A portál bal felső sarkában válassza **erőforrás létrehozása**.

   ![Erőforrás létrehozása az Azure Portalon](./media/aml-create-in-portal/portal-create-a-resource.png)

A keresősávba írja be a **Machine Learning**. Válassza ki a **Machine Learning szolgáltatás munkaterület** keresési eredmény.

   ![Keresse meg a munkaterület](./media/aml-create-in-portal/allservices-search.PNG)

Az a **ML-munkaterület szolgáltatási** panelen görgessen lefelé, és válassza **létrehozás** megkezdéséhez.

   ![Létrehozás](./media/aml-create-in-portal/portal-create-button.png)

Az a **ML-munkaterület szolgáltatási** panelen konfigurálja a munkaterülethez.

   Mező|Leírás
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterülethez. Ebben a példában használjuk **docs-ws**. Nevének egyedinek kell lennie az erőforráscsoport. Olyan nevet, amely könnyen visszahívása és mások által létrehozott munkaterületek megkülönböztessük használja.  
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésében, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ebben a példában használjuk **docs-aml**. 
   Hely | Válassza ki a felhasználók és az adatforrásokhoz legközelebb eső helyet. Ez a hely, ahol a munkaterület létrehozását.

   ![Munkaterület létrehozása](./media/aml-create-in-portal/workspace-create.png)

A létrehozási folyamat indításához válassza **létrehozás**. A munkaterület létrehozása egy kis ideig is eltarthat.

Az üzembe helyezés állapotának ellenőrzéséhez válassza az értesítések ikonjára **harang**, az eszköztáron.

   ![Munkaterület létrehozási állapota](./media/aml-create-in-portal/notifications.png)

A folyamat befejeződött, a telepítés sikeres üzenet jelenik meg. Is megtalálható az értesítések szakaszban. Az új munkaterület megtekintéséhez jelölje ki **erőforrás megnyitása**.
