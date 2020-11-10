---
title: Oktatóanyag – hitelesítés hozzáadása webalkalmazáshoz Azure App Serviceon | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a hitelesítését és engedélyezését Azure App Serviceon futó webalkalmazásokhoz.  Korlátozza a webalkalmazáshoz való hozzáférést a szervezeten belüli felhasználók számára.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a1b2809371a01b1c6fd822e5c4aaa197d0de3c1b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428869"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Oktatóanyag: hitelesítés hozzáadása a Azure App Service-on futó webalkalmazáshoz

Megtudhatja, hogyan engedélyezheti az Auth szolgáltatást a Azure App Service futó webalkalmazások számára, és korlátozhatja a hozzáférést a szervezet felhasználói számára.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Bejelentkezési felhasználó" border="false":::

A Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít, így a felhasználók bejelentkezhetnek, és az adataikat a webalkalmazásban minimális vagy egyetlen kód megírásával érhetik el.  A App Service hitelesítés/engedélyezési modul használata nem kötelező, de megkönnyíti az alkalmazás hitelesítésének és engedélyezésének egyszerűsítését. Ez a cikk bemutatja, hogyan védheti meg webalkalmazását a App Service hitelesítési/engedélyezési modullal, Azure Active Directory identitás-szolgáltató használatával.

A hitelesítési/engedélyezési modul engedélyezve van és konfigurálva van a Azure Portal és az alkalmazás beállításain keresztül. Nem szükségesek SDK-k, meghatározott nyelvek vagy az alkalmazás kódjának módosítása. Számos különböző identitás-szolgáltató támogatott, többek között az Azure AD, a Microsoft-fiók, a Facebook, a Google és a Twitter. Ha a hitelesítési/engedélyezési modul engedélyezve van, minden bejövő HTTP-kérelem áthalad az alkalmazás kódjának kezelése előtt.  További információért olvassa el [a hitelesítést és az engedélyezést Azure app Serviceban](overview-authentication-authorization.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A webes alkalmazás hitelesítésének konfigurálása
> * A webalkalmazáshoz való hozzáférés korlátozása a szervezeten belüli felhasználók számára

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Webalkalmazás létrehozása és közzététele App Service

Ebben az oktatóanyagban szüksége lesz egy webalkalmazásra, amely a Azure App Servicere van telepítve.  Használhat egy meglévő webalkalmazást, vagy a [ASP.net Core](quickstart-dotnetcore.md) rövid útmutatóban új webalkalmazást hozhat létre és tehet közzé app Service.

Akár meglévő webalkalmazást használ, akár újat hoz létre, jegyezze fel a webalkalmazás nevét és azon erőforráscsoport nevét, amelyre a webalkalmazás telepítve van. Ezekre a nevekre az oktatóanyag során van szükség. Ebben az oktatóanyagban például az eljárások és a képernyőképek nevei *SecureWebApp* tartalmaznak.

## <a name="configure-authentication-and-authorization"></a>Hitelesítés és engedélyezés konfigurálása

Most már fut egy webalkalmazás a App Serviceon.  Ezután engedélyeznie kell a webalkalmazás hitelesítését és engedélyezését. Az Azure Active Directoryt fogja használni identitásszolgáltatóként. További információért tekintse meg [az Azure Active Directory-hitelesítés az App Services-alkalmazáshoz való konfigurálásáról](configure-authentication-provider-aad.md) szóló részt.

A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az *erőforráscsoportok* lehetőséget bármely oldalon.

Az **erőforráscsoportok** területen keresse meg és válassza ki az erőforráscsoportot. Az **Áttekintés** területen válassza ki az alkalmazás felügyeleti lapját.

:::image type="content" alt-text="App Service kiválasztása" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

Az alkalmazás bal oldali menüjében válassza a **hitelesítés/engedélyezés** lehetőséget, majd **a** bejelöléssel engedélyezze app Service hitelesítést.

Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a **Bejelentkezés az Azure Active Directoryval** lehetőséget.

A **hitelesítésszolgáltatók** területen válassza a **Azure Active Directory** lehetőséget. Válassza az **expressz** lehetőséget, majd fogadja el az alapértelmezett beállításokat új ad-alkalmazás létrehozásához, majd kattintson **az OK gombra**.

:::image type="content" alt-text="Expressz hitelesítés" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

A **hitelesítés/engedélyezés** lapon válassza a **Mentés** lehetőséget.

Miután megtalálta az üzenetet `Successfully saved the Auth Settings for <app-name> App` , frissítse a portál oldalt.

Most már rendelkezik egy olyan alkalmazással, amelyet a App Service hitelesítés és engedélyezés biztosít.

## <a name="verify-limited-access-to-the-web-app"></a>A webalkalmazáshoz való korlátozott hozzáférés ellenőrzése

Ha engedélyezte a App Service hitelesítési és engedélyezési modulját, az alkalmazás regisztrálása az Azure AD-bérlőben lett létrehozva.  Az alkalmazás regisztrációja ugyanaz a megjelenítendő név, mint a webalkalmazás. A beállítások megtekintéséhez válassza a **Azure Active Directory** lehetőséget a portál menüjében, és válassza a **Alkalmazásregisztrációk** lehetőséget.  Válassza ki a létrehozott alkalmazás-regisztrációt.  Az áttekintésben ellenőrizze, hogy a **támogatott fióktípus** **csak a saját szervezetre** van-e beállítva.

:::image type="content" alt-text="Hozzáférés ellenőrzése" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Annak ellenőrzéséhez, hogy az alkalmazáshoz való hozzáférés csak a szervezeten belüli felhasználókra korlátozódik, indítson el egy böngészőt az inkognitóban vagy a privát módban, és navigáljon a címre `https://<app-name>.azurewebsites.net` .  A biztonságos bejelentkezési oldalra kell irányítani, amely ellenőrzi, hogy a nem hitelesített felhasználók nem férhetnek hozzá a helyhez.  A webhelyhez való hozzáféréshez jelentkezzen be felhasználóként a szervezetében.  Létrehozhat egy új böngészőt is, és megpróbálhatja bejelentkezni egy személyes fiók használatával annak ellenőrzéséhez, hogy a szervezeten kívüli felhasználók nem rendelkeznek hozzáféréssel.  

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha elkészült az Oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a kapcsolódó erőforrásokra, [törölje a létrehozott erőforrásokat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * A webes alkalmazás hitelesítésének konfigurálása
> * A webalkalmazáshoz való hozzáférés korlátozása a szervezeten belüli felhasználók számára

> [!div class="nextstepaction"]
> [Az App Service hozzáfér a tárolóhoz](scenario-secure-app-access-storage.md)
