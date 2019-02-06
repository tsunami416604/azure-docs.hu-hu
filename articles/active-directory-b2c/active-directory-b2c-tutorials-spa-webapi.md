---
title: Oktatóanyag – való hozzáférés engedélyezése egy ASP.NET Core webes API-t egy egyoldalas alkalmazás – Azure Active Directory B2C |} A Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy .NET Core webes API védelmére és meghívására egy egyoldalas alkalmazásból.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13cbf1e81e0d203c181efb0881ec2a437cbaef24
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752177"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Az ASP.NET Core webes API-t egy egyoldalas alkalmazás Azure Active Directory B2C használatával való hozzáférés engedélyezése

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory (Azure AD) B2C-vel védett ASP.NET Core webes API-t egy egyoldalas alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adjon hozzá egy webes API-alkalmazás
> * A webes API hatóköreinek konfigurálásáról
> * Adja meg az engedélyeket a webes API-hoz
> * A minta az alkalmazás konfigurálása

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és az Előfeltételek [oktatóanyag: Egyoldalas alkalmazás hitelesítés engedélyezése az Azure Active Directory B2C fiókkal rendelkező](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Adjon hozzá egy webes API-alkalmazás

Webes API-erőforrásokhoz regisztrálni kell a bérlőn belüli előtt fogadja el, és védett erőforrás-kérelmek megválaszolásához ügyfélalkalmazások, amelyek egy hozzáférési jogkivonatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy nevet az alkalmazásnak. Ha például *webapi1*.
6. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
7. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:5000`.
8. A **Alkalmazásazonosító URI-t**, adja meg a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amelyek a webalkalmazás konfigurálásakor fogja használni.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

Hatókört biztosít a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási engedélyeket a webes API számára.

1. Válassza ki **alkalmazások**, majd válassza ki *webapi1*.
2. Válassza ki **közzétett hatókörök**.
3. A **hatókör**, adja meg `Hello.Read`, és adja meg a leírást `Read access to hello`.
4. A **hatókör**, adja meg `Hello.Write`, és adja meg a leírást `Write access to hello`.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazás-engedélyeket biztosíthat a webes API-nak.

## <a name="grant-permissions"></a>Engedélyek megadása

Egy védett webes API hívása egy alkalmazásból, az alkalmazásengedélyeket az API-nak kell. Az előfeltételnek számító oktatóanyagot hozott létre egy webalkalmazást az Azure AD B2C nevű *webapp1*. Ez az alkalmazás használhatja a webes API meghívásához.

1. Válassza ki **alkalmazások**, majd válassza ki *webapp1*.
2. Válassza ki **API-hozzáférés**, majd válassza ki **Hozzáadás**.
3. Az a **API kiválasztása** legördülő menüben válasszon ki *webapi1*.
4. Az a **hatókörök kiválasztása** legördülő menüben válasszon ki a **Hello.Read** és **Hello.Write** , amelyet korábban megadott hatókörök.
5. Kattintson az **OK** gombra.

A **Saját egyoldalas mintaalkalmazás** regisztrálva van a védett **Hello Core API** meghívásához. Egy felhasználó hitelesíti magát, az Azure AD B2C az egyoldalas alkalmazás használatára. Az egyoldalas alkalmazás Azure AD B2C-vel hozzáférni a védett webes API-engedélyek kapja.

## <a name="configure-the-sample"></a>A minta konfigurálásához

Most, hogy a webes API regisztrálva van, és meghatározta a hatóköröket, konfigurálnia a webes API kódját az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban egy mintául szolgáló .NET Core-webalkalmazás a Githubból letöltheti fog konfigurálni. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a **B2C-WebAPI.sln** megoldást a Visual Studióban.
2. Nyissa meg az **appsettings.json** fájlt. Frissítse az alábbi értékeket, hogy konfigurálja a webes API-t a bérlő használatához:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>CORS engedélyezése

Ahhoz, hogy az egyoldalas alkalmazás, az ASP.NET Core webes API meghívásához, engedélyeznie kell [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. A **Startup.cs** fájlban adja hozzá a CORS-t a `ConfigureServices()` metódushoz.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. A **Startup.cs** fájlban konfigurálja a CORS-t a `Configure()` metódusban.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Nyissa meg a **launchSettings.json** fájlt a **Tulajdonságok** területen, keresse meg az **iisSettings** *applicationURL* beállítást, és a portszámot állítsa olyan értékre, amely regisztrálva van az API válasz URL-címére: `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Az egyoldalas alkalmazás konfigurálása

Az egyoldalas alkalmazás Azure AD B2C-t használ a felhasználói regisztrációt, bejelentkezést és a védett ASP.NET Core webes API-hívások. Az egyoldalas alkalmazás a .NET Core webes API meghívásához frissítse.

Az alkalmazás beállításainak módosításához:

1. Nyissa meg az `index.html` fájlt.
2. Konfigurálja a mintát az Azure AD B2C-bérlő regisztrációs információival. Az alábbi kódban adja hozzá a bérlő nevét a **b2cScopes** paraméterhez, és módosítsa a **webApi** értékét a korábban feljegyzett *applicationURL* értékre:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Az SPA-ALKALMAZÁSOKBA és a webes API futtatása

Az egyoldalas Node.js-alkalmazás és a .NET Core webes API-t is futtatni szeretné.

### <a name="run-the-aspnet-core-web-api"></a>Az ASP.NET Core webes API futtatása 

Nyomja le az **F5** billentyűt a **B2C-WebAPI.sln** megoldás hibakereséséhez a Visual Studióban.

Ha a projekt elindult, megnyílik egy weblap az alapértelmezett böngészőben, és amely tájékoztatja róla, hogy a webes API elérhetővé vált a kérelmek számára.

### <a name="run-the-single-page-app"></a>Az egyoldalas alkalmazás futtatása

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Futtassa az alábbi parancsot:
    ```
    npm install && npm update
    node server.js
    ```

    A konzolablakban láthatja, a port számát, ahol az alkalmazás üzemel.
    
    ```
    Listening on port 6420...
    ```

4. Keresse meg a címet a böngésző segítségével `http://localhost:6420` az alkalmazás megtekintéséhez.
5. Jelentkezzen be a [felhasználók egyoldalas (JavaScript-) alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-spa.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
6. Kattintson a **API hívása**.

Miután regisztrált egy felhasználói fiókot vagy bejelentkezett eggyel, a minta meghívja a védett webes API-t, és kiad egy eredményt.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adjon hozzá egy webes API-alkalmazás
> * A webes API hatóköreinek konfigurálásáról
> * Adja meg az engedélyeket a webes API-hoz
> * A minta az alkalmazás konfigurálása

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C-vel](tutorial-add-identity-providers.md)
