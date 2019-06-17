---
title: Asztali alkalmazás, amely meghívja a webes API-k (tokenbeolvasás az alkalmazás) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy asztali alkalmazás, amely meghívja a webes API-kat (az alkalmazás egy token beszerzése |})
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecf5b874345a94e8fd3d3a0783f8e48c7484377d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111256"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Asztali alkalmazás, amely meghívja a webes API - jogkivonat beszerzése

Miután létrehozott, `IPublicClientApplication`, majd fogjuk a webes API-hívás egy token beszerzéséhez használni.

## <a name="recommended-pattern"></a>Javasolt minta

Határozza meg a webes API a `scopes`. Függetlenül a felhasználói élményt, adja meg az alkalmazásban, a minta, amelyek igénybe szeretnék a következő:

- Rendszeresen próbál egy token beszerzése a token a gyorsítótárból meghívásával `AcquireTokenSilent`
- Ha ez a hívás sikertelen, a `AcquireToken` folyamatot, amely a használni kívánt (Itt által képviselt `AcquireTokenXX`)

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

Itt van most részletes szerzi be a jogkivonatokat az asztali alkalmazások hibakeresésének módjai

## <a name="acquiring-a-token-interactively"></a>Interaktív módon tokenbeolvasás

Az alábbi példa bemutatja a jogkivonat beszerzéséhez interaktív módon a felhasználó profilját, a Microsoft Graph olvasásához csak minimális mennyiségű kódra.

```CSharp
string[] scopes = new string["user.read"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Kötelező paraméterek

`AcquireTokenInteractive` csak egy kötelező paraméter ``scopes``, amely tartalmazza a karakterláncok, amelyek meghatározzák a hatókörök, ami egy token szükség egy enumerálás. Ha a jogkivonat a Microsoft Graph, a szükséges hatókörök található minden Microsoft graph API az api-referencia a "Engedélyek" nevű szakaszban. Például, hogy [listázása a felhasználók partneradatainak](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), a hatókör "User.Read", "Contacts.Read" kell használni. Lásd még: [Microsoft Graph-engedélyek referencia](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Az Android, a szülő-tevékenységet is meg kell (használatával `.WithParentActivityOrWindow`, lásd alább), hogy a jogkivonat visszakap szülő tevékenységre a kapcsolati után. Ha nem adja meg azt, egy, a rendszer hibajelzést hívásakor `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Adott választható paraméterek:

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Folyamatban van az interaktív, felhasználói felület fontos. `AcquireTokenInteractive` egy adott nem kötelező paraméter, támogató platformok megadásához engedélyezése rendelkezik a szülő felhasználói felületén. Amikor egy asztali alkalmazás esetében használt `.WithParentActivityOrWindow` platformtól függően különböző type tulajdonság tartozik:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Megjegyzés:

