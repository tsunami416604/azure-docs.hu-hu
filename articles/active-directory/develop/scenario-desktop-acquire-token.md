---
title: Asztali alkalmazás, amely meghívja a webes API-kat (token beszerzése az alkalmazáshoz) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre olyan asztali alkalmazást, amely webes API-kat hív meg (token beszerzése az alkalmazáshoz |)
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
ms.date: 10/24/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0eaeaf915ad480306c114d7ab79e88e95c336eb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893909"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Webes API-kat meghívó asztali alkalmazás – jogkivonat beszerzése

Miután létrehozta `IPublicClientApplication`ét, felhasználhatja egy olyan token beszerzéséhez, amelyet később a webes API-k meghívásához fog használni.

## <a name="recommended-pattern"></a>Javasolt minta

A webes API-t a `scopes`határozza meg. Függetlenül attól, hogy milyen élményt nyújt az alkalmazásban, a használni kívánt minta a következő:

- A jogkivonat-gyorsítótárból a `AcquireTokenSilent` meghívásával szisztematikusan próbálkozik a jogkivonat lekérésével
- Ha a hívás sikertelen, használja a használni kívánt `AcquireToken` folyamatot (itt `AcquireTokenXX`által képviselt)

### <a name="in-msalnet"></a>A MSAL.NET

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
### <a name="in-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
    
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

Itt láthatja a jogkivonatok asztali alkalmazásokban való beszerzésének különböző módszereit.

## <a name="acquiring-a-token-interactively"></a>Token interaktív beszerzése

Az alábbi példa azt mutatja be, hogy a jogkivonat interaktív beolvasása a felhasználó profiljának Microsoft Graph használatával történő olvasásához szükséges minimális kód.

### <a name="in-msalnet"></a>A MSAL.NET

