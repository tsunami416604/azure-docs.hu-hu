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
ms.openlocfilehash: 05331c710817e575deb7729189c9b2d8ccbafd7d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54489567"
---
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) használhatja az Azure-előfizetés hitelesítő adataival. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

1. A portál bal felső sarkában válassza **erőforrás létrehozása**.

   ![Erőforrás létrehozása az Azure Portalon](./media/aml-create-in-portal/portal-create-a-resource.png)

1. A keresősávba írja be a **Machine Learning**. Válassza ki a **Machine Learning szolgáltatás munkaterület** keresési eredmény.

   ![Keresse meg a munkaterület](./media/aml-create-in-portal/allservices-search.PNG)

1. Az a **ML-munkaterület szolgáltatási** panelen görgessen lefelé, és válassza **létrehozás** megkezdéséhez.

   ![Létrehozás](./media/aml-create-in-portal/portal-create-button.png)

1. Az a **ML-munkaterület szolgáltatási** panelen konfigurálja a munkaterülethez.

   Mező|Leírás
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterülethez. Ebben a példában használjuk **docs-ws**. Nevének egyedinek kell lennie az erőforráscsoport. Olyan nevet, amely könnyen visszahívása és mások által létrehozott munkaterületek megkülönböztessük használja.  
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésében, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ebben a példában használjuk **docs-aml**. 
   Hely | Válassza ki a felhasználók és az adatforrásokhoz legközelebb eső helyet. Ez a hely, ahol a munkaterület létrehozását.

   ![Munkaterület létrehozása](./media/aml-create-in-portal/workspace-create.png)

1. A létrehozási folyamat indításához válassza **létrehozás**. A munkaterület létrehozása egy kis ideig is eltarthat.

1. Az üzembe helyezés állapotának ellenőrzéséhez válassza az értesítések ikonjára **harang**, az eszköztáron.

1. A folyamat befejeződött, a telepítés sikeres üzenet jelenik meg. Is megtalálható az értesítések szakaszban. Az új munkaterület megtekintéséhez jelölje ki **erőforrás megnyitása**.

   ![Munkaterület létrehozási állapota](./media/aml-create-in-portal/notifications.png)