- A .NET Standard, a várt `object` van egy `Activity` Android rendszeren egy `UIViewController` az iOS-egy `NSWindow` Mac gépeken, és a egy `IWin32Window` vagy `IntPr` a Windows.
- A Windows, meg kell hívni `AcquireTokenInteractive` a felhasználói felületről hozzászóláslánc úgy, hogy a beágyazott böngésző lekérdezi a megfelelő felhasználói felület szinkronizálási környezetet.  Nem hívja meg a felhasználói felület szálával, előfordulhat, hogy üzeneteket nem megfelelően pump és/vagy a kölcsönös kizárás forgatókönyvek a felhasználói felületen keresztül. Egyik módszer az MSAL hívása a felhasználói felület szálával, ha nem a felhasználói felület szálán már, hogy használja a `Dispatcher` a WPF.
- WPF-, egy ablakban beolvasni a WPF vezérlőelem használja, ha `WindowInteropHelper.Handle` osztály. A hívás nem ezt követően a WPF vezérlőről (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` Adjon meg egy parancssort a felhasználó interaktivitási irányítható

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő állandókat határozza meg:

- ``SelectAccount``: kényszeríti az STS-re, amelyhez a felhasználónak van egy munkamenet fiókjait tartalmazó fiók kiválasztása párbeszédpanel megjelenítéséhez. Ez a beállítás akkor hasznos, ha az alkalmazás fejlesztői mindenképpen tudni szeretnének, hogy a felhasználó választhat a különböző identitások. Ez a beállítás meghajtók küldése MSAL ``prompt=select_account`` segítségével az identitásszolgáltatókhoz. Ez a beállítás az alapértelmezett, és hajtja végre, hogy a lehető legjobb élményt, (fiók, a munkamenet a felhasználó, és így tovább jelenléte a rendelkezésre álló információk alapján érdemes feladat. ...). Nem módosítja a jó oka, hacsak.
- ``Consent``: lehetővé teszi az alkalmazások kényszerítik a felhasználót a fejlesztő felszólítja beleegyezését, akkor is, ha a jóváhagyás előtt nyújtott. Ebben az esetben az MSAL küld `prompt=consent` segítségével az identitásszolgáltatókhoz. Ez a beállítás használható az egyes biztonsági a fókuszban lévő alkalmazások ahol a szervezet cégirányítási megköveteli, hogy a felhasználó egyike a beleegyezés párbeszédpanelen minden alkalommal, amikor az alkalmazás használata.
- ``ForceLogin``: lehetővé teszi, hogy az alkalmazás fejlesztője, a felhasználói hitelesítő adatokat kér a szolgáltatás által akkor is, ha a felhasználói kérés nem lenne szükség. Ezt a beállítást akkor lehet hasznos, ha egy token beszerzése sikertelen, a felhasználók újra-regisztrálási a. Ebben az esetben az MSAL küld `prompt=login` segítségével az identitásszolgáltatókhoz. Ismét megtudtuk, azt az egyes a fókuszban lévő biztonsági alkalmazások használják, ahol a szervezet cégirányítási követelményeket, hogy a felhasználó relogs – a alkotórészével alkalmazások férhetnek hozzá.
- ``Never`` (a .NET 4.5-ös és csak a WinRT) nem fog kérni a felhasználót, de ehelyett megpróbálja rejtett beágyazott webes nézetében tárolt cookie-t használja (lásd alább: Webes nézetek az MSAL.NET). Ezt a lehetőséget sikertelen lehet, és ebben az esetben `AcquireTokenInteractive` kivételt, hogy a felhasználói felületre vonatkozó interakciót van szükség, és használjon a helyrendsze kell értesíteni `Prompt` paraméter.
- ``NoPrompt``: Az identitásszolgáltató nem bármely kérés küldése. A beállítás csak akkor hasznos, ha szerkesztési szabályzatok Azure AD B2C-vel (lásd: [B2C tulajdonságairól](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ez a módosító szerepel ahol azt szeretné, hogy a felhasználót, hogy előre beleegyezik abba, hogy előre a számos erőforrás speciális forgatókönyv (és nem szeretné használni, általában használt az MSAL.NET növekményes beleegyezése és a Microsoft identity platform v2.0). További részletekért lásd: [Útmutató: a felhasználó több erőforrás vonatkozó díjak előzetes beleegyezés](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi egy indításkiterjesztési pont

`WithCustomWebUi` egy indításkiterjesztési pont, amely lehetővé teszi, hogy megadta a saját nyilvános ügyfélalkalmazások számára a felhasználói felület, és haladjon végig a /Authorize végpont az identitásszolgáltatóval, és lehetővé teszik a felhasználók őket jelentkezzen be, és hozzájárul. MSAL.NET is, majd a hitelesítési kód váltható be és a egy token beszerzéséhez. Ha például szolgál a Visual Studióban elektronok, amelyek az alkalmazások (például és visszajelzés) adja meg a webes interakció, de hagyja, hogy a legtöbb munkát MSAL.NET rendelkezik. Is használhatja, ha lehetővé szeretné tenni UI automation. Nyilvános ügyfélalkalmazások MSAL.NET használja a PKCE standard ([RFC 7636 - kód Exchange OAuth nyilvános ügyfelek által a koncepció kulcs](https://tools.ietf.org/html/rfc7636)) biztosítják, hogy biztonsági van: Csak az MSAL.NET beválthatja a kódot.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>WithCustomWebUi használata

Használatához `.WithCustomWebUI`, kell tennie:
  
  1. Alkalmazzon a `ICustomWebUi` interface (lásd: [Itt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Alapvetően egy módszert kell `AcquireAuthorizationCodeAsync` fogadja az engedélyezési kód URL-címe (argumentumon történő MSAL.NET) szerint haladjon végig az identitásszolgáltató-szal és majd visszatérő biztonsági másolatot az URL-címet, amely szerint az identitásszolgáltató volna a megvalósítás vissza (beleértve az engedélyezési kód) rendelkezik nevezik. Ha problémákba ütközik, a megvalósítás kell throw egy `MsalExtensionException` kivétel MSAL szépen együttműködni.
  2. Az a `AcquireTokenInteractive` hívás, használhat `.WithCustomUI()` átadása az egyéni példánya módosító webes felhasználói felülete

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Példák a teszt Automation - SeleniumWebUI ICustomWebUi megvalósítása

Az MSAL.NET csapat a bővítési mechanizmust kihasználhatja a felhasználói felület tesztek rendelkezik átírása. Abban az esetben, ha érdeklődne rendelkezhet egy pillantást a [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) az MSAL.NET forráskódban osztályban

#### <a name="other-optional-parameters"></a>Más választható paraméterek:

További információ az összes többi nem kötelező paraméter a `AcquireTokenInteractive` az a dokumentáció a [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés

Ha egy tartományi felhasználó jelentkezzen be a tartomány vagy az Azure AD a csatlakoztatott gép, kell használnia:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Korlátozások

- Csak akkor használható AcquireTokenByIntegratedWindowsAuth (IWA) **az összevont** csak a felhasználókra, amely, a felhasználók számára létrehozott egy Active Directory és az Azure Active Directory által támogatott. Közvetlenül az aad-ben, AD biztonsági - nélkül létrehozott felhasználók **felügyelt** felhasználók – Ez a hitelesítési folyamat nem használható. Ez a korlátozás nem érinti a felhasználónév/jelszó folyamatot.
- IWA van a .NET-keretrendszer, a .NET Core és a UWP platformokra írt alkalmazásokat
- IWA nem kerüli a többtényezős hitelesítés (többtényezős hitelesítést). Ha a többtényezős hitelesítés van konfigurálva, IWA meghiúsulhat, ha az MFA-hitelesítést szükség, mert MFA felhasználói beavatkozást igényel.
  > [!NOTE]
  > Ez azonos a bonyolult feladat. IWA nem interaktív, de 2FA felhasználói – interaktivitási igényel. Nem, szabályozzák, ha az identitásszolgáltató kér 2FA kell elvégezni, a bérlői rendszergazda does. A megfigyelések a 2FA akkor szükség, jelentkezzen be más országban, ha nem kapcsolódnak VPN-en keresztül a vállalati hálózathoz, és néha még akkor is, ha a VPN-en keresztül kapcsolódik. Nem várt determinisztikus szabálykészletet, Azure Active Directory AI segítségével folyamatosan ismerje meg, ha 2FA megadása kötelező. Érdemes egy felhasználói kérés (interaktív hitelesítés vagy az eszköz kódfolyamat) a tartalék IWA meghibásodásakor.

- Az átadott a szolgáltatót a `PublicClientApplicationBuilder` kell lennie:
  - bérlő-ed (a képernyő `https://login.microsoftonline.com/{tenant}/` ahol `tenant` vagy a bérlői azonosító vagy egy tartományhoz a tenanthoz társított képviselő GUID.
  - a munkahelyi és iskolai fiókok (`https://login.microsoftonline.com/organizations/`)

  > Személyes Microsoft-fiókok nem támogatottak (/ Common vagy /consumers bérlők nem használható)

- Mivel az integrált Windows-hitelesítés beavatkozás nélküli folyamatok:
  - az alkalmazás a felhasználó korábban hozzájárult kell használni az alkalmazást
  - vagy a bérlői rendszergazda korábban hozzájárult kell használni az alkalmazást a bérlő minden felhasználója számára.
  - Más szóval:
    - vagy a fejlesztők megnyomta a **Grant** gombra a szolgáltatást, az Azure Portalon
    - vagy egy Bérlői rendszergazda rendelkezik a **engedélyezéshez/visszavonáshoz rendszergazdai jóváhagyás {bérlői tartományhoz}** gombra a **API-engedélyek** a az alkalmazás regisztrációs lapján (lásd: [engedélyek hozzáadása hozzáférés a webes API-k](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - a megadott felhasználók, hogy engedélyt adjanak az alkalmazásnak vagy (lásd: [egyedi felhasználói jóváhagyás kérése](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - vagy a bérlői rendszergazdai jóváhagyást az alkalmazáshoz tartozó úgy megadott (lásd: [rendszergazdai jóváhagyás](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Ez a folyamat engedélyezve van a .net asztali, a .net core és a Windows Universal (UWP-) alkalmazások. .NET core-on csak a felhasználónév véve túlterhelés érhető el, a .NET Core platform nem kérje meg a felhasználónevet az operációs rendszer szerint.
  
A jóváhagyás további információkért lásd: [v2.0 engedélyek és jóváhagyás](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Használat

Általában csak egyetlen paramétert kell (`scopes`). Azonban a Windows-rendszergazda rendelkezik a telepítő a házirendek módja attól függően, hogy is lehet, hogy az alkalmazások a Windows rendszerű gépén keresse ki a bejelentkezett felhasználó nem engedélyezett. Ebben az esetben használja a második módszer `.WithUsername()` és továbbíthatja azt a felhasználónevet, a bejelentkezett felhasználó egyszerű felhasználónév formátumban – `joe@contoso.com`.

Az alábbi mintában találja, hogy milyen típusú kivételeket kap, és azok be az a legfrissebb esetben

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

A AcquireTokenByIntegratedWindowsAuthentication lehetséges dostupnosti listáját lásd: [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Felhasználónév / jelszó

A felhasználónév és jelszó megadásával is lekérheti a jogkivonatot. Ez a folyamat korlátozott, ezért nem ajánlott, de nincsenek még mindig használja az esetekben, amikor szükséges.

### <a name="this-flow-isnt-recommended"></a>Ez a folyamat nem ajánlott.

Ez a folyamat **nem ajánlott** , mert az alkalmazás a felhasználó megkérése a jelszavát nem biztonságos. A problémáról további információk: [Ez a cikk](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Előnyben részesített tokenbeolvasás csendes módban a Windows-tartományhoz csatlakoztatott gépeket folyamatábrája [integrált Windows-hitelesítés](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Ellenkező esetben használhatja [eszköz kódfolyamat](https://aka.ms/msal-net-device-code-flow)

> Bár ez hasznos, ha bizonyos esetekben (DevOps-forgatókönyvekre), ha azt szeretné, felhasználónév/jelszó használata az interaktív forgatókönyvek, ahol a felhasználói felület onw meg kell, valóban gondolja dobozból áthelyezése. Felhasználónév/jelszó használatával, vannak megadva felfelé több minden:

> - Alapszintű bérlők modern identitás: jelszó fogott lekérdezi, játssza vissza. Mert a fogalom, amely megszerezhetik megosztás titkos kód.
> Ez az beállításának kompatibilis.
> - Ehhez az MFA kívánó felhasználók nem tudják jelentkezik be (mivel nem lett a beavatkozás nélküli)
> - Felhasználók nem tudják az egyszeri bejelentkezés

### <a name="constraints"></a>Korlátozások

A következő korlátozások is érvényesek:

- A felhasználónév/jelszó flow nem kompatibilis a feltételes hozzáférés és a multi-factor authentication szolgáltatás: Következtében ha az alkalmazás fut az Azure AD-bérlővel, ahol a bérlői rendszergazda multi-factor Authentication hitelesítést igényel, nem használhatja ezt a folyamatot. Számos szervezet megtenni.
- Működés csak a munkahelyi és iskolai fiókok (nem MSA)
- A folyamat érhető el a .net asztali és a .net core, de nem UWP

### <a name="b2c-specifics"></a>B2C tulajdonságairól

[További információ a B2C-vel való használatához készült ROPC](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Hogyan kell használni?

`IPublicClientApplication`a metódus tartalmaz `AcquireTokenByUsernamePassword`

A következő mintát egyszerűbb esetet mutat be

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Az alábbi mintában találja, hogy milyen típusú kivételeket kap, és azok be az a legfrissebb esetben

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Alkalmazható az összes dostupnosti részleteiért `AcquireTokenByUsernamePassword`, lásd: [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Parancssori eszköz (nélkül a webböngésző)

### <a name="device-code-flow-why-and-how"></a>Eszköz kód flow miért? és hogyan?

Egy parancssori eszköz (amely nem rendelkezik a webes vezérlők), ha nem szeretné használni a korábbi folyamatokat, és nem kell használni `AcquireTokenWithDeviceCode`.

Az Azure ad-vel az interaktív hitelesítéshez szükséges egy webes böngésző (További részletekért lásd: [böngészők használati](https://aka.ms/msal-net-uses-web-browser)). Azonban azokon az eszközökön vagy operációs rendszereket, amelyeket nem ad meg egy webböngészőt a felhasználók hitelesítésére, eszköz kódot a folyamat lehetővé teszi a felhasználó egy másik eszközt (például egy másik számítógépre vagy egy mobiltelefon) aláírására használja interaktív módon. Az alkalmazás az eszköz hitelesítésikód-folyamata segítségével szerzi be a tokenek kifejezetten arra tervezték, ezek az eszközök/os kétlépéses folyamat. Az ilyen alkalmazások olyan alkalmazások, iOT, vagy a parancssori eszközök (CLI). A cél, hogy:

1. Felhasználói hitelesítés szükség, amikor az alkalmazás egy kódot biztosít, és megkérdezi a felhasználót, nyissa meg egy URL-címet (például egy internethez csatlakozó okostelefon) egy másik eszköz használatával (például `https://microsoft.com/devicelogin`), ahol a felhasználó felszólítást kap a kódot. Hogy megtörtént, a weblap irányítja a felhasználót egy normál hitelesítési módszer, beleértve a beleegyezést kérő és a multi-factor Authentication hitelesítés, amennyiben szükséges keresztül.

2. Sikeres hitelesítést követően a parancssori alkalmazást fog kapni a szükséges jogkivonatok, biztonsági csatornán keresztül történik, és fogja használni, a szükséges API-hívások végrehajtásához.

### <a name="code"></a>Kód

`IPublicClientApplication`metódus neve `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Ez a módszer paraméterekként fogadja:

- A `scopes` hozzáférési jogkivonat kérése
- Egy visszahívást, amelyet fog kapni a `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Az alábbi mintakód a legfrissebb esetben be az, hogy milyen típusú kivételeket kap, és azok kockázatcsökkentési magyarázatot.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
  {
       // This will print the message on the console which tells the user where to go sign-in using
       // a separate browser and the code to enter once they sign in.
       // The AcquireTokenWithDeviceCode() method will poll the server after firing this
       // device code callback to look for the successful login of the user via that browser.
       // This background polling (whose interval and timeout data is also provided as fields in the
       // deviceCodeCallback class) will occur until:
       // * The user has successfully logged in via browser and entered the proper code
       // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
       // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
       //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
       Console.WriteLine(deviceCodeResult.Message);
       return Task.FromResult(0);
  }).ExecuteAsync();

  Console.WriteLine(result.Account.Username);
  return result;
 }
 catch (MsalServiceException ex)
 {
  // Kind of errors you could have (in ex.Message)

  // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
  // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
  // your public client application with the following authorities:
  // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

  // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
  // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

  // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
  // no active subscriptions for the tenant. Check with your subscription administrator.
  // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
  // tenantId (GUID) or tenant domain name.
 }
 catch (OperationCanceledException ex)
 {
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>A fájlalapú tokengyorsítótárral

Egy memórián belüli tokengyorsítótárral MSAL.NET, alapértelmezés szerint nyújtja.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Sorba rendezés akkor testre szabható a Windows asztali alkalmazások és a web apps/webes API-k

.NET-keretrendszer és a .NET core esetében nem tesz semmit sem, ha a jogkivonat memórián belüli gyorsítótár tart az alkalmazás időtartamára. Ismerje meg, miért szerializálási nem azonnal használatra kész, ne feledje MSAL .NET asztali/core-alkalmazások lehet konzol vagy a Windows-alkalmazások (amely lenne hozzáférésük a fájlrendszerben), **, hanem** webes alkalmazások vagy webes API-t. Ezeket a webalkalmazások és webes API-kat használhatnak bizonyos megadott gyorsítótárfiók mechanizmusok, adatbázisok, elosztott gyorsítótárakat, redis Cache-gyorsítótárak és így tovább. Ahhoz, hogy egy állandó token gyorsítótár-alkalmazás a .NET asztali vagy Core, szüksége lesz a szerializálási testreszabása.

Osztályok és a kapcsolódási tokengyorsítótárral szerializálási részt a következő típusok a következők:

- ``ITokenCache``, események tokengyorsítótárral szerializálási kérelmek, valamint szerializálni vagy deszerializálni a különféle formátumokat gyorsítótárát módszerek előfizetni határozza meg, amely (ADAL v3.0, MSAL 2.x és MSAL 3.x = ADAL 5.0-s verzió)
- ``TokenCacheCallback`` egy visszahívás átadott az eseményeket, hogy a szerializálási kezelheti. fogja hívható típusú argumentumok ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` csak a nyújt a ``ClientId`` az alkalmazás és a egy hivatkozást, amely a jogkivonat áll rendelkezésre a felhasználó számára

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET token gyorsítótárak hoz létre, és biztosítja a `IToken` gyorsítótárazza, ha egy alkalmazás hívása `GetUserTokenCache` és `GetAppTokenCache` módszereket. Nem lehet a saját maga a felület megvalósítása. A felelősség, egy egyéni tokengyorsítótárral szerializálási megvalósításának, hogy:
>
> - Reagálás `BeforeAccess` és `AfterAccess` "események". A`BeforeAccess` delegált feladata deszerializálni a gyorsítótárat, mivel a `AfterAccess` egyik feladata a gyorsítótár szerializálásához.
> - Ezek az események egy része tárolni vagy betölteni a blobok, amely bármilyen kívánt tárolóra az esemény argumentumot átadni.

A stratégiák eltérnek attól függően, ha szeretne írni egy tokengyorsítótárral szerializálási nyilvános ügyfélalkalmazás (asztalon), vagy egy bizalmas ügyfélalkalmazás (webalkalmazás vagy webes API-alkalmazás démon).

Az MSAL 2.x óta számos lehetősége van, attól függően, ha azt szeretné, szerializálni a gyorsítótár csak az MSAL.NET formátum (egyesített formátum gyorsítótár, amely gyakori az MSAL, hanem a platformok között), vagy ha szeretné is támogatja, a [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Az ADAL V3 szerializálási tokengyorsítótárral.

Token gyorsítótár szerializálási ADAL.NET között az egyszeri bejelentkezési állapot megosztása testreszabása 3.x, ADAL.NET 5.x és MSAL.NET kifejtett részben a következő minta: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű tokengyorsítótárral szerializálási (csak az MSAL)

Alább, amelyek az asztali alkalmazások jogkivonat a gyorsítótárban egyedi szerializálás naiv végrehajtásának. Itt a felhasználói jogkivonatok gyorsítótárát egy fájlban, az alkalmazás ugyanabban a mappában.

Után az alkalmazás létrehozásához, engedélyezi a szerializálási meghívásával ``TokenCacheHelper.EnableSerialization()`` átadja a alkalmazás `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A segítőosztálynak néz ki a következő kódrészletet:

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Előzetes verziója egy termék minőségének tokengyorsítótárral fájlalapú szerializáló nyilvános ügyfélalkalmazások (a Windows, Mac és Linux rendszereken futó asztali alkalmazások) esetében érhető el a [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú kódtár. Az alkalmazások a következő nuget-csomagot a tartalmazhatnak: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> Jogi nyilatkozat. A Microsoft.Identity.Client.Extensions.Msal erőforrástár egy bővítmény MSAL.NET felett. Ezek a kódtárak található osztályok előfordulhat, hogy eljutnak az MSAL.NET a jövőben van, vagy a használhatatlanná tévő változásai.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Kettős tokengyorsítótárral szerializálási (MSAL egyesített cache + ADAL V3)

Ha tokengyorsítótárral szerializálási megvalósítani kívánt mindkettőt, az egyesített gyorsítótárazni a formátumot (közös ADAL.NET 4.x és MSAL.NET 2.x, és az egyéb MSALs generációs azonos vagy régebbi, ugyanarra a platformra), akkor is ihletet meríthet a következő kód :

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Ezúttal a segítőosztály a következő kódhoz hasonlóan néz ki:

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A webes API meghívása az asztali alkalmazás](scenario-desktop-call-api.md)
