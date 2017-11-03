---
title: "Bejelentkezés hozzáadása egy .NET MVC webes API-k használata az Azure AD v2.0-végponttól |} Microsoft Docs"
description: "Hogyan hozhat létre egy .NET MVC webes API-t, amely mindkét személyes Microsoft Account jogkivonatokat fogad el és a munkahelyi vagy iskolai fiókját."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: bb332196aef59a497a1f80b67ab5c1eeef3dee0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="secure-an-mvc-web-api"></a>Egy MVC webes API biztonságossá tétele
Az Azure Active Directoryban a v2.0-végpontra, megvédheti a Web API használatával [OAuth 2.0](active-directory-v2-protocols.md) hozzáférési jogkivonatok, mindkét személyes Microsoft-fiókkal rendelkező felhasználók és a munkahelyi vagy iskolai fiókok biztonságos hozzáférés a webes API.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

ASP.NET webes API-k Ez elvégezhető a .NET-keretrendszer 4.5 része a Microsoft OWIN köztes használatával.  Itt hozhat létre egy "Teendőlista" MVC webes API-t, amely lehetővé teszi az ügyfelek létrehozásához, és feladatokat beolvasni a felhasználói feladatlistában OWIN fogjuk használni.  A webes API-t, hogy a bejövő kérelmek tartalmaznak egy érvényes hozzáférési jogkivonatot, és bármilyen kérelmeket, amelyek nem teljesíti az ellenőrző egy védett útvonal fogja ellenőrizni.  Ez a minta a Visual Studio 2015 használatával lett létrehozva.

## <a name="download"></a>Letöltés
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Követéséhez is [töltse le az alkalmazás vázát egy .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) vagy klónozza a vázat:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Az üres alkalmazás egyszerű API-t a bolierplate kódot tartalmaz, de hiányzik az identitás-kapcsolódó darab mindegyikét. Ha nem szeretné követéséhez, hanem klónozhat vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Hozzon létre egy új alkalmazást [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy kövesse az alábbi [részletes lépéseket](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Másolja le a **alkalmazásazonosító** be az alkalmazáshoz hozzárendelt szüksége lehet rájuk hamarosan.

A visual studio megoldás is tartalmaz egy "TodoListClient", amely egy egyszerű WPF-alkalmazás.  A TodoListClient bemutatják, hogyan egy felhasználó bejelentkezik, és hogyan ügyfél is kérelmeket kiadni a Web API segítségével.  Ebben az esetben a TodoListClient, mind a TodoListService jelölik ugyanahhoz az alkalmazáshoz.  A TodoListClient konfigurálásához el a következőket is:

* Adja hozzá a **Mobile** platform az alkalmazásra vonatkozóan.

## <a name="install-owin"></a>Az OWIN telepítése
Most, hogy egy alkalmazás regisztrálása kell ellenőrizni fogja a bejövő kérések és a tokeneket a v2.0-végpontra kommunikálni az alkalmazás beállítása.

* Első lépésként nyissa meg a megoldást, és az OWIN köztes NuGet-csomagok hozzáadása a TodoListService projekthez a Csomagkezelő konzol használatával.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>OAuth-hitelesítés konfigurálása
* Egy OWIN indítási osztály hozzáadása a TodoListService projekt neve `Startup.cs`.  Kattintson a jobb gombbal a projektre--> **Hozzáadás** --> **új elem** --> "OWIN" keresése.  Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.
* Módosítsa az osztálydeklaráció való `public partial class Startup` -azt korábban már megvalósított Ez az osztály tartozik, egy másik fájlban.  Az a `Configuration(…)` metódus hívása ConfgureAuth(...) hitelesítés a webalkalmazás beállítása legyen.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Nyissa meg a fájlt `App_Start\Startup.Auth.cs` és megvalósítását a `ConfigureAuth(…)` metódus, amely elfogadja a v2.0-végpontra származó jogkivonatokat a Web API beállításához.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Mostantól a `[Authorize]` attribútumok a tartományvezérlők és az OAuth 2.0 tulajdonosi hitelesítéssel műveletek védelme érdekében.  Adja a `Controllers\TodoListController.cs` osztály engedélyezés címke használatával.  Ezzel kikényszeríti a felhasználót, hogy jelentkezzen be a lap elérése előtt.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Amikor egy jogosult hívó sikeresen hív meg, egy a `TodoListController` API-k, a művelet módosítania kell információkhoz juthat a hívóról.  A jogcímek belül a tulajdonosi jogkivonattal keresztül hozzáférést biztosít az OWIN a `ClaimsPrincipal` objektum.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Végül nyissa meg a `web.config` a TodoListService projekt gyökérkönyvtárában található fájlt, és adja meg a konfigurációs értékeit a `<appSettings>` szakasz.
  * A `ida:Audience` van a **alkalmazásazonosító** az alkalmazás a portálon megadott.

## <a name="configure-the-client-app"></a>Az ügyfélalkalmazás konfigurálása
Ahhoz, hogy a művelet a teendőlista lista szolgáltatás, a teendőlista lista ügyfél konfigurálása, hogy azok tokenek beszerzése a v2.0-végpontra és a szolgáltatás meghíváshoz kell.

* A TodoListClient projektben nyissa meg a `App.config` , és írja be a konfigurációs értékeit a `<appSettings>` szakasz.
  * A `ida:ClientId` alkalmazásazonosító másolta a portálról.

Végezetül tiszta, felépítéséhez, és minden olyan projekthez futtatásához!  Most már rendelkezik egy .NET MVC webes API-t, amely fogadja a személyes Microsoft-fiókot is származó jogkivonatokat és a munkahelyi vagy iskolai fiókját.  Jelentkezzen be a TodoListClient, és hívja meg a webes api tevékenységek hozzáadása a felhasználói feladatlistában.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [is letöltheti a .zip Itt](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), vagy a Githubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Következő lépések
Kiegészítő témakörök most már továbbléphet.  Előfordulhat, hogy ki szeretné próbálni:

[Webes API-k egy webalkalmazásból hívja >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

További forrásokért tekintse meg:

* [A v2.0 – útmutató fejlesztőknek >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-active-directory" címke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.
