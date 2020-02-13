---
title: Bejelentkezés felvétele a Microsofttal ASP.NET Core Web Apps – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan implementálhatja a Microsoft bejelentkezést egy ASP.NET Core webalkalmazásban az OpenID Connect használatával
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c11f7daf68585d63d19fca282ef2f4a306303ac7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160729"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal ASP.NET Core webalkalmazásba

Ebből a rövid útmutatóból megtudhatja, hogy egy ASP.NET Core webalkalmazás hogyan jelentkezhet be a személyes fiókokba (hotmail.com, outlook.com, Others) és munkahelyi és iskolai fiókokkal bármely Azure Active Directory (Azure AD) példányból.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg a [Azure Portal-Alkalmazásregisztrációk](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és a megoldáshoz az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AspNetCore-Quickstart`).
>    - Az **átirányítási URI**-ban adja hozzá a `https://localhost:44321/`elemet, majd kattintson a **regisztráció**elemre.
> 1. Válassza a **hitelesítés** menüt, majd adja hozzá a következő adatokat:
>    - Az **átirányítási URI**-k területen vegyen fel `https://localhost:44321/signin-oidc`, majd válassza a **Mentés**lehetőséget.
>    - A **Speciális beállítások** szakaszban állítsa be `https://localhost:44321/signout-oidc`a **KIJELENTKEZÉSI URL-címet** .
>    - Az **implicit támogatás szakaszban adja**meg az **azonosító jogkivonatokat**.
>    - Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia a válasz URL-címeket `https://localhost:44321/` és `https://localhost:44321/signin-oidc`ként, a kijelentkezési URL-címet pedig `https://localhost:44321/signout-oidc`ként kell hozzáadnia, és az engedélyezési végpont által kiállított azonosító tokeneket kell megadni.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-aspnet-core-project"></a>2\. lépés: a ASP.NET Core-projekt letöltése

- [A Visual Studio 2019-megoldás letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3\. lépés: A Visual Studio-projekt konfigurálása

1. Bontsa ki a zip-fájlt a gyökérmappa egyik helyi mappájába – például **C:\Azure-Samples**
1. Ha a Visual Studio 2019-et használja, nyissa meg a megoldást a Visual Studióban (nem kötelező).
1. Szerkessze a **appSettings. JSON** fájlt. `ClientId` megkeresése és a `ClientId` értékének frissítése a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosító** értékével. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here` – a Azure Portal regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** . Az **alkalmazás (ügyfél) azonosítóját** az alkalmazás **Áttekintés** oldalán találja.
> - `Enter_the_Tenant_Info_Here` – a következő lehetőségek egyike:
>   - Ha az alkalmazás **csak ebben a szervezeti könyvtárban támogatja a fiókokat**, cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>   - Ha az alkalmazás **minden Microsoft-fiókfelhasználót** támogat, ezt az értéket a `common` értékre cserélje le
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

## <a name="more-information"></a>További információ

Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódokról. Ez az Áttekintés hasznos lehet a kód működésének, a fő argumentumok, valamint a meglévő ASP.NET Core alkalmazásba való bejelentkezés hozzáadásának megismeréséhez.

### <a name="startup-class"></a>Indítási osztály

A *Microsoft. AspNetCore. Authentication* middleware a üzemeltetési folyamat inicializálásakor végrehajtandó indítási osztályt használ:

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

A metódus `AddAuthentication` konfigurálja a szolgáltatást cookie-alapú hitelesítés hozzáadására, amely böngésző-forgatókönyvekben használatos, és beállíthatja a kihívást az OpenID Connect használatára. 

A `.AddAzureAd` tartalmazó vonal hozzáadja a Microsoft Identity platform hitelesítését az alkalmazáshoz. Ezt követően a rendszer a Microsoft Identity platform-végpont használatával történő bejelentkezésre van konfigurálva.

> |Ahol  |  |
> |---------|---------|
> | ClientID  | Az alkalmazás (ügyfél) azonosítója a Azure Portal regisztrált alkalmazásban. |
> | Hitelesítésszolgáltató | A hitelesítést végző felhasználó STS-végpontja. Ez általában <https://login.microsoftonline.com/{tenant}/v2.0> a nyilvános felhőben, ahol a (z) {bérlő} a bérlő vagy a bérlői azonosító neve, vagy *közös* a közös végpontra (több-bérlős alkalmazásokhoz használt) való hivatkozásra. |
> | TokenValidationParameters | A jogkivonatok érvényesítéséhez használatos paraméterek listája. Ebben az esetben a `ValidateIssuer` `false`re van beállítva, hogy jelezze, hogy képes-e a bejelentkezések fogadására bármely személyes vagy munkahelyi vagy iskolai fiókból. |


> [!NOTE]
> A `ValidateIssuer = false` beállítása a rövid útmutató egyszerűsítése. A valós alkalmazásokban ellenőriznie kell a kiállítót.
> Ennek megértéséhez tekintse meg a mintákat.

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

A `[Authorize]` attribútum használatával védelemmel láthatja el a vezérlőket vagy a vezérlő metódusait. Ez az attribútum csak a hitelesített felhasználók engedélyezésével korlátozza a vezérlőhöz vagy a metódusokhoz való hozzáférést, ami azt jelenti, hogy a hitelesítési kérdés elindítható a vezérlőhöz, ha a felhasználó nem hitelesítve van.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Tekintse meg a GitHub-tárházat ehhez a ASP.NET Core oktatóanyaghoz, amely útmutatást nyújt a hitelesítés új ASP.NET Core webalkalmazáshoz való hozzáadásával, a Microsoft Graph és más Microsoft API-k meghívásával, a saját API-k meghívásával, a Hozzáadás módjával kapcsolatban. engedélyezés, a felhasználók bejelentkezésének módja a nemzeti felhőkben vagy a közösségi identitások és egyebek:

> [!div class="nextstepaction"]
> [ASP.NET Core webalkalmazás-oktatóanyag](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)