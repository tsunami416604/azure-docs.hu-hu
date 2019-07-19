---
title: Oktatóanyag – hozzáférést biztosíthat egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból – Azure Active Directory B2C | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy .NET Core webes API védelmére és meghívására egy egyoldalas alkalmazásból.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cbea29320c896637190766d1b2b60c09f7db5163
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347167"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés biztosítása egy ASP.NET Core webes API-hoz egy egyoldalas alkalmazásból Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy Azure Active Directory (Azure AD) B2C által védett ASP.NET Core webes API-erőforrást egy egyoldalas alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és az [előfeltételeket az oktatóanyagban: Az egyoldalas alkalmazások hitelesítésének engedélyezése a fiókokkal Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md)használatával.

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

A webes API-erőforrásokat regisztrálni kell a bérlőn, mielőtt azok elfogadják és reagálni tudják a védett erőforrásokra vonatkozó kéréseket olyan ügyfélalkalmazások számára, amelyek hozzáférési jogkivonatot mutatnak be.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adja meg az alkalmazás nevét. Például: *webapi1*.
6. A **Web App/web API** belefoglalása és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
7. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:5000`következőt:.
8. Az **alkalmazás-azonosító URI-ja**mezőben adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját, amelyet a webalkalmazás konfigurálásakor használni fog.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási engedélyeket a webes API számára.

1. Válassza az **alkalmazások**, majd a *webapi1*lehetőséget.
2. Válassza a **közzétett hatókörök**elemet.
3. A **hatókör**, `Hello.Read`a és a Leírás mezőbe írja be `Read access to hello`a következőt:.
4. A **hatókör**, `Hello.Write`a és a Leírás mezőbe írja be `Write access to hello`a következőt:.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazás-engedélyeket biztosíthat a webes API-nak.

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy alkalmazásból, meg kell adnia az alkalmazás engedélyeit az API-nak. Az előfeltételként szolgáló oktatóanyagban létrehozott egy webalkalmazást a *webapp1*nevű Azure ad B2Cban. Ezt az alkalmazást használhatja a webes API meghívásához.

1. Válassza az **alkalmazások**, majd a *webapp1*lehetőséget.
2. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
4. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált **Hello. Read** és **Hello. Write** hatóköröket.
5. Kattintson az **OK** gombra.

A **Saját egyoldalas mintaalkalmazás** regisztrálva van a védett **Hello Core API** meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik egyoldalas alkalmazást. Az egyoldalas alkalmazás engedélyezési engedélyt ad a Azure AD B2Ctól a védett webes API eléréséhez.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és hatókörök vannak definiálva, a webes API-kódot a Azure AD B2C bérlő használatára konfigurálja. Ebben az oktatóanyagban egy, a GitHubról letölthető minta .NET Core-webalkalmazást fog konfigurálni. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

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

Ha engedélyezni szeretné, hogy az egyoldalas alkalmazás meghívja a ASP.NET Core webes API-t, engedélyeznie kell a [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. A **Startup.cs** fájlban adja hozzá a CORS-t a `ConfigureServices()` metódushoz.

    ```csharp
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. A **Startup.cs** fájlban konfigurálja a CORS-t a `Configure()` metódusban.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Nyissa meg a **launchSettings.json** fájlt a **Tulajdonságok** területen, keresse meg az **iisSettings** *applicationURL* beállítást, és a portszámot állítsa olyan értékre, amely regisztrálva van az API válasz URL-címére: `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Az egyoldalas alkalmazás konfigurálása

Az egyoldalas alkalmazás Azure AD B2C használ a felhasználói regisztrációhoz, bejelentkezéshez és a védett ASP.NET Core webes API meghívásához. Az egyoldalas alkalmazást a .NET Core webes API meghívásához kell frissítenie.

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

## <a name="run-the-spa-application-and-web-api"></a>A SPA-alkalmazás és a webes API futtatása

A Node. js egyoldalas alkalmazást és a .NET Core webes API-t is futtatnia kell.

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

    A konzolablak megjeleníti a portszámot, ahol az alkalmazás üzemeltetve van.
    
    ```
    Listening on port 6420...
    ```

4. Böngésző használatával navigáljon a címre `http://localhost:6420` az alkalmazás megtekintéséhez.
5. Jelentkezzen be a [felhasználók egyoldalas (JavaScript-) alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-spa.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
6. Kattintson a **Call API**elemre.

Miután regisztrált egy felhasználói fiókot vagy bejelentkezett eggyel, a minta meghívja a védett webes API-t, és kiad egy eredményt.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](tutorial-add-identity-providers.md)
