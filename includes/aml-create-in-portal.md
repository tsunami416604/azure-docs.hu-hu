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
ms.openlocfilehash: 2ec45b67367198c14fc9d03cdb659a51aed8a504
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841555"
---
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) használhatja az Azure-előfizetés hitelesítő adataival. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. A portál bal felső sarkában válassza **erőforrás létrehozása**.

   ![Erőforrás létrehozása az Azure Portalon](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Válassza ki a Keresősáv használatával **Machine Learning szolgáltatás munkaterület**.

   ![Keresse meg a munkaterület](./media/aml-create-in-portal/allservices-search.png)

1. Az a **ML-munkaterület szolgáltatási** ablaktáblán válassza előbb **létrehozás** megkezdéséhez.

    ![Gomb létrehozása](./media/aml-create-in-portal/portal-create-button.png)

1. Az a **ML-munkaterület szolgáltatási** panelen konfigurálja a munkaterülethez.

    ![Munkaterület létrehozása](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Mező|Leírás
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterülethez. Ebben a példában használjuk **docs-ws**. Nevének egyedinek kell lennie az erőforráscsoport. Olyan nevet, amely könnyen visszahívása és mások által létrehozott munkaterületek megkülönböztessük használja.  
   Subscription |Válassza ki a használni kívánt Azure-előfizetést.
   Resource group | Használjon egy meglévő erőforráscsoportot az előfizetésében, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ebben a példában használjuk **docs-aml**. 
   Location | Válassza ki a felhasználók és az adatforrásokhoz legközelebb eső helyet. Ez a hely, ahol a munkaterület létrehozását.

1. Tekintse át a munkaterület-konfiguráció, és válassza ki **létrehozás**. A munkaterület létrehozása egy kis ideig is eltarthat.

1. A folyamat befejeződött, a telepítés sikeres üzenet jelenik meg. Is megtalálható az értesítések szakaszban. Az új munkaterület megtekintéséhez jelölje ki **erőforrás megnyitása**.

   ![Munkaterület létrehozási állapota](./media/aml-create-in-portal/notifications.png)
