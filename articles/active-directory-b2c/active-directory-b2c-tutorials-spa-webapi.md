---
title: Oktatóanyag – hozzáférést biztosíthat egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból – Azure Active Directory B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Active Directory B2Ct a .NET Core webes API-k védeleméhez, és hogyan hívhatja meg az API-t egy egyoldalas Node. js-alkalmazásból.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 7dae8bb552fe12d1cdf1f3d8a5fe124491933a87
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420326"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: hozzáférés biztosítása egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) által védett ASP.NET Core webes API-erőforrást egy egyoldalas alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a lépéseket és az előfeltételeket az [oktatóanyagban: engedélyezze a hitelesítést egy egyoldalas alkalmazásban Azure Active Directory B2C használatával](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 vagy újabb, vagy Visual Studio Code
* .NET Core 2,2 vagy újabb verzió
* Node.js

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban a webes API olvasási és írási engedélyeit is meghatározza.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Az egyoldalas alkalmazás konfigurálásakor az `demo.read` **hatókör hatókörében** szereplő értéket jegyezze fel, hogy az egy későbbi lépésben használhassa. A hatókör teljes értéke hasonló a `https://contosob2c.onmicrosoft.com/api/demo.read`hoz.

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy másik alkalmazásból, meg kell adnia a webes API-nak az alkalmazáshoz szükséges engedélyeket.

Az előfeltételként szolgáló oktatóanyagban létrehozott egy *webapp1*nevű webalkalmazást. Ebben az oktatóanyagban úgy konfigurálja az alkalmazást, hogy hívja meg az előző szakaszban létrehozott webes API-t ( *webapi1*).

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az egyoldalas webalkalmazás regisztrálva van a védett webes API meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik egyoldalas alkalmazást. Az egyoldalas alkalmazás engedélyezési engedélyt ad a Azure AD B2Ctól a védett webes API eléréséhez.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és hatókörök vannak definiálva, a webes API-kódot a Azure AD B2C bérlő használatára konfigurálja. Ebben az oktatóanyagban egy, a GitHubról letöltött minta .NET Core-webalkalmazást fog konfigurálni.

[Töltse le a \*. zip archívumot](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) , vagy KLÓNOZOTT webes API-projektet a githubról.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a <em>B2C-WebApi/**appSettings. JSON**</em>  fájlt a Visual Studióban vagy a Visual Studio Code-ban.
1. Módosítsa a `AzureAdB2C` blokkot úgy, hogy tükrözze a bérlő nevét, a webes API-alkalmazás alkalmazás-AZONOSÍTÓját, a regisztrációs/bejelentkezési szabályzat nevét és a korábban meghatározott hatóköröket. A blokknak az alábbi példához hasonlóan kell kinéznie (a megfelelő `Tenant` és `ClientId` értékekkel):

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

1. A `ConfigureServices()` metódusban is állítsa be a `jwtOptions.Authority` értéket a következő jogkivonat-kiállító URI-ra.

    Cserélje le a `<your-tenant-name>`t a B2C-bérlő nevére.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. A `Configure()` metódusban konfigurálja a CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Csak Visual Studio) A Megoldáskezelő **Tulajdonságok** területén nyissa meg a *launchSettings. JSON* fájlt, és keresse meg a `iisExpress` blokkot.
1. (Csak Visual Studio) Frissítse a `applicationURL` értéket a *webapi1* alkalmazás egy korábbi lépésben való regisztrálásakor megadott portszámmal. Például:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Egyoldalas alkalmazás konfigurálása

A sorozat [előző oktatóanyagának](active-directory-b2c-tutorials-spa.md) egyoldalas alkalmazása (SPA) Azure ad B2C használ a felhasználói regisztrációhoz és bejelentkezéshez, és meghívja a *frabrikamb2c* demo bérlő által védett ASP.net Core webes API-t.

Ebben a szakaszban egy egyoldalas alkalmazást frissít, hogy meghívja a Azure AD B2C bérlő által védett ASP.NET Core webes API-t, *amelyet a helyi* gépen futtat.

A SPA beállításainak módosítása:

1. Nyissa meg az *index. html* fájlt az előző oktatóanyagban letöltött vagy klónozott [Active Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] projektben.
1. Konfigurálja a mintát a bemutató URI azonosítójának használatával *. olvassa el* a korábban létrehozott hatókört, valamint a webes API URL-címét.
    1. A `appConfig` definícióban cserélje le a `b2cScopes` értéket a hatókör teljes URI-ja (a korábban feljegyzett **hatókör** értékre).
    1. Módosítsa a `webApi` értéket arra a átirányítási URI-ra, amelyet a webes API-alkalmazás egy korábbi lépésben való regisztrálásakor adott hozzá.

    A `appConfig` definíciójának a következő kódrészlethez hasonlóan kell kinéznie (a bérlő nevével a `<your-tenant-name>`helyén):

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

Ha a Visual Studio helyett inkább a `dotnet` parancssori felületet szeretné használni:

1. Nyisson meg egy konzolablak ablakot, és váltson a *\*. csproj* fájlt tartalmazó könyvtárra. Például:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. A webes API-t a `dotnet run`végrehajtásával hozhatja létre és futtathatja.

    Ha az API-t futtatja, a következőhöz hasonló kimenetnek kell megjelennie (az oktatóanyag esetében nyugodtan figyelmen kívül hagyhatja a `NETSDK1059` figyelmeztetéseket):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Az egyoldalas alkalmazás futtatása

1. Nyisson meg egy konzolablak ablakát, és váltson a Node. js-mintát tartalmazó könyvtárra. Például:

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

1. Az alkalmazás megtekintéséhez navigáljon `http://localhost:6420` a böngészőben.
1. Jelentkezzen be az [előző oktatóanyagban](active-directory-b2c-tutorials-spa.md)használt e-mail-cím és jelszó használatával. Sikeres bejelentkezés után a `User 'Your Username' logged-in` üzenetet kell látnia.
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
