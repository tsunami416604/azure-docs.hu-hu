---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "67135325"
---
### <a name="open-the-publish-wizard"></a>A közzétételi varázsló megnyitásához

A **Megoldáskezelőben**, kattintson a jobb gombbal a **SharingService** projektre, és válassza **közzététel**.

A közzétételi varázsló elindul. Válassza ki **App Service-ben** > **közzététel** megnyitásához a **létrehozása App Service** párbeszédpanel bezárásához.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az a **létrehozása App Service** párbeszédpanelen jelölje ki **vegyen fel egy fiókot** , és jelentkezzen be az Azure-előfizetéshez. Ha már bejelentkezett, válassza ki a kívánt fiók a legördülő listából.

> [!NOTE]
> Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
>

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el a **myResourceGroup** erőforráscsoportot, majd kattintson az **OK** gombra.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet.

Az a **konfigurálása csomag** párbeszédpanelen, akkor ezeket a beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service-csomag neve. |
| Location egység | USA nyugati régiója | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , amely meghatározza az üzemeltetési funkciókat. |

Kattintson az **OK** gombra.

### <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

A **alkalmazásnév**, adjon meg egy egyedi névre (érvényes karakterek: `a-z`, `0-9`, és `-`), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `https://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

A varázsló befejezését követően közzéteszi az ASP.NET Core-webalkalmazást az Azure-ba, és ezután megnyitja az alkalmazást az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/spatial-anchors-azure/web-app-running-live.png)

A jelen szakaszban használt nevét használja az URL-előtag a következő formátumban `https://<app_name>.azurewebsites.net`. Jegyezze fel az URL-címet, mert szüksége lesz rá.
