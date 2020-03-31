---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179622"
---
Az Azure App Service Mobilalkalmazások funkciója [az Azure Értesítési központokat] használja leküldéses küldése, így egy értesítési központot fog konfigurálni a mobilalkalmazáshoz.

1. Az [Azure Portalon]nyissa meg az **App Services**, és válassza ki az alkalmazás háttér. A **Beállítások csoportban**válassza a **Leküldéses**lehetőséget.
2. Ha értesítési központ-erőforrást szeretne hozzáadni az alkalmazáshoz, válassza a **Csatlakozás lehetőséget.** Létrehozhat egy elosztót, vagy csatlakozhat egy meglévőhez.

    ![Hub konfigurálása](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Most már csatlakoztatta az értesítési központot a Mobilalkalmazások háttérprojektjéhez. Később úgy konfigurálja ezt az értesítési központot, hogy egy platformértesítési rendszerhez (PNS) csatlakozzon az eszközökre való leküldéses hez.

[Azure-portál]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
