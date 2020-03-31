---
title: A több-bérlős alkalmazások forgalmának kezelése a portál használatával
titleSuffix: Azure Application Gateway
description: Ez a cikk útmutatást nyújt az Azure App Service-webalkalmazások meglévő vagy új alkalmazásátjáró háttérkészletének tagjaként történő konfigurálásához.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075162"
---
# <a name="configure-app-service-with-application-gateway"></a>Az App Service konfigurálása az Application Gateway alkalmazásátjáróval

Mivel az alkalmazásszolgáltatás egy több-bérlős szolgáltatás a közös üzembe helyezés helyett, a bejövő kérelem gazdagépfejlécét használja a kérelem helyes alkalmazásszolgáltatási végpontra történő feloldásához. Általában az alkalmazás DNS-neve, amely viszont az alkalmazásszolgáltatást előképező alkalmazásátjáróhoz társított DNS-név, eltér a háttéralkalmazás-szolgáltatás tartománynevétől. Ezért az alkalmazásátjáró által fogadott eredeti kérelem állomásfejléce nem egyezik meg a háttérszolgáltatás állomásnevével. Emiatt, kivéve, ha az alkalmazás átjáróból a háttérrendszerbe küldött kérelem gazdagépfejléce a háttérszolgáltatás gazdagépnevére változik, a több-bérlős háttérrendszerek nem tudják a kérést a megfelelő végpontra feloldani.

Az Application Gateway `Pick host name from backend address` biztosít egy nevű kapcsolót, amely felülbírálja a kérelem ben lévő állomásfejlécet a háttérrendszer gazdagépnevével, amikor a kérelem az alkalmazásátjáróról a háttérrendszerre kerül. Ez a funkció lehetővé teszi a több-bérlős háttérrendszerek, például az Azure app service és az API-kezelés támogatását. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> - Háttérkészlet létrehozása és appszolgáltatás hozzáadása
> - HTTP-beállítások és egyéni mintavétel létrehozása a "Pick Hostname" kapcsolókkal engedélyezve

## <a name="prerequisites"></a>Előfeltételek

