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
ms.openlocfilehash: 244a4ebe20863945bfc3b6236e70e786387c8909
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116684"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** a bal oldali menüben, és válassza a **Notification Hubs** a a **Mobile** szakaszban. Válassza ki a szolgáltatás hozzáadása a szolgáltatás neve melletti csillag ikont a **Kedvencek** szakaszban a bal oldali menüben. Miután hozzáadta a **Notification Hubs** való **Kedvencek**, jelölje ki azt a bal oldali menüben.

      ![Az Azure portal – jelölje be a Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Az a **Notification Hubs** lapon jelölje be **Hozzáadás** az eszköztáron.

      ![Adja hozzá a Notification Hubs - eszköztárgomb](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Az a **értesítési központ** lapon, tegye a következőket:

    1. Adjon meg egy nevet a **értesítési központ**.  

    1. Adjon meg egy nevet a **hozzon létre egy új névteret**. A névtér egy vagy több hubs tartalmazza.

    1. Kiválaszthat egy értéket a **hely** legördülő listában. Ezt az értéket adja meg a helyet, ahol az értesítési központ létrehozása szeretné.

    1. Válasszon ki egy meglévő erőforráscsoportot az **erőforráscsoport**, vagy hozzon létre egy új erőforráscsoport nevét.

    1. Kattintson a **Létrehozás** gombra.

        ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Válassza ki **értesítések** (Harang ikon), és válassza ki **erőforrás megnyitása**. A lista frissítéséhez a a **Notification Hubs** lapon, és válassza az értesítési központban.

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati sztringet. Később szüksége lesz rájuk leküldéses értesítések kezeléséhez.

      >[!IMPORTANT]
      >Tegye *nem* használja a **DefaultFullSharedAccessSignature** szabályzatot az alkalmazásban. Az a csak a háttérbeli használandó jelenti.
      >

      ![Azure Portal – értesítési központ kapcsolati sztringjei](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
