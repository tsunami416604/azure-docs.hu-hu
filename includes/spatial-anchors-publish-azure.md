---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998457"
---
### <a name="open-the-publish-wizard"></a>A közzétételi varázsló megnyitása

**Megoldáskezelő** kattintson a jobb gombbal a **SharingService** projektre, majd válassza a **Közzététel** lehetőséget.

Elindul a közzétételi varázsló. 

Válassza ki **app Service**  >  **Közzététel** elemet a **Létrehozás app Service** panel megnyitásához.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra.

A **app Service létrehozása** panelen válassza a **fiók hozzáadása** lehetőséget, majd jelentkezzen be az Azure-előfizetésbe. Ha már bejelentkezett, válassza ki a kívánt fiókot a legördülő listából.

   > [!NOTE]
   > Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
   >

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el az erőforráscsoport **myResourceGroup**, majd kattintson **az OK gombra**.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet.

A **üzemeltetési terv konfigurálása** panelen használja az alábbi beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service terv neve |
| Hely | USA nyugati régiója | Az az adatközpont, amelyen a webalkalmazás fut |
| Méret | Ingyenes | Az üzemeltetési funkciókat meghatározó [díjszabási](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) csomag |

Válassza az **OK** lehetőséget.

### <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Az **alkalmazás neve** mezőben adjon meg egy egyedi nevet. Érvényes karakterek: a-z, 0-9 és kötőjel (-), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `https://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

   A varázsló befejeződése után közzéteszi a ASP.NET Core webalkalmazást az Azure-ban, majd megnyitja az alkalmazást az alapértelmezett böngészőben.

  ![Képernyőkép egy közzétett ASP.NET-webalkalmazásról az Azure-ban.](./media/spatial-anchors-azure/web-app-running-live.png)

Az ebben a szakaszban használt alkalmazás neve URL-előtagként van megadva a formátumban `https://<app_name>.azurewebsites.net` . Másolja ezt az URL-címet egy szövegszerkesztőbe későbbi használatra.
