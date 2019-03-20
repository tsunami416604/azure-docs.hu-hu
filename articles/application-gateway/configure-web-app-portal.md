---
title: Forgalom több-bérlős alkalmazások, például az App service web Apps alkalmazások az Azure Application Gatewayjel – portál
description: Ez a cikk konfigurálása az Azure App service web apps szolgáltatásban, a meglévő vagy új application gateway háttérkészlet tagjainak nyújt útmutatást.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176208"
---
# <a name="configure-app-service-with-application-gateway"></a>Az App Service az Application Gateway konfigurálása

Az Application gateway lehetővé teszi, hogy rendelkezik egy Azure App service-webalkalmazás vagy egyéb több-bérlős szolgáltatások háttérkészlet-tagként. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> - Hozzon létre egy háttérkészlet és a egy App Service-ben hozzáadása
> - HTTP-beállítások és az egyéni mintavétel létrehozása "Válasszon állomásnév" kapcsolók engedélyezve

## <a name="prerequisites"></a>Előfeltételek

- Az Application gateway: Ha nem rendelkezik meglévő application gateway, hogyan [application gateway létrehozása](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- App Service-ben: Ha nem rendelkezik egy meglévő App service, [App service-dokumentáció](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>App Service-ben háttérkészlet hozzáadása

1. Az Azure Portalon nyissa meg az application gateway konfigurációs nézete.

2. A **háttérkészletek**, kattintson a **Hozzáadás** hozhat létre egy új háttérkészletet.

3. Adjon meg egy megfelelő nevet a háttérkészlethez. 

4. A **célok**, kattintson a legördülő listából, és válassza a **App Services** paramétert.

5. Azonnal az alábbi legördülő lista a **célok** legördülő menü jelenik meg az App Services listáját tartalmazó. A legördülő listából válassza ki az App Service háttérrendszer készlettag szerint adja hozzá, és kattintson a Hozzáadás gombra.

   ![App service háttérrendszer](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Az App Service-ben HTTP-beállítások létrehozása

1. A **HTTP-beállítások**, kattintson a **Hozzáadás** hozhat létre egy új HTTP-beállítás.

2. Adjon meg egy nevet a HTTP-beállítás és, engedélyezheti vagy letilthatja a Cookie-alapú affinitás alapján a követelmény.

3. Válassza ki a HTTP vagy HTTPS protokoll megfelelően a használati eset. 

4. Jelölje be a **használata az App Service** fog kapcsolja a **mintavétel létrehozása az állomásnévvel kiválasztása a háttérkiszolgálói címen** és **címet válasszon állomásnévvel** beállítások. Ez a beállítás is mintavétel létrehozása automatikusan engedélyezve van a kapcsolóval, és társítsa azt a HTTP-beállítás.

5. Kattintson a **OK** a HTTP-beállítás létrehozásához.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Hozzon létre szabályt, hogy be lehessen vonni a figyelőt, a Háttérkészlet és a HTTP-beállítás

1. A **szabályok**, kattintson a **alapszintű** új alapszintű szabály létrehozásához.

2. Adjon meg egy megfelelő nevet, és válassza ki a figyelő, amely a bejövő kérelmek, az App Service létesít.

3. Az a **háttérkészlet** legördülő menüben válassza a háttérkiszolgáló-készlet fent létrehozott.

4. Az a **HTTP-beállítás** legördülő menüben válassza a HTTP-beállítás a fent létrehozott.

5. Kattintson a **OK** Ez a szabály mentéséhez.

   ![Szabály](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Hozzáférés korlátozása

Az ezekben a példákban üzembe helyezett webalkalmazások közvetlenül az internetről elérhető nyilvános IP-címek használatára. A hibaelhárítás, amikor egy új funkciót működésével ismerkedik, és új néhány dolgot, így. Azonban éles üzembe helyezés egy szolgáltatást szeretne, ha szeretné hozzáadhat több korlátozást.

A web Apps korlátozhatja a hozzáférést egy módja [statikus IP-korlátozások az Azure App Service](../app-service/app-service-ip-restrictions.md). Például korlátozhatja a web app, hogy ez csak származó forgalmat megkapja az application gateway. Az app service IP korlátozás szolgáltatás használatával az application gateway virtuális IP-CÍMEK listája csak címként hozzáféréssel rendelkező.

## <a name="next-steps"></a>További lépések

Az App Service-ben és az egyéb több-bérlős támogatással, az application gateway szolgáltatással kapcsolatos további tudnivalókért lásd: [több-bérlős szolgáltatás támogatása az application gatewayen](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Abban az esetben, ha az App service válasza az App service URL-cím átirányítása, lásd: hogyan [átirányítás, az App service URL-címet a probléma elhárításához](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