- Alkalmazásátjáró: Ha nem rendelkezik meglévő alkalmazásátjáróval, tekintse meg, hogyan [hozhat létre alkalmazásátjárót](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- App-szolgáltatás: Ha nem rendelkezik meglévő appszolgáltatással, olvassa el [az App Service dokumentációját.](https://docs.microsoft.com/azure/app-service/)

## <a name="add-app-service-as-backend-pool"></a>Alkalmazásszolgáltatás hozzáadása háttérkészletként

1. Az Azure Portalon nyissa meg az alkalmazásátjáró konfigurációs nézetét.

2. A **Háttérmedencék**csoportban kattintson a **Hozzáadás** gombra új háttérkészlet létrehozásához.

3. Adja meg a háttérkészlet megfelelő nevét. 

4. A **Célok csoportban**kattintson a legördülő menüre, és válassza az **App Services** lehetőséget.

5. Megjelenik egy közvetlenül a **Célok** legördülő lista alatti legördülő lista, amely az Alkalmazásszolgáltatások listáját tartalmazza. Ebből a legördülő menüből válassza ki azt az App Service szolgáltatást, amelyet háttérkészlet-tagként szeretne hozzáadni, és kattintson a Hozzáadás gombra.

   ![Alkalmazásszolgáltatás-háttérszolgáltatás](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > A legördülő menü csak azokat az alkalmazásszolgáltatásokat fogja felnagyítja, amelyek ugyanabban az előfizetésben vannak, mint az Application Gateway. Ha olyan alkalmazásszolgáltatást szeretne használni, amely nem abban az előfizetésben van, mint amelyben az Application Gateway található, akkor ahelyett, hogy az **App Services t** a **Célok** legördülő menüben választaná, válassza az **IP-címet vagy** a állomásnév beállítást, és adja meg a állomásnevet (például). azurewebsites.net) az alkalmazásszolgáltatásból.

## <a name="create-http-settings-for-app-service"></a>HTTP-beállítások létrehozása az App Service-hez

1. A **HTTP-beállítások csoportban**kattintson a **Hozzáadás** gombra új HTTP-beállítás létrehozásához.

2. Adjon meg egy nevet a HTTP-beállításnak, és engedélyezheti vagy letilthatja a cookie-alapú affinitást a követelménynek megfelelően.

3. Válassza ki a protokollt HTTP vagy HTTPS néven a használati esetnek hasonlóan. 

   > [!NOTE]
   > Ha a HTTPS lehetőséget választja, nem kell hitelesítési vagy megbízható főtanúsítványt feltöltenie az alkalmazásszolgáltatás háttérrendszerének engedélyezési listájához, mivel az alkalmazásszolgáltatás megbízható Azure-szolgáltatás.

4. Jelölje be az **App Service használatához jelölőnégyzetet.** Ne feledje, `Create a probe with pick host name from backend address` `Pick host name from backend address` hogy a kapcsolók, és automatikusan engedélyezve lesz.`Pick host name from backend address` felülírja a kérelem ben lévő állomásfejlécet a háttérrendszer állomásnevével, amikor a kérelem az Alkalmazásátjáróról a háttérrendszerre kerül.  

   `Create a probe with pick host name from backend address`automatikusan létrehoz egy állapotmintát, és társítja azt ehhez a HTTP-beállításhoz. Ehhez a HTTP-beállításhoz nem kell más állapotminta létrehozásához. Ellenőrizheti, hogy egy új, <HTTP Setting name> <Unique GUID> névvel rendelkező mintavétel került-e fel az `Pick host name from backend http settings enabled`Állapot-mintavételek listájára, és már rendelkezik-e a kapcsolóval.

   Ha már rendelkezik egy vagy több, az App Service szolgáltatáshoz használt HTTP-beállítással, és ha ezek a HTTP-beállítások `Create a probe with pick host name from backend address` ugyanazt a protokollt használják, mint amit a létrehozandó lapon használ, akkor a kapcsoló helyett egy legördülő menüt kap az egyéni mintavételek kiválasztásához. Ennek az az oka, hogy mivel már létezik egy HTTP-beállítás az `Pick host name from backend http settings enabled` alkalmazásszolgáltatással, ezért létezne egy állapotminta is, amely rendelkezik a kapcsolóval. Válassza ki az egyéni mintavételt a legördülő menüből.

5. A HTTP-beállítás létrehozásához kattintson az **OK** gombra.

   ![HTTP-beállítás1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-beállítás2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Szabály létrehozása a Figyelő, a háttérkészlet és a HTTP-beállítás összekapcsolására

1. Új alapszabály létrehozásához kattintson a **Szabályok** **csoportalapgombra.**

2. Adja meg a megfelelő nevet, és válassza ki a figyelőt, amely elfogadja az app szolgáltatás bejövő kérelmeket.

3. A **háttérkészlet** legördülő menüben válassza ki a fent létrehozott háttérkészletet.

4. A **HTTP beállítás** legördülő menüben válassza ki a fent létrehozott HTTP-beállítást.

5. A szabály mentéséhez kattintson az **OK** gombra.

   ![Szabály](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>További konfiguráció az alkalmazásszolgáltatás relatív útvonalára való átirányítás esetén

Amikor az alkalmazásszolgáltatás átirányítási választ küld az ügyfélnek, hogy irányítsa át a relatív elérési útját (például egy átirányítás contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2), ugyanazt az állomásnevet használja a válasz helyfejlécében, mint az alkalmazásátjárótól kapott kérelemben. Így az ügyfél közvetlenül a contoso.azurewebsites.net/path2, ahelyett, hogy az alkalmazásátjárón (contoso.com/path2) keresztül. Az alkalmazásátjáró megkerülése nem kívánatos.

Ha a használati esetben vannak olyan esetek, amikor az App Service-nek átirányítási választ kell küldenie az ügyfélnek, hajtsa végre a [további lépéseket a helyfejléc újraírásához.](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)

## <a name="restrict-access"></a>Hozzáférés korlátozása

Az ezekben a példákban telepített webalkalmazások nyilvános IP-címeket használnak, amelyek közvetlenül az internetről érhetők el. Ez segít a hibaelhárításban, amikor új funkcióról tanul, és új dolgokat próbál ki. Ha azonban egy funkciót éles környezetben kíván telepíteni, további korlátozásokat kell hozzáadnia.

A webalkalmazásokhoz való hozzáférést az [Azure App Service statikus IP-korlátozásainak használatával korlátozhatja.](../app-service/app-service-ip-restrictions.md) Korlátozhatja például a webalkalmazást, hogy az csak az alkalmazásátjáróból érkező forgalmat fogadjon. Az alkalmazásszolgáltatás IP-korlátozási szolgáltatásával az alkalmazásátjáró VIP-címét sorolja fel az egyetlen hozzáféréssel rendelkező címként.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az alkalmazásszolgáltatásról és az alkalmazásátjáróval való más több-bérlős támogatásról, olvassa el a [több-bérlős szolgáltatás támogatása az alkalmazásátjáróval című témakört.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)
