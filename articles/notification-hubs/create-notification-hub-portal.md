---
title: Hozzon létre egy Azure-értesítési központot az Azure Portalhasználatával | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Azure-értesítési központot az Azure Portal használatával.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 3aeeb989d15dc74849c85fa58cbefa891809f3c5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347092"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure-értesítési központot az Azure Portalon 
Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatásról további információt a [Mi az Azure Értesítési központ?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban hozzon létre egy értesítési központot az Azure Portalon. Az első szakasz lépéseket ad egy értesítési központ névtér és egy hub létrehozásához a névtérben. A második szakasz lépéseket ad egy értesítési központ létrehozásához egy meglévő értesítési központ névterében. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Névtér és értesítési központ létrehozása
Ebben a szakaszban létrehoz egy névteret és egy központot a névtérben. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Értesítési központ létrehozása meglévő névtérben
Ebben a szakaszban egy meglévő névtérben hoz létre értesítési központot. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** keresse meg az **Értesítési központot**, válassza a **csillag** (`*`) lehetőséget az **Értesítési központ névtere** i elemmellett, és adja hozzá a bal oldali menü **KEDVENCEK** szakaszához. Válassza az **Értesítési központ névtereit**. 

      ![Azure portal – válassza az Értesítési központ névtereit](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Az **Értesítési központ névterezéselapon** válassza ki a névteret a listából. 

      ![A névtér kiválasztása a listából](./media/create-notification-hub-portal/select-namespace.png)
1. Az **Értesítési központ névtere** lapján válassza a **Hub hozzáadása** lehetőséget az eszköztáron. 

      ![Értesítési központ névterei – Hub hozzáadása gomb](./media/create-notification-hub-portal/add-hub-button.png)
4. Az **Új értesítési központ** lapon adja meg az értesítési központ nevét, és kattintson az **OK gombra.**

      ![Új értesítési központ lap -> adja meg a hub nevét](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Válassza **az Értesítések** (Csengő ikon) lehetőséget a tetején az új hub üzembe helyezésének állapotának megtekintéséhez. Az értesítési ablak bezárásához válassza az **X** lehetőséget a jobb sarokban. 

      ![Telepítési értesítés](./media/create-notification-hub-portal/deployment-notification.png)
5. Frissítse az **Értesítési központ névtereit** weblapot, hogy az új központ megjelenjen a listában. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Válassza ki az **értesítési központot** az értesítési központ kezdőlapjának megtekintéséhez. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott egy értesítési központot. A hub platformértesítési rendszer (PNS) beállításaival való konfigurálásáról az [Értesítési központ konfigurálása PNS-beállításokkal](configure-notification-hub-portal-pns-settings.md)című témakörben olvashat. 