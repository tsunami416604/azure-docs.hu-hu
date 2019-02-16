---
title: Az Azure Portalon az Azure notification hub létrehozása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan hozhat létre az Azure notification hub az Azure portal használatával.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 62e72f27e48f7bf220901f4eb36090f926724a2a
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314118"
---
# <a name="create-an-azure-notification-hub-in-the-azure-portal"></a>Az Azure notification hub létrehozása az Azure Portalon 
Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

Ebben a rövid útmutatóban létrehozhat egy értesítési központ, az Azure Portalon. Az első szakasz lehetővé teszi a Notification Hubs-névtér és a egy eseményközpont a névtéren létrehozásához szükséges lépéseket. A második szakasz lehetővé teszi egy meglévő a Notification Hubs-névtér egy értesítési központ létrehozásának lépéseit. 

## <a name="create-a-namespace-and-a-notification-hub"></a>A névtér és a egy értesítési központ létrehozása
Ebben a szakaszban hoz létre egy névteret és a egy hub névtér. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Egy meglévő névtér vagy az értesítési központ létrehozása
Ebben a szakaszban egy meglévő névtér vagy az értesítési központ létrehozása. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüjében keressen **értesítési központ**válassza **csillag** (`*`) melletti **értesítési központok névterei** választásával adja azt hozzá a **Kedvencek** szakaszban a bal oldali menüben. Válassza ki **értesítési központok névterei**. 

      ![Az Azure portal - válassza értesítési központok névterei](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Az a **értesítési központok névterei** lapra, jelölje be a névtér a listából. 

      ![Válassza ki a névteret a listából](./media/create-notification-hub-portal/select-namespace.png)
1. Az a **Notification Hub Namespace** lapon jelölje be **hozzáadása Hub** az eszköztáron. 

      ![Értesítési központok névterei - hozzáadása Hub gomb](./media/create-notification-hub-portal/add-hub-button.png)
4. Az a **új értesítési központ** lapon adja meg az értesítési központ nevét, és válassza ki **OK**.

      ![Új értesítési központ lap -> adja meg a hub nevét](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Válassza ki **értesítések** (Harang ikon) a legfelül látható az új központi telepítésének állapota. Válassza ki **X** a jobb sarokban az értesítési ablak bezárásához. 

      ![Központi telepítési értesítés](./media/create-notification-hub-portal/deployment-notification.png)
5. Frissítse a **értesítési központok névterei** webes oldalon tekintheti meg az új hub, a listában. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Válassza ki a **értesítési központ** , tekintse meg az értesítési központ kezdőlapján. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott egy értesítési központ. A hub konfigurálása a platform notification system (PNS) beállításokkal kapcsolatban lásd: [hozzon létre egy értesítési központ PNS beállításokkal](configure-notification-hub-portal-pns-settings.md). 