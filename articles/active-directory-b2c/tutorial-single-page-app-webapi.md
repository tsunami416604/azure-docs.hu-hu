---
title: 'Oktatóanyag: Hozzáférés a ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból'
titleSuffix: Azure AD B2C
description: Ebben az oktatóanyagban megtudhatja, hogyan védheti meg a .NET Core webes API-t az Active Directory B2C használatával, és hogyan hívhatja meg az API-t egyegyoldalas Node.js alkalmazásból.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f6f9ff7bb0d504ecc163f6ce1f87477b1ea9c2d1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186142"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés a ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory B2C (Azure AD B2C) által védett ASP.NET Core web API-erőforrást egy egyoldalas alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API-hoz
> * A minta konfigurálása az alkalmazás használatára

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a lépéseket és előfeltételeket az [Oktatóanyagban: Hitelesítés engedélyezése egyoldalas alkalmazásban az Azure Active Directory B2C használatával.](tutorial-single-page-app.md)
* Visual Studio 2019-es vagy újabb verzió, illetve Visual Studio-kód
* .NET Core 2.2 vagy újabb
* Node.js

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban határozza meg a webes API olvasási és írási engedélyeit is.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Az egyoldalas alkalmazás konfigurálásakor rögzítse a **hatókör hatóköre** `demo.read` alatti értéket egy későbbi lépésben. A teljes hatókör értéke `https://contosob2c.onmicrosoft.com/api/demo.read`hasonló a hoz.

## <a name="grant-permissions"></a>Engedélyek megadása

Egy védett webes API-t egy másik alkalmazásból való hívásához meg kell adnia az alkalmazásnak a webes API-nak.

Az előfeltételként szolgáló oktatóanyagban létrehozott egy *webapp1*nevű webalkalmazást. Ebben az oktatóanyagban úgy konfigurálja az alkalmazást, hogy meghívja az előző szakaszban létrehozott *web API-t, a webapi1-et.*

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az egyoldalas webalkalmazás regisztrálva van a védett webes API hívásához. A felhasználó hitelesíti magát az Azure AD B2C az egyoldalas alkalmazás használatához. Az egyoldalas alkalmazás engedélyezési engedélyt kap az Azure AD B2C-től a védett webes API eléréséhez.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy a webes API regisztrálva van, és a hatókörök definiálva, konfigurálja a webes API-kódot az Azure AD B2C-bérlő használatára. Ebben az oktatóanyagban konfigurálegy minta .NET Core webalkalmazást, amelyet a GitHubról tölt le.

