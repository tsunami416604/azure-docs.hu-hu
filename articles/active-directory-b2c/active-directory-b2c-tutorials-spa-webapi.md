---
title: Oktatóanyag – ASP.NET Core webes API-hoz való hozzáférés engedélyezése egy egyoldalas alkalmazásból az Azure Active Directory B2C használatával | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Active Directory B2C-t egy .NET Core webes API védelmére és meghívására egy egyoldalas alkalmazásból.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: a2fbdebfc800c33a99b19b366209aeabb03fe115
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590833"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Oktatóanyag: ASP.NET Core webes API-hoz való hozzáférés engedélyezése egy egyoldalas alkalmazásból az Azure Active Directory B2C használatával

Az oktatóanyag azt mutatja be, hogyan hívhat meg egy Azure Active Directory (Azure AD) B2C-vel védett ASP.NET Core webes API-t egy egyoldalas alkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API regisztrálása az Azure AD B2C-bérlőben
> * Webes API hatóköreinek meghatározása és konfigurálása
> * Alkalmazásengedélyek megadása a webes API számára
> * Mintakód frissítése egy webes API Azure AD B2C-vel történő védelméhez

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a [felhasználók egyoldalas alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-spa.md) foglalkozó oktatóanyagot.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **ASP.NET és webfejlesztési** számítási feladattal.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) vagy újabb
* [Node.js](https://nodejs.org/en/download/) telepítése

## <a name="register-web-api"></a>Webes API regisztrálása

A webes API-erőforrásoknak regisztrálva kell lenniük a bérlőben, mielőtt fogadni és válaszolni tudnának az [ügyfélalkalmazások](../active-directory/develop/developer-glossary.md#client-application) által leadott, [védett erőforrásokra vonatkozó kérelmekre](../active-directory/develop/developer-glossary.md#resource-server), amelyekhez egy, az Azure Active Directoryból származó [hozzáférési jogkivonat](../active-directory/develop/developer-glossary.md#access-token) tartozik. A regisztráció meghatározza az [alkalmazás- és szolgáltatásnév-objektumot](../active-directory/develop/developer-glossary.md#application-object) a bérlőben. 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Az Azure Portalon válassza az **Azure AD B2C** lehetőséget a szolgáltatások listájából.

2. A B2C beállításaiban kattintson az **Alkalmazások**, majd a **Hozzáadás** lehetőségre.

    A mintául szolgáló webes API bérlőben történő regisztrálásához használja a következő beállításokat.
    
    ![Új API hozzáadása](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | Hello Core API | Adjon meg egy olyan **nevet**, amely megfelelően körülírja a webes API-t a felhasználók számára. |
    | **Webalkalmazás vagy webes API szerepeltetése** | Igen | Válassza az **Igen** lehetőséget a webes API-k esetén. |
    | **Implicit folyamat engedélyezése** | Igen | Válassza az **Igen** lehetőséget, mivel az API [OpenID Connect bejelentkezést](active-directory-b2c-reference-oidc.md) használ. |
    | **Válasz URL-cím** | `http://localhost:44332` | A válasz URL-címek olyan végpontok, amelyeken keresztül az Azure AD B2C visszaadja az API által kért jogkivonatokat. Ebben az oktatóanyagban a mintául szolgáló webes API helyileg fut (localhost), és az 5000-es porton figyel. |
    | **Alkalmazásazonosító URI** | HelloCoreAPI | Az URI egyedileg azonosítja az API-t a bérlőben. Ez lehetővé teszi, hogy bérlőnként több API-t is regisztráljon. A [hatókörök](../active-directory/develop/developer-glossary.md#scopes) szabályozzák a hozzáférést a védett API-erőforrásokhoz, és alkalmazásazonosító URI-nként vannak meghatározva. |
    | **Natív ügyfél** | Nem | Mivel ez egy webes API, nem pedig egy natív ügyfél, válassza a Nem lehetőséget. |
    
3. Kattintson a **Létrehozás** gombra az API regisztrálásához.

A regisztrált API-k az Azure AD B2C-bérlő alkalmazásainak listájában jelennek meg. Válassza ki webes API-ját a listáról. Ekkor megjelenik a webes API tulajdonságpanelje.

![Webes API – Tulajdonságok](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Jegyezze fel az **alkalmazás ügyfél-azonosítóját**. Az azonosító egyedi módon azonosítja az API-t, és az oktatóanyag későbbi részében, az alkalmazás konfigurálásakor lesz rá szükség.

A webes API Azure AD B2C-vel végzett regisztrációja egy megbízhatósági kapcsolatot határoz meg. Mivel az API B2C-vel van regisztrálva, megbízhat a más alkalmazásoktól kapott B2C hozzáférési jogkivonatokban.

## <a name="define-and-configure-scopes"></a>Hatókörök meghatározása és konfigurálása

A [hatókörök](../active-directory/develop/developer-glossary.md#scopes) lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. Egyes felhasználók például rendelkezhetnek olvasási és írási hozzáféréssel is, míg más felhasználóknak csak olvasási engedélye lehet. Ebben az oktatóanyagban meghatározzuk az olvasási engedélyeket a webes API számára.

### <a name="define-scopes-for-the-web-api"></a>A webes API hatóköreinek meghatározása

A regisztrált API-k az Azure AD B2C-bérlő alkalmazásainak listájában jelennek meg. Válassza ki webes API-ját a listáról. Ekkor megjelenik a webes API tulajdonságpanelje.

Kattintson a **Közzétett hatókörök (előzetes verzió)** gombra.

Az API hatóköreinek konfigurálásához adja meg a következő bejegyzéseket. 

![a webes API-ban meghatározott hatókörök](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Beállítás      | Ajánlott érték  | Leírás                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Hatókör** | demo.read | Olvasási hozzáférés a bemutató API-hoz |

Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazás-engedélyeket biztosíthat a webes API-nak.

### <a name="grant-app-permissions-to-web-api"></a>Alkalmazásengedélyek megadása a webes API-nak

Egy védett webes API alkalmazásból történő hívásához alkalmazásengedélyeket kell biztosítania az API számára. Ebben az oktatóanyagban használja a [felhasználók egyoldalas (JavaScript-) alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-spa.md) foglalkozó oktatóanyagban létrehozott egyoldalas alkalmazást.

1. Válassza ki az Azure Portalon az **Azure AD B2C** elemet a szolgáltatások listájáról, majd kattintson az **Alkalmazások** lehetőségre a regisztrált alkalmazások listájának megjelenítéséhez.

2. Válassza ki a **Saját egyoldalas mintaalkalmazás** elemet az alkalmazáslistából, és kattintson az **API-hozzáférés (előzetes verzió)**, majd a **Hozzáadás** gombra.

3. Az **API kiválasztása** legördülő menüben válassza ki a regisztrált **Hello Core API** webes API-t.

4. A **Hatókörök kiválasztása** legördülő menüben válassza ki azt a hatókört, amelyet a webes API regisztrációja során megadott.

    ![alkalmazás hatóköreinek kiválasztása](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Kattintson az **OK** gombra.

A **Saját egyoldalas mintaalkalmazás** regisztrálva van a védett **Hello Core API** meghívásához. A WPF asztali alkalmazás használatához a felhasználó az Azure AD B2C-vel [hitelesíti magát](../active-directory/develop/developer-glossary.md#authentication). Az asztali alkalmazás lekéri az [engedélyezést](../active-directory/develop/developer-glossary.md#authorization-grant) az Azure AD B2C-ből a védett webes API-hoz való hozzáféréshez.

## <a name="update-code"></a>Kód frissítése

Most, hogy regisztrálta a webes API-t és meghatározta a hatóköröket, konfigurálnia kell a webes API kódját az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban egy, a GitHubról letölthető, mintául szolgáló .NET Core-webalkalmazást fog konfigurálni. 

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

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
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>CORS engedélyezése

Ahhoz, hogy az egyoldalas alkalmazás meghívhassa az ASP.NET Core webes API-t, engedélyeznie kell a [CORS](https://docs.microsoft.com/aspnet/core/security/cors) mechanizmust.

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

3. A **Tulajdonságok** területen nyissa meg a **launchSettings.json** fájlt, keresse meg az *applicationURL* beállítást, és jegyezze fel az értéket a következő szakaszhoz.

### <a name="configure-the-single-page-app"></a>Az egyoldalas alkalmazás konfigurálása

Az egyoldalas alkalmazás Azure AD B2C-t használ a felhasználók regisztrációjához és bejelentkeztetéséhez, és meghívja a védett ASP.NET Core webes API-t. Frissítenie kell a .NET Core webes API-t hívó egyoldalas alkalmazást.
Az alkalmazás beállításainak módosításához:

1. Nyissa meg az `index.html` fájlt az egyoldalas Node.js-mintaalkalmazásban.
2. Konfigurálja a mintát az Azure AD B2C-bérlő regisztrációs információival. Az alábbi kódban adja hozzá a bérlő nevét a **b2cScopes** paraméterhez, és módosítsa a **webApi** értékét a korábban feljegyzett *applicationURL* értékre:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:64791/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Az SPA-alkalmazás és a webes API futtatása

Az egyoldalas Node.js-alkalmazást és a .NET Core webes API-t is futtatnia kell.

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

    A konzolablakban megjelenik annak a portnak a száma, amelynél az alkalmazás található.
    
    ```
    Listening on port 6420...
    ```

4. Egy böngészőben lépjen a `http://localhost:6420` címre az alkalmazás megtekintéséhez.
5. Jelentkezzen be a [felhasználók egyoldalas (JavaScript-) alkalmazáson belüli, az Azure Active Directory B2C-vel történő hitelesítésével](active-directory-b2c-tutorials-spa.md) foglalkozó oktatóanyagban használt e-mail-címmel és jelszóval.
6. Kattintson az **API meghívása** gombra.

Miután regisztrált egy felhasználói fiókot vagy bejelentkezett eggyel, a minta meghívja a védett webes API-t, és kiad egy eredményt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C-oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ez a cikk azon vezette végig, hogyan biztosíthat védelmet egy webes API-nak különböző hatókörök Azure AD B2C-ben való regisztrálásával és meghatározásával. További információkért tekintse át az elérhető Azure AD B2C-kódmintákat.

> [!div class="nextstepaction"]
> [Azure AD B2C-kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
