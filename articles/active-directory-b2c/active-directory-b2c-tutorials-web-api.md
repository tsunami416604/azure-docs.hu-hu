---
title: Oktatóanyag – ASP.NET webes API hozzáférés engedélyezése – Azure Active Directory B2C |} A Microsoft Docs
description: Az Active Directory B2C használata az ASP.NET webes API védelmére és meghívására egy ASP.NET-webalkalmazás oktatóanyag.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cc4db0f2fe8f5db41f6e8332a398029bd105f3af
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756342"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés biztosítása az ASP.NET webes API-hoz az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy védett webes API-erőforrást az Azure Active Directory (Azure AD) B2C-vel egy ASP.NET-webalkalmazások.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adjon hozzá egy webes API-alkalmazás
> * A webes API hatóköreinek konfigurálásáról
> * Adja meg az engedélyeket a webes API-hoz
> * A minta az alkalmazás konfigurálása

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és az Előfeltételek [oktatóanyag: Egy webalkalmazás, Azure Active Directory B2C használatával hitelesítés engedélyezése](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Adjon hozzá egy webes API-alkalmazás

Webes API-erőforrásokhoz regisztrálni kell a bérlőn belüli előtt fogadja el, és védett erőforrás-kérelmek megválaszolásához ügyfélalkalmazások, amelyek egy hozzáférési jogkivonatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy nevet az alkalmazásnak. Ha például *webapi1*.
6. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
7. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:44332`.
8. A **Alkalmazásazonosító URI-t**, adja meg a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amelyek a webalkalmazás konfigurálásakor fogja használni.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

Hatókört biztosít a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

1. Válassza ki **alkalmazások**, majd válassza ki *webapi1*.
2. Válassza ki **közzétett hatókörök**.
3. A **hatókör**, adja meg `Hello.Read`, és adja meg a leírást `Read access to hello`.
4. A **hatókör**, adja meg `Hello.Write`, és adja meg a leírást `Write access to hello`.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök ügyfél megadására is használható alkalmazás számára a webes API-t.

## <a name="grant-permissions"></a>Engedélyek megadása

Egy védett webes API hívása egy alkalmazásból, az alkalmazásengedélyeket az API-nak kell. Az előfeltételnek számító oktatóanyagot hozott létre egy webalkalmazást az Azure AD B2C nevű *webapp1*. Ez az alkalmazás használhatja a webes API meghívásához.

1. Válassza ki **alkalmazások**, majd válassza ki *webapp1*.
2. Válassza ki **API-hozzáférés**, majd válassza ki **Hozzáadás**.
3. Az a **API kiválasztása** legördülő menüben válasszon ki *webapi1*.
4. Az a **hatókörök kiválasztása** legördülő menüben válasszon ki a **Hello.Read** és **Hello.Write** , amelyet korábban megadott hatókörök.
5. Kattintson az **OK** gombra.

Az alkalmazás regisztrálva van a védett webes API meghívásához. Egy felhasználó hitelesíti magát, az Azure AD B2C használni az alkalmazást. Az alkalmazás Azure AD B2C-vel hozzáférni a védett webes API-engedélyek kapja.

## <a name="configure-the-sample"></a>A minta konfigurálásához

Most, hogy a webes API regisztrálva van, és meghatározta a hatóköröket, konfigurálnia a webes API-t az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban egy mintául szolgáló webes API-t fog konfigurálni. A mintául szolgáló webes API-t az előfeltételként szolgáló oktatóanyagot letöltött projekt tartalmazza.

Két projekt szerepel a mintául szolgáló megoldásban:

A következő két projekt vannak a mintául szolgáló megoldásban:

- **TaskWebApp** – létrehozása és a egy feladatlista szerkesztése. A példa a **regisztrálási vagy bejelentkezési** való regisztráció vagy bejelentkezés a felhasználói felhasználói folyamatot.
- **TaskService** – támogatja a létrehozás, Olvasás, frissítés, és a feladatlista törlés. Az API-t az Azure AD B2C által védett és TaskWebApp hívja meg.

### <a name="configure-the-web-application"></a>A webalkalmazás konfigurálása

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
2. Nyissa meg a **Web.config** elemet a **TaskWebApp** projektben.
3. Az API helyi futtatásához használja az **api:TaskServiceUrl** localhost-beállítást. Az alábbiak szerint módosítsa a Web.config fájlt: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Konfigurálja az API URI-ját. Ez az az URI-t a webalkalmazás az API-kérelem leadásához használja. A kért engedélyeket is konfigurálja.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a **Web.config** elemet a **TaskService** projektben.
2. Konfigurálja az API-t a bérlő használatához.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Állítsa be az ügyfél-azonosítót az API regisztrált alkalmazásazonosítójára.

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. A felhasználó a bejelentkezési nevű folyamat mentése Update és a bejelentkezési felhasználói folyamat.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Konfigurálja a hatókör beállításait, hogy egyezzenek a portálon korábban létrehozottakkal.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Minta futtatása

A **TaskWebApp** és a **TaskService** projektet is futtatnia kell. 

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza az **Indítási projektek beállítása...** lehetőséget. 
2. Válassza ki **több kezdőprojekt**.
3. Mindkét projektnél módosítsa a **Művelet** értékét **Indításra**.
4. Kattintson a **OK** a konfiguráció mentéséhez.
5. Nyomja le az **F5** gombot mindkét alkalmazás futtatásához. Mindegyik alkalmazás saját böngészőlapon nyílik `https://localhost:44316/` a webalkalmazás.
    `https://localhost:44332/` a webes API.

6. Kattintson a webalkalmazás **regisztrációs / bejelentkezési** a webes alkalmazásba való bejelentkezéshez. Használja a korábban létrehozott fiókot. 
7. Miután bejelentkezett, kattintson a **feladatlista** , és hozzon létre egy feladatlista elemet.

Amikor létrehoz egy feladatlista elemet, a webes alkalmazás kérést küld a webes API a feladatlista elem létrehozására. Védett webalkalmazás hívja a védett webes API-t az Azure AD B2C-bérlőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adjon hozzá egy webes API-alkalmazás
> * A webes API hatóköreinek konfigurálásáról
> * Adja meg az engedélyeket a webes API-hoz
> * A minta az alkalmazás konfigurálása

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C-vel](tutorial-add-identity-providers.md)
