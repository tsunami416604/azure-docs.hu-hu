---
title: Bejelentkezés hozzáadása a Microsofttal ASP.NET Core webalkalmazásokhoz – Microsoft identity platform | Azure
description: A Microsoft bejelentkezés megvalósítása ASP.NET Core Web App alkalmazáson az OpenID Connect használatával
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2d604da58f4056fc718239b6bc019d61ddbee51b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883831"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Rövid útmutató: Bejelentkezés hozzáadása a Microsofttal egy ASP.NET Core webalkalmazáshoz
Ebben a rövid útmutatóban egy kódminta segítségével megtudhatja, hogy egy ASP.NET Core webalkalmazás hogyan tud bejelentkezni a személyes fiókokba (hotmail.com, outlook.com, mások) és munkahelyi és iskolai fiókokbármely Azure Active Directory (Azure AD) példányból. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy illusztrációhoz.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az [Azure Portal – Alkalmazásregisztrációk.](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AspNetCore-Quickstart`).
>    - Az **Átirányítás**URI `https://localhost:44321/`területén adja hozzá a összeadást, és válassza a **Regisztráció**lehetőséget.
> 1. Válassza a **Hitelesítés** menüt, majd adja meg a következő adatokat:
>    - Az **Irányított URI-k átirányítása**csoportban adja hozzá a lehetőséget, `https://localhost:44321/signin-oidc`és válassza a Mentés **lehetőséget.**
>    - A **Speciális beállítások** csoportban állítsa `https://localhost:44321/signout-oidc`a **Kijelentkezés URL-címét** a-ra.
>    - Az **Implicit engedély** területen jelölje be az **azonosító jogkivonatok** elemet.
>    - Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a gyorsindítás kódmintája működjön, hozzá `https://localhost:44321/` `https://localhost:44321/signin-oidc`kell adnia a `https://localhost:44321/signout-oidc`válasz URL-címeket, és hozzá kell adnia a kijelentkezési URL-címet , és kérnie kell az engedélyezési végpont által kibocsátandó azonosító jogkivonatokat.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-aspnet-core-project"></a>2. lépés: Töltse le a ASP.NET Core projekt

> [!div renderon="docs"]
> [A Visual Studio 2019-es megoldás letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet a Visual Studio 2019 használatával.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: Az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk, és készen áll a futtatásra. 
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt futtatása
> 1. A zip-fájl kibontása a gyökérmappán belüli helyi mappába – például **C:\Azure-Samples**
> 1. A megoldás megnyitása a Visual Studióban 
> 1. Az **appsettings.json fájl szerkesztése.** Keresse `ClientId` meg és `ClientId` frissítse a regisztrált alkalmazás **alkalmazásazonosító-értékével.** 
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here`- az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója(ügyfélazonosítója).** **Az alkalmazás (ügyfél) azonosítóját** az alkalmazás **áttekintése** lapon találja.
> - `Enter_the_Tenant_Info_Here`- az alábbi lehetőségek egyike:
>   - Ha az alkalmazás **csak ebben a szervezeti címtárban támogatja a fiókokat,** cserélje le ezt az értéket a **bérlői azonosító** vagy a bérlő **nevére** (például contoso.microsoft.com)
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>   - Ha az alkalmazás **minden Microsoft-fiókfelhasználót** támogat, ezt az értéket a `common` értékre cserélje le
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

## <a name="more-information"></a>További információ

Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódról. Ez az áttekintés hasznos lehet a kód működésének megértéséhez, a fő argumentumok, és akkor is, ha egy meglévő ASP.NET Core alkalmazáshoz szeretne bejelentkezést hozzáadni.

### <a name="how-the-sample-works"></a>A minta működése
![Megmutatja, hogyan működik az ez a rövid útmutató által létrehozott mintaalkalmazás](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Indítási osztály

*A Microsoft.AspNetCore.Authentication* middleware olyan indítási osztályt használ, amely az üzemeltetési folyamat inicializálásakor kerül végrehajtásra:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

A `AddAuthentication` módszer úgy konfigurálja a szolgáltatást, hogy cookie-alapú hitelesítést adjon hozzá, amelyet a böngészőforgatókönyvekben használnak, és állítsa be a kihívást az OpenID Connect beállítására. 

A Microsoft-identitásplatform-hitelesítést tartalmazó `.AddAzureAd` sor hozzáadja az alkalmazáshoz. Ezután úgy van beállítva, hogy a Microsoft identity platform végponthasználatával jelentkezzen be.

> |Ahol  |  |
> |---------|---------|
> | ClientID  | Alkalmazás (ügyfél) azonosítóaz Azure portalon regisztrált alkalmazásból. |
> | Authority | A felhasználó hitelesítéséhez szükséges STS-végpont. Ez általában <https://login.microsoftonline.com/{tenant}/v2.0> nyilvános felhőhöz van, ahol a {tenant} a bérlő vagy a bérlőazonosító neve, vagy *gyakori* a közös végpontra való hivatkozáshoz (több-bérlős alkalmazásokhoz használatos) |
> | TokenValidationParameters | A jogkivonatok érvényesítéséhez használatos paraméterek listája. Ebben az `ValidateIssuer` esetben van `false` beállítva, hogy jelezze, hogy képes elfogadni bejelentkezések bármely személyes, munkahelyi vagy iskolai fiókok. |


> [!NOTE]
> A `ValidateIssuer = false` beállítás a rövid útmutató egyszerűsítése. A valós alkalmazásokban ellenőrizni kell a kibocsátót.
> Lásd a mintákat, hogy megértsük, hogyan kell csinálni.

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

Az attribútum segítségével megvédheti a `[Authorize]` vezérlő- vagy vezérlőmetódusokat. Ez az attribútum korlátozza a hozzáférést a vezérlőhöz vagy metódusokhoz azáltal, hogy csak hitelesített felhasználókszámára engedélyezi, ami azt jelenti, hogy a hitelesítési kihívás elindítható a vezérlő eléréséhez, ha a felhasználó nincs hitelesítve.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Tekintse meg a GitHub-tárházban a ASP.NET Core oktatóanyagát, amely további információt tartalmaz, beleértve a hitelesítés hozzáadásának módját egy vadonatúj ASP.NET Core webalkalmazáshoz, hogyan hívhatja meg a Microsoft Graph-ot és más Microsoft API-kat, hogyan hívhatja meg saját API-jait, hogyan adhat hozzá engedélyezést, hogyan jelentkeztetheti be a felhasználókat a nemzeti felhőkben, vagy hogyan lehet bejelentkezni a nemzeti felhőkben, vagy közösségi identitásokkal és egyebek :

> [!div class="nextstepaction"]
> [ASP.NET Core Web App oktatóanyaga](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
