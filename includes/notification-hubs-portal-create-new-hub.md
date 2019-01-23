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
ms.openlocfilehash: b68fa345d4772134c30ce8b8b559f98113a0496f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453097"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Erőforrás létrehozása** > **Mobil** > **Értesítési központ** elemet.

      ![Azure Portal – Értesítési központ létrehozása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)

3. Az **Értesítési központ** mezőbe írjon be egy egyedi nevet. Válassza ki a **Helyet**, **Előfizetést** és **Erőforráscsoportot** (ha már rendelkezik ilyennel).

      Ha még nem rendelkezik szolgáltatásbusz-névtérrel, használhatja a központ neve alapján létrehozott alapértelmezett nevet (ha a névtér neve elérhető).

      Ha már rendelkezik szolgáltatásbusz-névtérrel, amelyben létrehozná a központot, kövesse ezeket a lépéseket

    a. A **Névtér** területen válassza a **Meglévő használata** hivatkozást.

    b. Kattintson a **Létrehozás** gombra.

    ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Válassza az **Értesítések** (harang ikon), majd az **Erőforrás megnyitása** lehetőséget.

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)
5. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati sztringet. Ezekre később szükség lesz a leküldéses értesítések kezeléséhez.

      >[!IMPORTANT]
      >**NE** használja a DefaultFullSharedAccessSignature elemet az alkalmazásban. Ezt csak a háttérben használhatja.
      >

      ![Azure Portal – értesítési központ kapcsolati sztringjei](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