[Töltsön \*le egy .zip archívumot,](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) vagy klónozza a mintawebes API-projektet a GitHubról.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a <em>B2C-WebApi/**appsettings.json** </em> fájlt a Visual Studio vagy a Visual Studio Code alkalmazásban.
1. Módosítsa `AzureAdB2C` a blokkot úgy, hogy az tükrözze a bérlő nevét, a web API-alkalmazás alkalmazásazonosítóját, a regisztrációs/bejelentkezési szabályzat nevét és a korábban definiált hatóköröket. A blokknak a következő példához `Tenant` hasonlóan `ClientId` kell kinéznie (megfelelő és értékekkel):

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

Ahhoz, hogy az egyoldalas alkalmazás meghívja a ASP.NET Core webes API-t, engedélyeznie kell a [CORS-t](https://docs.microsoft.com/aspnet/core/security/cors) a webes API-ban.

1. A *Startup.cs* fájlban adja hozzá a CORS-t a `ConfigureServices()` metódushoz.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. A `ConfigureServices()` metóduson belül `jwtOptions.Authority` is állítsa be az értéket a következő jogkivonat-kiállító URI értékre.

    Cserélje `<your-tenant-name>` le a B2C-bérlő nevét.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. A `Configure()` metódusban konfigurálja a CORS-t.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Csak Visual Studio) A **Megoldáskezelő Tulajdonságok területén** nyissa meg a *launchSettings.json* fájlt, majd keresse meg a `iisExpress` blokkot.
1. (Csak Visual Studio) Frissítse `applicationURL` az értéket a megadott portszámmal, amikor egy korábbi lépésben regisztrálta a *webapi1* alkalmazást. Példa:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Az egyoldalas alkalmazás konfigurálása

Az egyoldalas alkalmazás (SPA) az [előző oktatóanyag](tutorial-single-page-app.md) a sorozatban az Azure AD B2C a felhasználói regisztráció és a bejelentkezés, és meghívja a ASP.NET Core webes API által védett *frabrikamb2c* demo bérlő.

Ebben a szakaszban frissíti az egyoldalas alkalmazást, hogy meghívja az *Azure* AD B2C-bérlő által védett ASP.NET Core web API-t, amelyet a helyi gépen futtat.

A spa beállításainak módosítása:

1. Nyissa meg az *index.html* fájlt az előző oktatóanyagban letöltött vagy klónozott [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] projektben.
1. Konfigurálja a mintát a korábban létrehozott *demo.read* hatókör URI-jával és a webes API URL-címével.
    1. A `appConfig` definícióban cserélje `b2cScopes` le az értéket a hatókör teljes URI-jára (a korábban rögzített **SCOPE** értékre).
    1. Módosítsa `webApi` az értéket a web API-alkalmazás egy korábbi lépésben történő regisztrálásakor hozzáadott átirányítási URI-ra.

    A `appConfig` definíciónak a következő kódblokkhoz hasonlóan kell kinéznie `<your-tenant-name>`(a bérlő nevével a következő helyen):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Az SPA és a webes API futtatása

Végül futtassa mind a ASP.NET Core webes API-t, és a Node.js egyoldalas alkalmazást a helyi gépen. Ezután jelentkezzen be az egyoldalas alkalmazásba, és nyomja meg a gombot a védett API-hoz való kérelem kezdeményezéséhez.

Bár mindkét alkalmazás helyileg fut ebben az oktatóanyagban, az Azure AD B2C biztonságos regisztráció/bejelentkezés, valamint a védett webes API-hoz való hozzáférés engedélyezéséhez.

### <a name="run-the-aspnet-core-web-api"></a>A ASP.NET Core webes API futtatása

A Visual Studio alkalmazásban nyomja le az **F5 billentyűt** a *B2C-WebAPI.sln* megoldás létrehozásához és hibakereséséhez. Amikor a projekt elindul, egy weboldal jelenik meg az alapértelmezett böngészőben, amely bejelenti, hogy a webes API kérésekhez elérhető.

Ha a Visual `dotnet` Studio helyett a CLI-t szeretné használni:

1. Nyisson meg egy konzolablakot, és váltson a * \*.csproj* fájlt tartalmazó könyvtárra. Példa:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. A webes API-t a `dotnet run`végrehajtásával hozd létre és futtassa.

    Ha az API működik, a következőhöz hasonló kimenetet kell látnia (az `NETSDK1059` oktatóanyag esetében nyugodtan figyelmen kívül hagyhatja a figyelmeztetéseket):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Az egyoldalas alkalmazás futtatása

1. Nyisson meg egy konzolablakot, és váltson a Node.js mintát tartalmazó könyvtárra. Példa:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    node server.js
    ```

    A konzolablak az alkalmazás azon portszámát jeleníti meg.

    ```console
    Listening on port 6420...
    ```

1. Keresse `http://localhost:6420` meg a böngészőben az alkalmazás megtekintéséhez.
1. Jelentkezzen be az [előző oktatóanyagban](tutorial-single-page-app.md)használt e-mail címmel és jelszóval. Sikeres bejelentkezéskor látnia kell `User 'Your Username' logged-in` az üzenetet.
1. Válassza a **Webes API hívása** gombot. Az SPA engedélyezési engedélyt kap az Azure AD B2C-től, majd hozzáfér a védett webes API-hoz az indexlap tartalmának megjelenítéséhez:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API-hoz
> * A minta konfigurálása az alkalmazás használatára

Most, hogy látta, hogy egy SPA egy erőforrást kér egy védett webes API-ból, mélyebben megismerheti, hogy ezek az alkalmazástípusok hogyan kommunikálnak egymással és az Azure AD B2C-vel.

> [!div class="nextstepaction"]
> [Az Active Directory B2C >használható alkalmazástípusok](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
