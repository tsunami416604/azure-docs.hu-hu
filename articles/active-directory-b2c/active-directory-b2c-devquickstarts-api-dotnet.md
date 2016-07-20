<properties
    pageTitle="Azure AD B2C előzetes verzió | Microsoft Azure"
    description="Hogyan lehet OAuth 2.0 hitelesítési hozzáférési jogkivonat általi védelemmel és az Azure Active Directory B2C alkalmazásával .NET-alapú webes API-t készíteni?"
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/16/2016"
    ms.author="dastrock"/>

# Azure Active Directory B2C előzetes verzió: .NET webes API készítése

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Az Azure Active Directory (Azure AD) B2C segítségével védetté tehet egy webes API-t OAuth 2.0 hozzáférési jogkivonatok használatával. Ezek a jogkivonatok engedélyezik az Azure AD B2C-t alkalmazó ügyfélalkalmazások számára az API hitelesítését. Ez a cikk ismerteti, hogyan kell olyan .NET MVC (Model-View-Controller) „Feladatlista” alkalmazást létrehozni, amely tartalmazza a felhasználói regisztrációt, bejelentkezést és profilkezelést. Minden felhasználói feladatlistát egy feladatszolgáltatás tárol. Ez a webes API lehetővé teszi a hitelesített felhasználók számára, hogy saját feladatlistájukban feladatokat hozzanak létre és olvassanak el.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD B2C címtár létrehozása

Ahhoz, hogy használni tudja az Azure AD B2C-t, előbb létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és sok minden mást. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne ebben az útmutatóban.

## Alkalmazás létrehozása

A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

- Az alkalmazás tartalmazzon egy **webalkalmazást** vagy **webes API-t**.
- A webalkalmazáshoz használja az **átirányítási egységes erőforrás-azonosítót** `https://localhost:44316/`. Ez a webalkalmazás ügyféloldalának alapértelmezett helye ehhez a kódmintához.
- Másolja be az alkalmazáshoz hozzárendelt **alkalmazásazonosítót**. Erre később még szüksége lesz.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Házirendek létrehozása

Az Azure AD B2C-ben minden felhasználói élményt [házirendek](active-directory-b2c-reference-policies.md) határoznak meg. Az ügyfél ebben a kódmintában három identitásélményt tartalmaz: regisztráció, bejelentkezés és profilszerkesztés. Mindegyik típushoz létre kell hoznia egy házirendet a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) leírtak szerint. A három házirend létrehozásakor ügyeljen arra, hogy:

- Az identitásszolgáltatók paneljén válassza a **User ID sign-up** (Regisztrálás felhasználói azonosítóval) vagy az **Email sign-up** (Regisztrálás e-mail címmel) lehetőséget.
- A regisztrációs házirendben válassza ki a **megjelenítendő nevet** és az egyéb regisztrációs attribútumokat.
- Alkalmazási jogcímnek minden házirend esetén válassza ki a **megjelenítendő nevet** és az **objektumazonosítót**. Kiválaszthat egyéb jogcímeket is.
- Az egyes házirendek létrehozása után másolja be a házirend**nevét**. A házirendek nevére később még szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A három házirend sikeres létrehozása után készen áll az alkalmazás elkészítésére.

## A kód letöltése

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-devquickstarts-bug-fix.md)]

Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). A minta menet közbeni létrehozásához [letöltheti a projektvázát tartalmazó .zip-fájlt](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). A vázprojektet klónozhatja is:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

A kész alkalmazás [.zip fájlként](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) vagy `complete` az adott adattár ágában is elérhető.

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A megoldásfájl két projektet tartalmaz: `TaskWebApp` és `TaskService`. `TaskWebApp` egy MVC-webalkalmazás, amellyel a felhasználó kommunikál. `TaskService` egy webes API háttéralkalmazás, amely tárolja a felhasználók feladatlistáit.

## A feladathoz tartozó webalkalmazás konfigurálása

