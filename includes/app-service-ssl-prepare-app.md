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
ms.openlocfilehash: ecdd419331c88e712644851f9213861f882cf0f6
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53381517"
---
## <a name="prepare-your-web-app"></a>A webalkalmazás előkészítése

Ha egy egyéni SSL-tanúsítványt (külső tanúsítványt vagy App Service-tanúsítványt) szeretne a webalkalmazáshoz kötni, az [App Service-csomagnak](https://azure.microsoft.com/pricing/details/app-service/) az **Alapszintű**, **Standard**, **Prémium** vagy **Izolált** szinten kell lennie. Ebben a lépésben ellenőrzi, hogy a webalkalmazás a támogatott tarifacsomagban van-e.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Nyissa meg az [Azure Portalt](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Keresse meg a webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd a webalkalmazás nevére.

![Webalkalmazás kiválasztása](./media/app-service-ssl-prepare-app/select-app.png)

A webalkalmazás felügyeleti lapjára jut.  

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

A webalkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszhoz, és válassza a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Ellenőrizze, hogy a webalkalmazás nem az **F1** vagy a **D1** szinten van-e. A webalkalmazás aktuális tarifacsomagja sötétkék kerettel van kiemelve.

![A tarifacsomag ellenőrzése](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Az egyéni SSL nem támogatott az **F1** és a **D1** szinten. Ha vertikális felskálázásra van szüksége, kövesse az alábbi szakaszban található lépéseket. Egyébként zárja be a **vertikális felskálázás** lapon, majd ugorjon a [az App Service-csomag vertikális felskálázása](#scale-up-your-app-service-plan) szakaszban.

### <a name="scale-up-your-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![Tarifacsomag kiválasztása](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Vertikális felskálázási értesítés](./media/app-service-ssl-prepare-app/scale-notification.png)

