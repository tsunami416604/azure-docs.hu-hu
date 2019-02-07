---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823206"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben, és válassza a **Notification Hubs** a a **Mobile** szakaszban. Válassza ki a csillag (`*`), adja hozzá a a szolgáltatás neve mellett a **Kedvencek** szakaszban a bal oldali menüben. Miután **Notification Hubs** adnak hozzá **Kedvencek**, jelölje ki azt a bal oldali menüben. 

      ![Az Azure portal – jelölje be a Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Az a **Notification Hubs** lapon jelölje be **Hozzáadás** az eszköztáron. 

      ![Adja hozzá a Notification Hubs - eszköztárgomb](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Az a **értesítési központ** lapon, tegye a következőket: 
    1. Adjon meg egy **neve** az értesítés **hub**.  
    2. Adjon meg egy **neve** számára a **névtér**.
    3. Válassza ki a **hely** , amelyre szeretné az értesítési központ létrehozása. 
    4. Válasszon ki egy meglévő erőforráscsoportot, vagy adjon meg egy nevet az új **erőforráscsoport**.
    5. Kattintson a **Létrehozás** gombra. 

        ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Válassza ki **értesítések** (Harang ikon), és válassza ki **erőforrás megnyitása**, vagy frissítse a listát a **Notification Hubs** lapon, és válassza ki az értesítési központ. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati sztringet. Ezekre később szükség lesz a leküldéses értesítések kezeléséhez.

      >[!IMPORTANT]
      >**NE** használja a DefaultFullSharedAccessSignature elemet az alkalmazásban. Ezt csak a háttérben használhatja.
      >

      ![Azure Portal – értesítési központ kapcsolati sztringjei](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