Ha a felhasználó kommunikál a(z) `TaskWebApp` projekttel, akkor az ügyfél kérelmeket küld az Azure AD-hoz, és megkapja a `TaskService` webes API hívásához használandó jogkivonatokat. A felhasználói bejelentkezéshez és a jogkivonatok megkapásához meg kell adnia `TaskWebApp` bizonyos információkat az alkalmazásról. A(z) `TaskWebApp` projektben nyissa meg a projekt gyökérkönyvtárában található `web.config` fájlt, és cserélje le a(z) `<appSettings>` szakaszban szereplő értékeket:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Két olyan `[PolicyAuthorize]` decorator elem is van, amelyeknél kell adnia a bejelentkezési házirend nevét. A `[PolicyAuthorize]` attribútum indítja a megfelelő házirendet, ha egy felhasználó megpróbál belépni az alkalmazásban egy olyan oldalra, amelyhez hitelesítés szükséges.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

Ha szeretné megtudni, hogy egy olyan webes alkalmazás, mint például a(z) `TaskWebApp`, hogyan alkalmazza az Azure AD B2C-t, nézze meg a [.NET-webalkalmazás készítése](active-directory-b2c-devquickstarts-web-dotnet.md) részt.

## Az API védelme

Ha egy ügyfél hívja az API-t a felhasználók nevében, biztonságossá teheti az API-t `TaskService` az OAuth 2.0 tulajdonosi jogkivonatok használatával. Az API a Microsoft Open Web Interface .NET (OWIN) könyvtárra történő alkalmazásával elfogadhat és érvényesíthet jogkivonatokat.

### Az OWIN telepítése
Első lépésként telepítse az OWIN OAuth hitelesítési folyamatot:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Adja meg a B2C-re vonatkozó információkat
Nyissa meg a(z) `TaskService` projekt gyökérkönyvtárában található `web.config` fájlt, és cserélje le a(z) `<appSettings>` szakaszban szereplő értékeket. Ezeket az értékeket fogja használni a teljes API és OWIN könyvtár.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

### OWIN indítási osztály hozzáadása
A `Startup.cs` nevű `TaskService` projekthez adjon hozzá egy OWIN indítási osztályt.  Kattintson a jobb gombbal a projektre, válassza az **Add** (Hozzáadás), majd a **New Item** (Új elem) lehetőséget, és keresse meg az OWIN elemet.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### OAuth 2.0 típusú hitelesítés konfigurálása
Nyissa meg a(z) `App_Start\Startup.Auth.cs` fájlt, és hajtsa végre a(z) `ConfigureAuth(...)` módszert:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
    private const string discoverySuffix = ".well-known/openid-configuration";

    public void ConfigureAuth(IAppBuilder app)
    {   
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API accepts tokens only from its own clients
            ValidAudience = clientId,
        };

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {   
            // This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
        });
    }
}
```

### Feladatvezérlő védelme
Az alkalmazás OAuth 2.0 típusú hitelesítés használatára történő konfigurálása után, a webes API-t biztonságossá teheti úgy, hogy egy `[Authorize]` címkét ad hozzá a feladatvezérlőhöz. A feladatvezérlőben történik a feladatlisták kezelése, így osztályszinten kell biztonságossá tenni az egész vezérlőt. Az egyes műveletekre vonatkozóan is hozzáadhatja a(z) `[Authorize]` címkét a részletesebb vezérlés érdekében.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### Felhasználói adatok beolvasása a jogkivonatból
`TasksController` tárolja a feladatokat az adatbázisban, ahol minden feladathoz hozzá van rendelve egy felhasználó, aki a feladat „tulajdonosa”. A tulajdonos azonosítása a felhasználói **objektumazonosítóval** történik. (Ezért kellett az alkalmazás jogcímeként objektumazonosítót hozzáadnia minden házirendben.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## Mintaalkalmazás futtatása

Végezetül készítse el és futtassa a következőket: `TaskWebApp` és `TaskService`. Regisztráljon az alkalmazásra e-mail-címmel vagy felhasználónévvel. Hozzon létre feladatokat a felhasználói feladatlistában, és figyelje meg, hogy azok megmaradnak a API-ban azután is, ha leállította, majd újraindította az adott ügyfelet.

## Házirendek szerkesztése

Miután az Azure AD B2C használatával biztonságossá tett egy API-t, kísérletezhet az alkalmazás-házirendek szerkesztésével, és megtekintheti ennek az API-ra gyakorolt hatásait (vagy a hatás hiányát). Módosíthatja <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also --> a házirendekben az alkalmazás jogcímét és a webes API-n hozzáférhető felhasználói adatokat. Minden hozzáadott jogcím elérhető lesz a .NET MVC webes API számára a(z) `ClaimsPrincipal` objektumban az ebben a cikkben leírtak szerint.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Jun16_HO2-->


