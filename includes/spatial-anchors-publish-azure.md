---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67178808"
---
### <a name="open-the-publish-wizard"></a>A közzétételvarázsló megnyitása

A **Megoldáskezelőben**kattintson a jobb gombbal a **SharingService** projektre, és válassza **a Közzététel parancsot.**

Elindul a Közzététel varázsló. Az **App Service** > **Publish** lehetőséget választva nyissa meg az App **Service létrehozása** párbeszédpanelt.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az **App Service létrehozása** párbeszédpanelen válassza **a Fiók hozzáadása** lehetőséget, és jelentkezzen be az Azure-előfizetésbe. Ha már be van jelentkezve, válassza ki a kívánt fiókot a legördülő listából.

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

Az **Üzemeltetési séma konfigurálása** párbeszédpanelen használja az alábbi beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service-csomag neve. |
| Hely | USA nyugati régiója | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | A fogadási funkciókat meghatározó [tarifacsomag.](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

Válassza **az OK gombot.**

### <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Az **Alkalmazásnév**mezőbe írjon be egy `a-z` `0-9`egyedi `-`alkalmazásnevet (az érvényes karakterek , és ), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `https://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

Miután a varázsló befejezte, közzéteszi a ASP.NET Core webalkalmazást az Azure-ban, majd megnyitja az alkalmazást az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/spatial-anchors-azure/web-app-running-live.png)

Az ebben a szakaszban használt alkalmazásnév a formátum `https://<app_name>.azurewebsites.net`URL-előtagjaként használatos. Vegye figyelembe ezt az URL-t, mert szüksége lesz rá.
