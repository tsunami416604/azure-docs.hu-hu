---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67178808"
---
### <a name="open-the-publish-wizard"></a>A közzétételi varázsló megnyitása

A **megoldáskezelő**kattintson a jobb gombbal a **SharingService** projektre, és válassza a **Közzététel**lehetőséget.

Elindul a közzétételi varázsló. Válassza ki **app Service** > **Közzététel** elemet a **app Service létrehozása** párbeszédpanel megnyitásához.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A **app Service létrehozása** párbeszédpanelen válassza a **fiók hozzáadása** lehetőséget, és jelentkezzen be az Azure-előfizetésbe. Ha már bejelentkezett, válassza ki a kívánt fiókot a legördülő listából.

> [!NOTE]
> Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
>

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [resource group intro text](resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el a **myResourceGroup** erőforráscsoportot, majd kattintson az **OK** gombra.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet.

A **üzemeltetési terv konfigurálása** párbeszédpanelen használja a következő beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service-csomag neve. |
| Hely | USA nyugati régiója | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | Az üzemeltetési funkciókat meghatározó [díjszabási](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) csomag. |

Kattintson az **OK** gombra.

### <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Az **alkalmazás neve**mezőben adjon meg egy egyedi nevet (érvényes karakterek `a-z` `0-9`:, és `-`), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `https://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

A varázsló befejeződése után közzéteszi a ASP.NET Core webalkalmazást az Azure-ban, majd megnyitja az alkalmazást az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/spatial-anchors-azure/web-app-running-live.png)

Az ebben a szakaszban használt alkalmazás neve URL-előtagként van megadva a formátumban `https://<app_name>.azurewebsites.net`. Jegyezze fel ezt az URL-címet, mert szüksége lesz rá.
