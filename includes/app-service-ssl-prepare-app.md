---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4c5c0197ab6cecbba4bac4c0bff5ef76de24b6de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78301002"
---
## <a name="prepare-your-web-app"></a>A webalkalmazás előkészítése

Egyéni biztonsági kötések létrehozásához vagy az App Service alkalmazáshoz tartozó Ügyféltanúsítványok engedélyezéséhez a [app Service tervének](https://azure.microsoft.com/pricing/details/app-service/) **alapszintű**, **standard**, **prémium**vagy **elszigetelt** szinten kell lennie. Ebben a lépésben ellenőrzi, hogy a webalkalmazás a támogatott tarifacsomagban van-e.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a [Azure Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Keresse meg a webalkalmazást.

Keresse meg és válassza ki a **app Services**.

![App Services kiválasztása](./media/app-service-ssl-prepare-app/app-services.png)

A **app Services** lapon válassza ki a webalkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-ssl-prepare-app/select-app.png)

A webalkalmazás felügyeleti lapján található.  

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

A webalkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszhoz, és válassza a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Ellenőrizze, hogy a webalkalmazás nem az **F1** vagy a **D1** szinten van-e. A webalkalmazás aktuális tarifacsomagja sötétkék kerettel van kiemelve.

![A tarifacsomag ellenőrzése](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Az egyéni SSL nem támogatott az **F1** és a **D1** szinten. Ha vertikális felskálázásra van szüksége, kövesse az alábbi szakaszban található lépéseket. Ellenkező esetben zárjuk be a vertikális **felskálázás** lapot, és hagyja [ki a app Service-csomag vertikális felskálázása](#scale-up-your-app-service-plan) szakaszt.

### <a name="scale-up-your-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![Tarifacsomag kiválasztása](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Vertikális felskálázási értesítés](./media/app-service-ssl-prepare-app/scale-notification.png)

