---
title: Oktatóanyag – hozzáférést biztosíthat egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból – Azure Active Directory B2C
description: Ismerje meg, hogyan használhatja a Active Directory B2Ct a .NET Core webes API-k védeleméhez, és az API-t egy egyoldalas Node. js-alkalmazásból meghívhatja.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f940893a5328db65549b40269578399655f8539e
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679274"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés biztosítása egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) által védett ASP.NET Core webes API-erőforrást egy egyoldalas alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a lépéseket és az [előfeltételeket az oktatóanyagban: Engedélyezze a hitelesítést egy egyoldalas alkalmazásban Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md)használatával.
* Visual Studio 2019 vagy újabb, vagy Visual Studio Code
* .NET Core 2,2 vagy újabb verzió
* Node.js

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban a webes API olvasási és írási engedélyeit is meghatározza.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Az egyoldalas alkalmazás konfigurálásakor `demo.read` a hatókör **teljes értékét** jegyezze fel egy későbbi lépésben való használatra. A hatókör teljes értéke hasonló a `https://yourtenant.onmicrosoft.com/api/demo.read`következőhöz:.

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy másik alkalmazásból, meg kell adnia a webes API-nak az alkalmazáshoz szükséges engedélyeket.

Az előfeltételként szolgáló oktatóanyagban létrehozott egy *webapp1*nevű webalkalmazást. Ebben az oktatóanyagban úgy konfigurálja az alkalmazást, hogy hívja meg az előző szakaszban létrehozott webes API-t ( *webapi1*).

1. Navigáljon a B2C-bérlőhöz Azure Portal
1. Válassza az **alkalmazások**, majd a *webapp1*lehetőséget.
1. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
1. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Kattintson az **OK** gombra.

Az egyoldalas webalkalmazás regisztrálva van a védett webes API meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik egyoldalas alkalmazást. Az egyoldalas alkalmazás engedélyezési engedélyt ad a Azure AD B2Ctól a védett webes API eléréséhez.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és hatókörök vannak definiálva, a webes API-kódot a Azure AD B2C bérlő használatára konfigurálja. Ebben az oktatóanyagban egy, a GitHubról letöltött minta .NET Core-webalkalmazást fog konfigurálni.

