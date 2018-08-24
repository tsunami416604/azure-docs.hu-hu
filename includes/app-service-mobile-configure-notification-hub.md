---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0a74079677a084b2d4e8221cf5a74b356126811d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817438"
---
Az Azure App Service Mobile Apps funkcióját használja [Azure Notification Hubs] küldhet leküldéses értesítést, kíván beállítani egy értesítési központ számára.

1. Az a az [Azure Portal], lépjen a **App Services**, majd válassza ki az alkalmazás háttérrendszere. A **beállítások**válassza **leküldéses**.
2. Egy értesítésiközpont-erőforrás hozzáadása az alkalmazáshoz, jelölje be **Connect**. Eseményközpont létrehozásához, vagy egy meglévő csatlakozni.

    ![A központ konfigurálása](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Egy értesítési központ már csatlakoztatta a Mobile Apps háttér-projekthez. Később konfigurálja az értesítési központ egy platform notification system (PNS) leküldése eszközökre való kapcsolódáshoz.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