```CSharp
string[] scopes = new string[] {"user.read"};
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

### <a name="in-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

Swift

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

### <a name="mandatory-parameters"></a>Kötelező paraméterek

`AcquireTokenInteractive` csak egy kötelező paraméterrel rendelkezik ``scopes``, amely tartalmazza a karakterláncok enumerálását, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat a Microsoft Graph, a szükséges hatókörök az "engedélyek" szakaszban található minden Microsoft Graph API API-referenciájában találhatók. A [felhasználó névjegyeinek listázásához](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)például a "felhasználó. Read", a "Contacts. Read" hatókört kell használni. Lásd még [Microsoft Graph engedélyek referenciáját](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Androidon a fölérendelt tevékenységet is meg kell adnia (`.WithParentActivityOrWindow`használatával, lásd alább), hogy a token az interakció után visszakerüljön a fölérendelt tevékenységbe. Ha nem ad meg, a rendszer kivételt fog kiváltani `.ExecuteAsync()`meghívásakor.

### <a name="specific-optional-parameters-in-msalnet"></a>Speciális választható paraméterek a MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Az interaktív KEZELŐFELÜLET fontos. `AcquireTokenInteractive` egy olyan opcionális paramétert ad meg, amely lehetővé teszi, hogy az azt támogató platformok számára a szülő felhasználói felületet válassza. Asztali alkalmazásokban való használat esetén a `.WithParentActivityOrWindow` eltérő típusúnak kell lennie a platformtól függően:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Megjegyzéseket tartalmazó

- A .NET Standard verzióban a várt `Activity` `object` az Androidon, a `UIViewController` iOS-en, egy `NSWindow` MAC gépen, valamint egy `IWin32Window` vagy `IntPr` Windows rendszeren.
- Windows rendszeren a felhasználói felületi szálból kell meghívnia `AcquireTokenInteractive`t, hogy a beágyazott böngésző megkapja a megfelelő felhasználói felületi szinkronizálási környezetet.  Ha a felhasználói felületi szál nem hívja meg a hívást, az üzenetek nem tudnak megfelelően és/vagy holtpontra jutni a felhasználói felületen. A MSAL a felhasználói felületi szálból való meghívásának egyik módja, ha a felhasználói felületi szálon már nem a `Dispatcher` a WPF rendszeren.
- Ha WPF-t használ, a WPF-vezérlőkből származó ablak beszerzéséhez `WindowInteropHelper.Handle` osztályt használhat. A hívást ezután egy WPF-vezérlőből (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

a `WithPrompt()` a felhasználóval való interaktivitás vezérlésére szolgál egy kérdés megadásával

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő konstansokat határozza meg:

- ``SelectAccount``: kényszeríti az STS-t, hogy bemutassa a fiók kiválasztása párbeszédpanelt, amely olyan fiókokat tartalmaz, amelyekhez a felhasználó rendelkezik munkamenettel. Ez a beállítás akkor hasznos, ha az alkalmazások fejlesztői számára engedélyezni szeretné a felhasználók számára a különböző identitások közötti választást. Ez a beállítás a MSAL ``prompt=select_account`` küldését hajtja az identitás-szolgáltatónak. Ez az alapértelmezett beállítás, és jó munkát végez a lehető legjobb élmény biztosításához a rendelkezésre álló információk (fiók, a felhasználó munkamenetének jelenléte stb.) alapján. ...). Csak akkor módosítsa, ha nincs megfelelő oka.
- ``Consent``: lehetővé teszi, hogy az alkalmazás fejlesztője kényszerítse a felhasználót abban az esetben is, ha a beleegyező engedélyt megadták. Ebben az esetben a MSAL `prompt=consent` küld az identitás-szolgáltatónak. Ez a beállítás olyan biztonsági szempontokat használó alkalmazásokban használható, ahol a szervezet irányítása megköveteli, hogy a felhasználó az alkalmazás minden egyes használatakor megtekintse a beleegyezik párbeszédpanelt.
- ``ForceLogin``: lehetővé teszi, hogy az alkalmazás fejlesztője akkor is kérje a felhasználótól a hitelesítő adatok megadását, ha ez a felhasználó nem lenne szükséges. Ez a beállítás akkor lehet hasznos, ha a jogkivonat beszerzése meghiúsul, hogy a felhasználó újra bejelentkezzen. Ebben az esetben a MSAL `prompt=login` küld az identitás-szolgáltatónak. Azt is láttuk, hogy az egyes biztonsági irányultságú alkalmazásokban, ahol a szervezeti irányítás megköveteli, hogy a felhasználó újrajelentkezzen – minden alkalommal, amikor hozzáférnek az alkalmazás egyes részeihez.
- ``Never`` (csak a .NET 4,5-es és a WinRT esetén) nem kéri a felhasználót, hanem a rejtett beágyazott webes nézetben tárolt cookie-t próbálja használni (lásd alább: webes nézetek a MSAL.NET-ben). Ha ez a beállítás nem sikerül, és ebben az esetben `AcquireTokenInteractive` kivételt jelez, hogy a felhasználói felületi interakcióra van szükség, és egy másik `Prompt` paramétert kell használnia.
- ``NoPrompt``: a rendszer nem küld figyelmeztetést az identitás-szolgáltatónak. Ez a lehetőség csak Azure AD B2C profil-házirendek szerkesztéséhez használható (lásd a [B2C-specifikusokat](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ez a módosító olyan speciális forgatókönyvekben használatos, ahol a felhasználónak előre jóvá kell hagynia több erőforrást (és nem szeretné használni a növekményes beleegyezést, amelyet általában a MSAL.NET/a Microsoft Identity platformmal használ). A részletekért lásd [: a felhasználó beleegyezett több erőforrásra](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

A webes felhasználói felület egy böngésző meghívására szolgáló mechanizmus. Ez a mechanizmus lehet dedikált felhasználói felületi WebBrowser vezérlő vagy a böngésző megnyitásának delegálására szolgáló módszer.
A MSAL webes felhasználói felületi implementációkat biztosít a legtöbb platformhoz, de továbbra is előfordulhatnak olyan esetek, amikor a böngészőt saját maga szeretné üzemeltetni: 

- a MSAL által kifejezetten nem érintett platformok, például a Blazer, az Unity, a Mono on Desktop
- szeretné tesztelni az alkalmazást, és egy olyan automatizált böngészőt szeretne használni, amely a szelén segítségével használható 
- a böngésző és a MSAL-t futtató alkalmazás külön folyamatokban van

##### <a name="at-a-glance"></a>Áttekintés

Ennek eléréséhez meg kell adnia egy `start Url`MSAL, amelyet egy tetszőleges böngészőben kell megjeleníteni, hogy a felhasználó megadhatja a felhasználónevét stb. A hitelesítés befejeződése után az alkalmazásnak vissza kell térnie a `end Url`MSAL, amely tartalmazza az Azure AD által biztosított kódot.
A `end Url` gazdagépe mindig a `redirectUri`. A `end Url` elfogásához a következőket teheti: 

- a böngésző átirányításának figyelése, amíg a `redirect Url` el nem éri a találatot, vagy
- a böngésző átirányítja az URL-címre, amelyet figyel

##### <a name="withcustomwebui-is-an-extensibility-point"></a>A WithCustomWebUi egy bővíthetőségi pont

a `WithCustomWebUi` egy olyan bővíthetőségi pont, amely lehetővé teszi saját felhasználói felületének megadását a nyilvános ügyfélalkalmazások számára, és lehetővé teszi, hogy a felhasználó áthaladjon az/Authorize végpontján, és lehetővé tegye a bejelentkezést és a hozzájuk való hozzájárulásukat. A MSAL.NET megadhatja a hitelesítési kódot, és lekérheti a tokent. A Visual Studióban használt példányoknak, hogy az elektron-alkalmazások (például a VS visszajelzések) biztosítják a webes interakciót, azonban ne MSAL.NET a munka nagy részét. Akkor is használhatja, ha a felhasználói felület automatizálását szeretné megadni. A nyilvános ügyfélalkalmazások esetében a MSAL.NET a PKCE standard ([RFC 7636-Proof kulcs a Code Exchange számára a OAuth nyilvános ügyfelek által](https://tools.ietf.org/html/rfc7636)) használatával biztosítja a biztonság tiszteletben tartását: csak MSAL.net válthat be a kódban.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>A WithCustomWebUi használata

A `.WithCustomWebUI`használatához a következőket kell tennie:
  
  1. A `ICustomWebUi` felület implementálása (lásd [itt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Alapvetően egy metódust kell megvalósítani `AcquireAuthorizationCodeAsync` az engedélyezési kód URL-címének (MSAL.NET alapján számított) elfogadását, hogy a felhasználó átugorjon az identitás-szolgáltatóval való interakcióra, majd visszaadja az URL-címet, amellyel az identitás szolgáltatója lenne a megvalósítást vissza kell hívni (beleértve az engedélyezési kódot is). Ha problémák merülnek fel, a megvalósításnak `MsalExtensionException` kivételt kell eldobnia, hogy szépen működjenek együtt a MSAL-mel.
  2. A `AcquireTokenInteractive`-hívásban használhatja `.WithCustomUI()` módosítót az egyéni webes felhasználói felület példányának átadásával

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Példák a ICustomWebUi bevezetésére a test automationben – SeleniumWebUI

A MSAL.NET csapata átírta a felhasználói felületi teszteket a bővíthető mechanizmus kihasználása érdekében. Ha érdekli, tekintse meg a [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) osztályt a MSAL.net forráskódjában

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>A SystemWebViewOptions nagyszerű élményt nyújt

A MSAL.NET 4,1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) lehetővé teszi az alábbiak megadását:

- a (`BrowserRedirectError`) vagy a megjelenítendő HTML-kódrészlet (`HtmlMessageError`) a rendszerböngészőben való bejelentkezési/beleegyező hibák esetén
- az a (`BrowserRedirectSuccess`) vagy a megjelenítendő HTML-kódrészlet (`HtmlMessageSuccess`) a sikeres bejelentkezés/beleegyezikés esetén.
- a rendszerböngésző indításához futtatandó művelet. Ehhez megadhatja saját megvalósítását a `OpenBrowserAsync` delegált beállításával. Az osztály két böngésző alapértelmezett implementációját is biztosítja: `OpenWithEdgeBrowserAsync` és `OpenWithChromeEdgeBrowserAsync`, a Microsoft Edge és [a Microsoft Edge esetében pedig a Chromium-on](https://www.windowscentral.com/faq-edge-chromium).

A struktúra használatához a következőhöz hasonló módon írhat:

```CSharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Egyéb választható paraméterek