[Töltse le a .zip-archívumot,vagyklónozottwebesAPI-projektetagithubról.\*](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a *B2C-WebApi/**appSettings. JSON*** fájlt a Visual Studióban vagy a Visual Studio Code-ban.
1. Módosítsa a `AzureAdB2C` blokkot úgy, hogy tükrözze a bérlő nevét, a webes API-alkalmazás alkalmazás-azonosítóját, a regisztrációs/bejelentkezési szabályzat nevét és a korábban definiált hatóköröket. A blokknak az alábbi példához hasonlóan kell kinéznie `Tenant` ( `ClientId` megfelelő és értékekkel):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>CORS engedélyezése

Ahhoz, hogy egy egyoldalas alkalmazás meghívja a ASP.NET Core webes API-t, engedélyeznie kell a [CORS](https://docs.microsoft.com/aspnet/core/security/cors) a webes API-ban.

1. A *Startup.cs* fájlban adja hozzá a CORS-t a `ConfigureServices()` metódushoz.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. A `ConfigureServices()` metódusban is állítsa be az `jwtOptions.Authority` értéket a következő jogkivonat-kiállítói URI-ra.

    Cserélje `<your-tenant-name>` le a helyére a B2C-bérlő nevét.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. A metódusban konfigurálja a `Configure()` CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Csak Visual Studio) A megoldáskezelő **Tulajdonságok** területén nyissa meg a *launchSettings. JSON* fájlt, és keresse meg `iisExpress` a blokkot.
1. (Csak Visual Studio) Frissítse az `applicationURL` értéket a megadott portszámmal, amikor regisztrálta a *webapi1* alkalmazást egy korábbi lépésben. Példa:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Egyoldalas alkalmazás konfigurálása

A sorozat [előző oktatóanyagának](active-directory-b2c-tutorials-spa.md) egyoldalas alkalmazása (SPA) Azure ad B2C használ a felhasználói regisztrációhoz és bejelentkezéshez, és meghívja a *frabrikamb2c* demo bérlő által védett ASP.net Core webes API-t.

Ebben a szakaszban egy egyoldalas alkalmazást frissít, hogy meghívja *a Azure ad B2C bérlő* által védett ASP.net Core webes API-t, amelyet a helyi gépen futtat.

A SPA beállításainak módosítása:

1. Nyissa meg az *index. html* fájlt az előző oktatóanyagban letöltött vagy klónozott [Active Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] projektben.
1. Konfigurálja a mintát a bemutató URI azonosítójának használatával *. olvassa el* a korábban létrehozott hatókört, valamint a webes API URL-címét.
    1. A definícióban cserélje le az `b2cScopes` értéket a hatókör teljes URI-ja (a korábban rögzített **teljes hatókör értékre** ). `appConfig`
    1. Módosítsa az `webApi` értéket `applicationURL` az előző szakaszban megadott értékre.

    A `appConfig` definíciónak a következő kódrészlethez hasonlóan kell kinéznie (a bérlő nevével a `<your-tenant-name>`helyén):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>A SPA és a webes API futtatása

Végül futtatja a ASP.NET Core webes API-t és a Node. js egyoldalas alkalmazást a helyi gépen. Ezután jelentkezzen be az egyoldalas alkalmazásba, és egy gomb megnyomásával kezdeményezzen kérést a védett API-nak.

Habár mindkét alkalmazás helyileg fut ebben az oktatóanyagban, Azure AD B2C a biztonságos regisztrációhoz/bejelentkezéshez és a védett webes API-hoz való hozzáférés biztosításához.

### <a name="run-the-aspnet-core-web-api"></a>A ASP.NET Core webes API futtatása

A Visual Studióban nyomja le az **F5** billentyűt a *B2C-WebAPI. SLN* megoldás felépítéséhez és hibakereséséhez. A projekt indításakor megjelenik egy weblap az alapértelmezett böngészőben, amely bejelenti, hogy a webes API elérhető a kérelmek számára.

Ha inkább a parancssori felületet szeretné `dotnet` használni a Visual Studio helyett:

1. Nyisson meg egy konzolablak ablakát, és váltson a  *\*. csproj* fájlt tartalmazó könyvtárra. Példa:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Hozza létre és futtassa a webes API-t `dotnet run`a végrehajtásával.

    Ha az API-t futtatja, a következőhöz hasonló kimenetnek kell megjelennie (az oktatóanyag esetében nyugodtan figyelmen kívül `NETSDK1059` hagyhatja a figyelmeztetéseket):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Az egyoldalas alkalmazás futtatása

1. Nyisson meg egy konzolablak ablakát, és váltson a Node. js-mintát tartalmazó könyvtárra. Példa:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    node server.js
    ```

    A konzolablak megjeleníti a portszámot, ahol az alkalmazás üzemeltetve van.

    ```console
    Listening on port 6420...
    ```

1. `http://localhost:6420` Az alkalmazás megtekintéséhez navigáljon a böngészőben.
1. Jelentkezzen be az [előző oktatóanyagban](active-directory-b2c-tutorials-spa.md)használt e-mail-cím és jelszó használatával. A sikeres bejelentkezés után megjelenik az `User 'Your Username' logged-in` üzenet.
1. Kattintson a **webes API hívása** gombra. A fürdő Azure AD B2Ctól szerez be engedélyezési engedélyt, majd hozzáfér a védett webes API-hoz az index oldal tartalmának megjelenítéséhez:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

Most, hogy már látott egy SPA-kérést egy védett webes API-ból származó erőforrással, mélyebben megértette, hogy ezek az alkalmazások hogyan hatnak egymással és a Azure AD B2C.

> [!div class="nextstepaction"]
> [Active Directory B2C > használható alkalmazások típusai](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
