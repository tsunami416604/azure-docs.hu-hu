---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179622"
---
Az Azure App Service Mobile Apps funkcióját használja [Az Azure Notification hubs használatával] küldhet leküldéses értesítést, kíván beállítani egy értesítési központ számára.

1. Az a [Azure Portal], lépjen a **App Services**, majd válassza ki az alkalmazás háttérrendszere. A **beállítások**válassza **leküldéses**.
2. Egy értesítésiközpont-erőforrás hozzáadása az alkalmazáshoz, jelölje be **Connect**. Eseményközpont létrehozásához, vagy egy meglévő csatlakozni.

    ![A központ konfigurálása](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Egy értesítési központ már csatlakoztatta a Mobile Apps háttér-projekthez. Később konfigurálja az értesítési központ egy platform notification system (PNS) leküldése eszközökre való kapcsolódáshoz.

[Azure Portal]: https://portal.azure.com/
[Az Azure Notification hubs használatával]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
