---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963336"
---
### <a name="launch-the-publish-wizard"></a>A közzétételi varázsló elindítása

Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **SharingService** projektre, és válassza **közzététel**.

A közzétételi varázsló automatikusan elindul. Válassza az **App Service** > **Publish** (App Service, Közzététel) lehetőséget a **Create App Service** (App Service létrehozása) párbeszédpanel megnyitásához.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson az **Add an account** (Fiók hozzáadása) parancsra, majd jelentkezzen be az Azure-előfizetésébe. Ha már bejelentkezett, válassza ki a kívánt fiókot a legördülő listából.

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

Az a **konfigurálása csomag** párbeszédpanelen, a beállításokat használja a táblázatban.

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service-csomag neve. |
| Hely | USA nyugati régiója | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

Kattintson az **OK** gombra.

### <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Az **Alkalmazás neve** mezőben adjon meg egy egyedi nevet az alkalmazás számára (érvényes karakterek: `a-z`, `0-9` és `-`), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `https://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

Miután a varázsló befejeződött, közzéteszi az ASP.NET Core-webalkalmazást az Azure-ban, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/spatial-anchors-azure/web-app-running-live.png)

A [létrehozási és közzétételi lépésben](#create-and-publish-the-web-app) megadott alkalmazásnév lesz az URL-előtag a következő formátumban: `https://<app_name>.azurewebsites.net`. Jegyezze fel az URL-címet, később lesz.
