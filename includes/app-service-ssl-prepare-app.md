---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4c5c0197ab6cecbba4bac4c0bff5ef76de24b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301002"
---
## <a name="prepare-your-web-app"></a>A webalkalmazás előkészítése

Egyéni biztonsági kötések létrehozásához vagy az App Service-alkalmazás ügyféltanúsítványok engedélyezéséhez az [App Service-csomagnak](https://azure.microsoft.com/pricing/details/app-service/) az **Alapszintű,** **Standard**, **Prémium**vagy **Elkülönített** szinten kell lennie. Ebben a lépésben ellenőrzi, hogy a webalkalmazás a támogatott tarifacsomagban van-e.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az [Azure Portalt](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Keresse meg a webalkalmazást.

Keresse meg és válassza **az App Services**lehetőséget.

![Alkalmazásszolgáltatások kiválasztása](./media/app-service-ssl-prepare-app/app-services.png)

Az **App Services** lapon válassza ki a webalkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-ssl-prepare-app/select-app.png)

A webalkalmazás felügyeleti oldalán landolt.  

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

A webalkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszhoz, és válassza a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Ellenőrizze, hogy a webalkalmazás nem az **F1** vagy a **D1** szinten van-e. A webalkalmazás aktuális tarifacsomagja sötétkék kerettel van kiemelve.

![A tarifacsomag ellenőrzése](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Az egyéni SSL nem támogatott az **F1** és a **D1** szinten. Ha vertikális felskálázásra van szüksége, kövesse az alábbi szakaszban található lépéseket. Ellenkező esetben zárja be a **Felskálázás** lapot, és hagyja ki az [App Service-csomag felskálázása](#scale-up-your-app-service-plan) szakaszt.

### <a name="scale-up-your-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![Tarifacsomag kiválasztása](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Vertikális felskálázási értesítés](./media/app-service-ssl-prepare-app/scale-notification.png)