További információ a [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) -hez készült dokumentációban `AcquireTokenInteractive` további opcionális paraméterekről

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés

Ha tartományi felhasználót szeretne bejelentkezni egy tartományba vagy egy Azure AD-hez csatlakoztatott gépre, a következőt kell használnia:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Korlátozások

- A AcquireTokenByIntegratedWindowsAuth (IWA) csak **összevont** felhasználók esetében használható, azaz a felhasználók egy Active Directoryban létrehozott és Azure Active Directory által támogatottak. A közvetlenül a HRE-ben létrehozott felhasználók AD-alapú, **felügyelt** felhasználók nélkül nem használhatják ezt a hitelesítési folyamatot. Ez a korlátozás nem befolyásolja a Felhasználónév/jelszó folyamatát.
- A IWA a .NET-keretrendszer, a .NET Core és a UWP platformokhoz írt alkalmazások esetében használható.
- A IWA nem kerüli el az MFA-t (többtényezős hitelesítés). Ha az MFA konfigurálva van, a IWA sikertelen lehet, ha MFA-kihívásra van szükség, mert az MFA felhasználói beavatkozást igényel.
  > [!NOTE]
  > Ez egy trükkös megoldás. A IWA nem interaktív, az MFA azonban felhasználói interaktivitást igényel. Nem szabályozhatja, hogy mikor történjen meg az identitás-szolgáltató az MFA elvégzését, a bérlői rendszergazda. A megfigyelések során MFA szükséges, ha egy másik országból jelentkezik be, ha nem VPN-kapcsolaton keresztül csatlakozik a vállalati hálózathoz, és néha még akkor is, ha VPN-en keresztül csatlakozik. Ne várja meg a determinisztikus, Azure Active Directory az AI-t használja a folyamatos tanuláshoz, ha MFA szükséges. Ha a IWA meghibásodik, a felhasználónak meg kell egyeznie a felhasználói üzenettel (interaktív hitelesítéssel vagy az eszköz kódjának folyamatával).

- A `PublicClientApplicationBuilder` átadott hatóságnak a következőnek kell lennie:
  - Bérlő-ED (az űrlap `https://login.microsoftonline.com/{tenant}/`, ahol a `tenant` vagy a bérlő AZONOSÍTÓját vagy a bérlőhöz társított tartományt jelképező GUID.
  - bármilyen munkahelyi és iskolai fiókhoz (`https://login.microsoftonline.com/organizations/`)
  - A Microsoft személyes fiókjai nem támogatottak (/gyakori hibák-vagy/consumers-bérlők nem használhatók)

- Mivel az integrált Windows-hitelesítés csendes folyamat:
  - az alkalmazás felhasználójának előzőleg el kell juttatnia az alkalmazás használatát
  - vagy a bérlői rendszergazdának előzőleg el kell juttatnia a bérlő összes felhasználója számára az alkalmazás használatát.
  - Más szóval:
    - Ön fejlesztőként megnyomta a Azure Portal a **Grant (Engedélyezés** ) gombot saját magára,
    - vagy a bérlői rendszergazda megnyomta az alkalmazás regisztrációjának **API-engedélyek** lapján lévő **{bérlői tartomány} gombra vonatkozó engedélyezési/visszavonási rendszergazdai jóváhagyást** (lásd: a [webes API-k eléréséhez szükséges engedélyek hozzáadása](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)).
    - vagy lehetősége van arra, hogy a felhasználók beleegyeznek az alkalmazásba (lásd: [egyéni felhasználói engedély kérése](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - vagy megadta a lehetőséget, hogy a bérlői rendszergazda beleegyezett az alkalmazásba (lásd: [rendszergazdai engedély](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Ez a folyamat engedélyezve van a .net Desktop, a .net Core és a Windows Universal (UWP) alkalmazásokhoz. A .NET Core-on csak a Felhasználónév elérhető túlterhelése érhető el, mivel a .NET Core platform nem tudja megkérni a felhasználónevet az operációs rendszernek.
  
További információ a beleegyező adatokról: a [Microsoft Identity platform engedélyei és beleegyezik](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Használat

Általában csak egy paraméterre van szükség (`scopes`). Azonban attól függően, hogy a Windows-rendszergazda milyen módon beállította a házirendeket, lehetséges, hogy a Windows-gépen lévő alkalmazások nem tudják megkeresni a bejelentkezett felhasználót. Ebben az esetben használjon egy második módszert `.WithUsername()` és adja át a bejelentkezett felhasználó felhasználónevét UPN formátumként – `joe@contoso.com`.

A következő minta a legfrissebb eseteket mutatja be, és magyarázatokkal szolgál a lekérhető kivételek típusára és azok enyhítésére

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

A AcquireTokenByIntegratedWindowsAuthentication lehetséges módosítók listájáért lásd: [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Felhasználónév/jelszó

A tokent a Felhasználónév és a jelszó megadásával is megvásárolhatja. Ez a folyamat korlátozott, és nem ajánlott, de továbbra is használhatók a szükséges esetek.

### <a name="this-flow-isnt-recommended"></a>Ez a folyamat nem ajánlott

Ez a folyamat **nem ajánlott** , mert az alkalmazás arra kéri a felhasználót, hogy a jelszava ne legyen biztonságos. A problémával kapcsolatos további információkért tekintse meg [ezt a cikket](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). A tokenek Windows-tartományhoz csatlakoztatott gépeken való csendes beszerzésének előnyben részesített folyamata [integrált Windows-hitelesítés](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Ellenkező esetben használhatja az [eszköz kódját](https://aka.ms/msal-net-device-code-flow) is

> [!NOTE] 
> Bár ez bizonyos esetekben hasznos lehet (DevOps forgatókönyvek), ha olyan interaktív helyzetekben szeretné használni a felhasználónevet és a jelszót, ahol a saját felhasználói felületét adja meg, érdemes meggondolni, hogyan helyezheti el onnan. A Felhasználónév/jelszó használatával számos dolgot ad meg:
>
> - a modern identitás alapbérlői: a jelszó bekerül, majd újra lejátszhatók. Mivel egy titkos megosztási titok ezt a fogalmát felhasználhatja.
> Ez nem kompatibilis a jelszóval.
> - az MFA-t igénylő felhasználóknak nem lehet bejelentkezniük (mivel nincs interakció)
> - A felhasználók nem tudják elvégezni az egyszeri bejelentkezést

### <a name="constraints"></a>Korlátozások

A következő korlátozások is érvényesek:

- A Felhasználónév/jelszó folyamat nem kompatibilis a feltételes hozzáféréssel és a többtényezős hitelesítéssel: Ha az alkalmazás egy Azure AD-bérlőben fut, ahol a bérlői rendszergazda a többtényezős hitelesítést igényli, nem használhatja ezt a folyamatot. Számos szervezet ezt teszi.
- Csak munkahelyi és iskolai fiókokhoz használható (nem MSA)
- A folyamat elérhető a .net Desktopban és a .net Core-on, de nem a UWP-on

### <a name="b2c-specifics"></a>B2C-sajátosságok

[További információ a ROPC a B2C-vel való használatáról](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Hogyan kell használni?

a `IPublicClientApplication`a metódust tartalmazza `AcquireTokenByUsernamePassword`

Az alábbi minta egy egyszerűsített esetet mutat be

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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

A következő minta a legfrissebb eseteket mutatja be, és magyarázatokkal szolgál a lekérhető kivételek típusára és azok enyhítésére

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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

A `AcquireTokenByUsernamePassword`alkalmazható módosítókkal kapcsolatos részletekért lásd: [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Parancssori eszköz (webböngésző nélkül)

### <a name="device-code-flow-why-and-how"></a>Miért van az eszköz kódjának folyamata? és hogyan?

Ha olyan parancssori eszközt írunk (amely nem rendelkezik webvezérlőkkel), és nem szeretné használni az előző folyamatokat, akkor a `AcquireTokenWithDeviceCode`t kell használnia.

Az Azure AD-vel való interaktív hitelesítéshez webböngészőre van szükség (részletekért lásd: [böngészők használata](https://aka.ms/msal-net-uses-web-browser)). Ha azonban a felhasználókat olyan eszközökön vagy operációs rendszereken szeretné hitelesíteni, amelyek nem biztosítanak webböngészőt, az eszköz kódjának folyamata lehetővé teszi, hogy a felhasználó egy másik eszközt (például egy másik számítógépet vagy egy mobiltelefont) használjon az interaktív bejelentkezéshez. Az eszköz kódjának használatával az alkalmazás egy kétlépéses folyamaton keresztül szerzi be a jogkivonatokat, kifejezetten ezekhez az eszközökhöz/operációs rendszerekhez. Ilyen alkalmazások például a iOT-on vagy parancssori eszközökön (CLI) futó alkalmazások. Az a gondolat, hogy:

1. Ha felhasználói hitelesítésre van szükség, az alkalmazás egy kódot biztosít, és arra kéri a felhasználót, hogy használjon egy másik eszközt (például egy internetkapcsolattal rendelkező okostelefont), hogy navigáljon egy URL-címre (például `https://microsoft.com/devicelogin`), ahol a rendszer kéri a felhasználótól a kód megadását. Ezzel a weblap a felhasználót a normál hitelesítési felülettel irányítja át, beleértve a hozzájárulási kéréseket és a többtényezős hitelesítést, ha szükséges.

2. A sikeres hitelesítés után a parancssori alkalmazás egy visszaadott csatornán keresztül kapja meg a szükséges jogkivonatokat, és ezt fogja használni a webes API-hívások igény szerinti elvégzéséhez.

### <a name="code"></a>Kód

a `IPublicClientApplication`egy `AcquireTokenWithDeviceCode` nevű metódust tartalmaz.

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

A metódus a következő paramétereket veszi figyelembe:

- A `scopes` hozzáférési token igényléséhez
- Visszahívás, amely a `DeviceCodeResult` fogja fogadni

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

A következő mintakód a legfrissebb eseteket mutatja be, és magyarázatokkal szolgál a beolvasott kivételek típusáról és azok enyhítéséről.

```CSharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();
           
    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification 

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
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
    // TODO: handle or throw all these exceptions depending on your app
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
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled. 
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads 
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

## <a name="file-based-token-cache"></a>Fájl alapú jogkivonat-gyorsítótár

A MSAL.NET-ben alapértelmezés szerint a memóriában tárolt jogkivonat-gyorsítótár van megadva.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>A szerializálás testreszabható a Windows asztali alkalmazásokban és webalkalmazásokban/webes API-kon

Ha a .NET-keretrendszer és a .NET Core esetében nem csinál semmit, a memóriában tárolt jogkivonat-gyorsítótár az alkalmazás időtartamára tart. Annak megismeréséhez, hogy a szerializálás miért nincs megadva a dobozban, ne feledje, hogy a MSAL .NET Desktop/Core alkalmazások konzolos vagy Windows-alkalmazások lehetnek (amelyek hozzáférhetnek a fájlrendszerhez), de a webalkalmazásokhoz vagy webes API-hoz **is** . Ezek a webalkalmazások és webes API-k bizonyos gyorsítótárazási mechanizmusokat használhatnak, például adatbázisokat, elosztott gyorsítótárat, Redis gyorsítótárat és így tovább. Ha állandó jogkivonat-gyorsítótárazási alkalmazást szeretne létrehozni a .NET Desktopban vagy a Core-ban, testre kell szabnia a szerializálást.

A jogkivonat-gyorsítótár szerializálásakor részt vevő osztályok és felületek a következők:

- ``ITokenCache``, amely a jogkivonat-gyorsítótár szerializálási kéréseire való előfizetéshez, valamint a gyorsítótár szerializálására vagy deszerializálására szolgáló metódusokat határozza meg különböző formátumokban (ADAL v 3.0, MSAL 2. x és MSAL 3. x = ADAL v 5.0)
- ``TokenCacheCallback`` visszahívást kapott az eseményekre, így kezelheti a szerializálást. a rendszer a ``TokenCacheNotificationArgs``típusú argumentumokkal hívja meg őket.
- ``TokenCacheNotificationArgs`` csak az alkalmazás ``ClientId``ét és annak a felhasználónak a hivatkozását adja meg, amelyhez a jogkivonat elérhető

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> A MSAL.NET jogkivonat-gyorsítótárat hoz létre az Ön számára, és megadja a `IToken` gyorsítótárat az alkalmazás `UserTokenCache` és `AppTokenCache` tulajdonságainak meghívásakor. Nem kell önállóan megvalósítani a felületet. Az egyéni jogkivonat-gyorsítótár szerializálásának megvalósításakor a következőt kell tennie:
>
> - Reagáljon `BeforeAccess`re, és `AfterAccess` "Events" (vagy *aszinkron* ). A`BeforeAccess` delegált feladata a gyorsítótár deszerializálása, míg a `AfterAccess` az egyik felelős a gyorsítótár szerializálásához.
> - Az események egy része tárolja vagy betölti a blobokat, amelyeket az Event argumentumon át kell adni a kívánt tárterülethez.

A stratégiák eltérőek attól függően, hogy egy nyilvános ügyfélalkalmazás (asztali) vagy egy bizalmas ügyfélalkalmazás (webalkalmazás/webes API, Daemon-alkalmazás) esetében a jogkivonat-gyorsítótár szerializálását írja-e a rendszer.

A MSAL v2. x óta több lehetőség is rendelkezésre áll, attól függően, hogy a gyorsítótárat csak a MSAL.NET formátumra kívánja-e szerializálni (az egységes formátumú gyorsítótár, amely közös a MSAL, hanem a platformok között is), vagy ha támogatni kívánja az [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) jogkivonat-gyorsítótárat is. a ADAL v3 szerializálása.

A jogkivonat-gyorsítótár szerializálásának testreszabásával megoszthatja a 3. x, a ADAL.NET 5. x és a MSAL.NET közötti SSO-állapotot a következő minta ADAL.NET: [Active-Directory-DotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű jogkivonat-gyorsítótár szerializálása (csak MSAL)

Az alábbi példa egy jogkivonat-gyorsítótár egyéni szerializálásának naiv implementációját mutatja be asztali alkalmazásokhoz. Itt a felhasználói jogkivonat gyorsítótára ugyanabban a mappában található fájlban van, mint az alkalmazás.

Az alkalmazás létrehozása után engedélyezheti a szerializálást az alkalmazás átadásával ``TokenCacheHelper.EnableSerialization()`` meghívásával `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Ez a segítő osztály a következő kódrészlethez hasonlít:

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

A Windows, Mac és Linux rendszerű asztali alkalmazásokhoz készült, a termék minőségi jogkivonat-gyorsítótárának fájl alapú szerializáló előzetes verziója a [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú könyvtárában érhető el. Az alkalmazásokban a következő nuget-csomaggal veheti fel: [Microsoft. Identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Nyilatkozat. A Microsoft. Identity. Client. Extensions. Msal könyvtár a MSAL.NET-en keresztüli kiterjesztés. Az ezekben a tárakban lévő osztályok a jövőben MSAL.NET a későbbiekben, ahogy az vagy a változtatások miatt.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Kettős jogkivonat gyorsítótár-szerializálása (MSAL Unified cache + ADAL v3)

Ha azt szeretné, hogy a jogkivonat-gyorsítótár szerializálását az egyesített gyorsítótár formátumával (Common ADAL.NET 4. x és MSAL.NET 2. x, valamint ugyanazon generáció vagy régebbi, ugyanazon a platformon lévő más MSALs) alkalmazza, akkor a következő kód ihlette. :

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

Ezúttal a segítő osztály a következő kódhoz hasonlít:

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
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API meghívása az asztali alkalmazásból](scenario-desktop-call-api.md)
