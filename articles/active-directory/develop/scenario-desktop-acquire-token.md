---
title: Beszerezni egy token hívni egy webes API (asztali alkalmazás) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre olyan asztali alkalmazást, amely webes API-kat hív meg az alkalmazás jogkivonatának megszerzéséhez
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 636c7c654b98ced5f93c3ace0e4a99bfc9bf7def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262606"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Webes API-kat meghívjaó asztali alkalmazás: Jogkivonat beszerzése

Miután elkészítette a nyilvános ügyfélalkalmazás egy példányát, egy jogkivonat beszerzésére fogja használni, amelyet majd egy webes API-hívásához fog használni.

## <a name="recommended-pattern"></a>Ajánlott minta

A webes API-t `scopes`a . Bármi legyen is a tapasztalat, amit az alkalmazásban biztosít, a minta használata a következő:

- Rendszeresen megpróbál tokeneket beszerezni a `AcquireTokenSilent`tokengyorsítótárból a hívásával.
- Ha ez a hívás `AcquireToken` sikertelen, használja a használni kívánt `AcquireTokenXX`folyamatot, amelyet itt a .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>A MSAL.NET

```csharp
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

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Az MSAL-ban iOS és macOS rendszeren

C célkitűzés:

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
Swift:

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
---

Az alábbiakban a tokenek beszerzésének különböző módjait olvashatja be egy asztali alkalmazásban.

## <a name="acquire-a-token-interactively"></a>Token beszerzése interaktív módon

A következő példa minimális kódot mutat be, hogy interaktívan beolvassa a felhasználói profil olvasásához a Microsoft Graph segítségével.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>A MSAL.NET

```csharp
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

### <a name="mandatory-parameters"></a>Kötelező paraméterek

`AcquireTokenInteractive`csak egy kötelező ``scopes``paraméterrel rendelkezik, amely olyan karakterláncok felsorolását tartalmazza, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat a Microsoft Graph,a szükséges hatókörök találhatók az API-referencia minden Microsoft Graph API-t a szakasz neve "Engedélyek" című szakaszban. A felhasználó [névjegyének listázásához](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)például a "User.Read", "Contacts.Read" tartományt kell használni. További információt a [Microsoft Graph engedélyek hivatkozása című témakörben talál.](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)

Androidon is meg kell adnia a `.WithParentActivityOrWindow`szülő tevékenység használatával, ahogy az látható, hogy a jogkivonat visszatér az adott szülőtevékenységhez az interakció után. Ha nem adja meg, a hívás `.ExecuteAsync()`során kivétel történik.

### <a name="specific-optional-parameters-in-msalnet"></a>Speciális választható paraméterek a MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow ablak

A felhasználói felület azért fontos, mert interaktív. `AcquireTokenInteractive`egy adott választható paraméterrel rendelkezik, amely az azt támogató platformok esetében megadhatja a szülő felhasználói felületet. Ha egy asztali alkalmazásban `.WithParentActivityOrWindow` használják, más típusú, ami a platformtól függ.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Megjegyzések:

- A .NET Standard `object` rendszerben `Activity` a `UIViewController` várt rendszer `NSWindow` Android, iOS, MAC és `IWin32Window` Windows `IntPr` rendszeren történik.
- Windows rendszerben a `AcquireTokenInteractive` felhasználói felület száláról kell hívnia, hogy a beágyazott böngésző megkapja a megfelelő felhasználói felületi szinkronizálási környezetet. Ha nem a felhasználói felület szálából hív, előfordulhat, hogy az üzenetek nem szivattyúznak megfelelően, és holtpontra jutnak a felhasználói felülettel. A Microsoft authentication libraries (MSALs) hívásának egyik módja a felhasználói felület i száláról, `Dispatcher` ha még nem szerepel a felhasználói felület szálán, az a WPF használata.
- Ha WPF-et használ, hogy ablakot szerezzen be egy WPF vezérlőből, használhatja az `WindowInteropHelper.Handle` osztályt. Ezután a hívás egy WPF vezérlő (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>Prompt (Gyors)

`WithPrompt()`segítségével szabályozhatja az interaktivitást a felhasználóval egy kérdés megadásával.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő állandókat határozza meg:

- ``SelectAccount``kényszeríti az STS-t, hogy mutassa be a fiókkijelölési párbeszédpanelt, amely azokat a fiókokat tartalmazza, amelyekhez a felhasználó munkamenetet tart. Ez a beállítás akkor hasznos, ha az alkalmazásfejlesztők azt szeretnék, hogy a felhasználók válasszanak a különböző identitások közül. Ez a beállítás az ``prompt=select_account`` MSAL-t az identitásszolgáltatónak való küldésre ösztönzi. Ez az alapértelmezett beállítás. Ez csinál egy jó munka-ból, hogy a lehető legjobb élményt a rendelkezésre álló információk, mint például a fiók és a munkamenet jelenléte a felhasználó számára. Ne változtasd meg, hacsak nincs rá jó okod.
- ``Consent``lehetővé teszi az alkalmazás fejlesztője számára, hogy kényszerítse a felhasználó beleegyezését, még akkor is, ha korábban megadták a hozzájárulást. Ebben az esetben az `prompt=consent` MSAL elküldi az identitásszolgáltatónak. Ez a beállítás néhány biztonságközpontú alkalmazásban használható, ahol a szervezet cégirányítási megköveteli, hogy a felhasználó minden alkalommal megjelenik a hozzájárulási párbeszédpanel, amikor az alkalmazást használja.
- ``ForceLogin``lehetővé teszi, hogy az alkalmazás fejlesztője a szolgáltatás hitelesítő adatokat kér a felhasználótól, még akkor is, ha erre a felhasználói kérdésre nincs szükség. Ez a beállítás hasznos lehet, hogy a felhasználó jelentkezzen be újra, ha egy jogkivonat beszerzése sikertelen. Ebben az esetben az `prompt=login` MSAL elküldi az identitásszolgáltatónak. Néha a biztonságra összpontosító alkalmazásokban használják, ahol a szervezet cégirányítási megköveteli, hogy a felhasználó újra aláírja minden alkalommal, amikor egy alkalmazás meghatározott részeihez férnek hozzá.
- ``Never``(csak a .NET 4.5 és a WinRT esetén) nem kéri a felhasználót, hanem a rejtett beágyazott webes nézetben tárolt cookie-t próbálja használni. További információt a MSAL.NET webes nézetei című témakörben talál. Ezzel a beállítással sikertelen lehet. Ebben az `AcquireTokenInteractive` esetben kivételt küld, hogy értesítse a rról, hogy felhasználói felületi interakcióra van szükség. Másik `Prompt` paramétert kell használnia.
- ``NoPrompt``nem küld semmilyen kérdést az identitásszolgáltatónak. Ez a beállítás csak az Azure Active Directory (Azure AD) B2C szerkesztési profilszabályzatok. További információ: [Azure AD B2C specifics.](https://aka.ms/msal-net-b2c-specificities)

#### <a name="withextrascopetoconsent"></a>Extrascopetohozzájárulással

Ez a módosító egy speciális forgatókönyv, ahol azt szeretné, hogy a felhasználó előzetesen több erőforrás előzetes beleegyezését, és nem szeretné használni a növekményes hozzájárulás, amely általában MSAL.NET/the Microsoft identitás platform. További információ: [A felhasználó hozzájárulásának előzetes biztosítása több erőforráshoz.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>ACustomWebUi segítségével

A webes felhasználói felület egy böngésző meghívására alkalmas mechanizmus. Ez a mechanizmus lehet egy dedikált UI WebBrowser vezérlő, vagy a böngésző megnyitásának delegálása.
Az MSAL a legtöbb platformon webes felhasználói felületimplementációkat biztosít, de vannak olyan esetek, amikor a böngészőt saját kezűleg szeretné üzemeltetni:

- Az MSAL által kifejezetten nem lefedett platformok, például a Blazor, a Unity és a Monó asztali gépeken.
- Azt szeretné, hogy a felhasználói felület tesztelje az alkalmazást, és egy automatikus böngésző, amely használható szelén.
- A böngésző és az MSAL-t futtató alkalmazás külön folyamatokban van.

##### <a name="at-a-glance"></a>Első pillantásra

Ennek elérése érdekében adja `start Url`meg az MSAL-nak, amelyet egy választott böngészőben kell megjeleníteni, hogy a végfelhasználó olyan elemeket adjon meg, mint például a felhasználóneve.
A hitelesítés befejezése után az alkalmazásnak vissza `end Url`kell adnia az MSAL-nak, amely az Azure AD által biztosított kódot tartalmaz.
A fogadó `end Url` mindig `redirectUri`. Az `end Url`elfogáshoz tegye az alábbi teendők egyikét:

- A böngésző átirányításának figyelése a lenemütésig. `redirect Url`
- A böngésző átirányítsa egy URL-re, amelyet figyelni fog.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi egy bővíthetőségi pont

`WithCustomWebUi`egy bővíthetőségi pont, amelysegítségével saját felhasználói felületet biztosíthat a nyilvános ügyfélalkalmazásokban. Azt is engedélyezheti, hogy a felhasználó menjen át az identitásszolgáltató /Authorize végpontján, és hagyja, hogy jelentkezzen be és járul hozzá. MSAL.NET ezután beválthatják a hitelesítési kódot, és egy jogkivonatot kaphatnak. Például a Visual Studio-ban használják elektronok alkalmazások (például visual studio visszajelzés) biztosítja a webes interakció, de hagyja, hogy MSAL.NET a munka nagy részét. Akkor is használhatja, ha felhasználói felület automatizálását szeretné biztosítani. A nyilvános ügyfélalkalmazásokban MSAL.NET a Kódcsere próbakulcsa (PKCE) szabványt használja a biztonság tiszteletben tartásának biztosítására. Csak MSAL.NET válthatja be a kódot. További információ: [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Használat withCustomWebUi

A `.WithCustomWebUI`használatához kövesse az alábbi lépéseket.

  1. Implementálja az `ICustomWebUi` felületet. További információ: [ezen a webhelyen.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) Valósítson meg egy `AcquireAuthorizationCodeAsync`módszert, és fogadja el a MSAL.NET által kiszámított engedélyezési kód URL-címét. Ezután hagyja, hogy a felhasználó menjen át az identitásszolgáltatóval való interakción, és adja vissza azt az URL-címet, amellyel az identitásszolgáltató visszahívta volna a megvalósítást az engedélyezési kóddal együtt. Ha problémája van, a végrehajtásnak kivételt `MsalExtensionException` kell tennie, hogy szépen együttműködjön az MSAL-lal.
  2. A `AcquireTokenInteractive` hívás, használja `.WithCustomUI()` a módosító halad a példány az egyéni webes felhasználói felület.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Példák az ICustomWebUi tesztelésének megvalósítására: SeleniumWebUI

A MSAL.NET csapat átírta a felhasználói felület tesztjeit, hogy ezt a bővíthetőségi mechanizmust használja. Ha érdekel, nézd meg a [SzelénWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) osztály a MSAL.NET forráskód.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Nagyszerű élményt nyújt a SystemWebViewOptions segítségével

A 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)MSAL.NET a következőket adhatja meg:

- A rendszer`BrowserRedirectError`webböngészőjében bejelentkezési vagy`HtmlMessageError`hozzájárulási hibák esetén megjelenítendő ( ) URI-kód vagy HTML-töredék.
- Sikeres bejelentkezés vagy`BrowserRedirectSuccess`hozzájárulás esetén a (`HtmlMessageSuccess`) vagy a HTML-töredék megjelenítéséhez szükséges URI.
- A rendszerböngésző indításához futtatandó művelet. A `OpenBrowserAsync` meghatalmazott beállításával saját megvalósítást biztosíthat. Az osztály két böngésző alapértelmezett implementációját is biztosítja: `OpenWithEdgeBrowserAsync` a Microsoft Edge és `OpenWithChromeEdgeBrowserAsync` a Microsoft Edge a [Chromiumon.](https://www.windowscentral.com/faq-edge-chromium)

A struktúra használatához írja be a következő példához hasonló példát:

```csharp
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

Ha többet szeretne megtudni a többi `AcquireTokenInteractive`választható paraméterről, olvassa el a [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)című témakört.

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Az MSAL Python nem biztosít közvetlenül egy interaktív beszerzési token metódust. Ehelyett megköveteli, hogy az alkalmazás küldjön egy engedélyezési kérelmet a felhasználói beavatkozási folyamat végrehajtása során egy engedélyezési kód beszerzéséhez. Ez a kód ezután `acquire_token_by_authorization_code` átadható a módszernek a jogkivonat lekéréséhez.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Az MSAL-ban iOS és macOS rendszeren

C célkitűzés:

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

Swift:

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
---

## <a name="integrated-windows-authentication"></a>Beépített Windows-hitelesítés

Ha egy tartományfelhasználót szeretne bejelentkezni egy tartományba vagy az Azure AD-hez csatlakozó gépre, használja az integrált Windows-hitelesítést (IWA).

### <a name="constraints"></a>Korlátozások

- Az integrált Windows-hitelesítés csak *összevont+* felhasználók számára használható, azaz az Active Directoryban létrehozott és az Azure AD által támogatott felhasználók számára. Közvetlenül az Azure AD-ben az Active Directory- háttér, más néven *felügyelt* felhasználók nélkül létrehozott felhasználók nem használhatják ezt a hitelesítési folyamatot. Ez a korlátozás nincs hatással a felhasználónév és a jelszó folyamatára.
- Az IWA a .
- Az IWA nem kerüli meg a többtényezős hitelesítést (MFA). Ha az MFA konfigurálva van, az IWA sikertelen lehet, ha többes fa-kihívásra van szükség, mert az MFA felhasználói beavatkozást igényel.
  > [!NOTE]
  > Ez trükkös. Az IWA nem interaktív, de az MFA felhasználói interaktivitást igényel. Nem szabályozhatja, hogy az identitásszolgáltató mikor kéri az MFA-t, a bérlői rendszergazda. Megfigyeléseink szerint az MFA akkor szükséges, ha egy másik országból jelentkezik be, ha nem VPN-en keresztül csatlakozik egy vállalati hálózathoz, és néha akkor is, ha VPN-en keresztül csatlakozik. Ne várjon determinisztikus szabályrendszerre. Az Azure AD ai segítségével folyamatosan megtudhatja, ha többéves szükséges. Ha az IWA meghibásodik, visszaáll a felhasználói kérdésre, például az interaktív hitelesítésre vagy az eszközkód-áramlásra.

- Az átadott `PublicClientApplicationBuilder` hatóságnak a következőknek kell lennie:
  - Bérlője az `https://login.microsoftonline.com/{tenant}/`űrlap `tenant` , ahol vagy a GUID, amely a bérlőazonosítót, vagy a bérlőhöz társított tartomány.
  - Minden munkahelyi és iskolai `https://login.microsoftonline.com/organizations/`számlák: .
  - A Microsoft személyes fiókjai nem támogatottak. A /common vagy /consumers bérlők nem használhatók.

- Mivel az integrált Windows-hitelesítés csendes folyamat:
  - Az alkalmazás felhasználójának korábban hozzá kell járulnia az alkalmazás használatához.
  - Vagy a bérlői rendszergazdának korábban beleegyezéssel kell rendelkeznie a bérlő összes felhasználójához az alkalmazás használatához.
  - Más szóval:
    - Vagy ön, mint fejlesztő választotta ki a **Grant** gombot az Azure Portalon saját maga.
    - Vagy egy bérlői rendszergazda kiválasztotta a **Grant/Revoke rendszergazdai hozzájárulás {bérlői tartomány}** gombot az alkalmazás regisztrációjának **API-engedélyek** lapján. További információt a [Webes API-k hozzáférési engedélyeinek hozzáadása](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)című témakörben talál.
    - Vagy megadta a módját, hogy a felhasználók hozzájáruljanak az alkalmazáshoz. További információt az [Egyéni felhasználó hozzájárulásának kérése](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)című témakörben talál.
    - Vagy megadta a bérlői rendszergazda beleegyezését az alkalmazáshoz. További információt a [Rendszergazdai hozzájárulás című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)

- Ez a folyamat engedélyezve van a .NET asztali, a .NET Core és az UWP alkalmazásokhoz.

A hozzájárulással kapcsolatos további információkért lásd a [Microsoft identity platform engedélyeit és beleegyezését.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="learn-how-to-use-it"></a>További információ a használatról

# <a name="net"></a>[.NET](#tab/dotnet)

A MSAL.NET a következőket kell használnia:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Általában csak egy paraméterre van szüksége (`scopes`). Attól függően, hogy a Windows rendszergazdája hogyan állította be a házirendeket, előfordulhat, hogy a Windows-számítógépen lévő alkalmazások nem kereshetik meg a bejelentkezett felhasználót. Ebben az esetben használjon `.WithUsername()`egy második módszert , és adja meg a bejelentkezett felhasználó felhasználónevét upn formátumban, `joe@contoso.com`például. A .NET Core rendszerben csak a felhasználónevet átvevő túlterhelés érhető el, mert a .NET Core platform nem tudja megkérdezni a felhasználónevet az operációs rendszertől.

A következő minta bemutatja a legfrissebb esetet, a leképezések és azok enyhítései magyarázataival.

```csharp
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

Az AcquireTokenByIntegratedWindowsAuthentication lehetséges módosítóinak listáját az [AcquireTokenByIntegratedWindowsAuthParameterBuilder című témakörben található.](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="java"></a>[Java](#tab/java)

Ez a kivonat az [MSAL Java fejlesztői mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)származik.

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ez a folyamat még nem támogatott MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Ez a folyamat nem vonatkozik a MacOS-ra.

---

## <a name="username-and-password"></a>Felhasználónév és jelszó

A felhasználónév és a jelszó megadásával is beszerezhet egy jogkivonatot. Ez a folyamat korlátozott, és nem ajánlott, de még mindig vannak olyan használati esetek, ahol szükség van rá.

### <a name="this-flow-isnt-recommended"></a>Ez a folyamat nem ajánlott

Ez a folyamat *nem ajánlott,* mert az alkalmazás nem biztonságos, ha az alkalmazás jelszót kér a felhasználótól. További információ: [Mi a megoldás a jelszavak növekvő problémájára?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). A token csendes beszerzésének előnyben részesített folyamata a Windows tartományhoz csatlakozó gépeken az [integrált Windows-hitelesítés](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Használhatja [az eszközkód-folyamatot](https://aka.ms/msal-net-device-code-flow)is.

> [!NOTE]
> A felhasználónév és a jelszó használata bizonyos esetekben hasznos, például devops-forgatókönyvek. Ha azonban felhasználónevet és jelszót szeretne használni olyan interaktív forgatókönyvekben, ahol saját felhasználói felületet biztosít, gondolja át, hogyan távolodhat el tőle. Felhasználónév és jelszó használatával számos dolgot felkell adnia:
>
> - A modern identitás alaptételei. A jelszó adathalászatra és visszajátszásra kerül, mert egy megosztott titok elfogható. Nem egyeztethető össze a jelszónélküliel.
> - Azok a felhasználók, akiknek többmédia-t kell tenni, nem jelentkezhetnek be, mert nincs interakció.
> - A felhasználók nem tudnak egyszeri bejelentkezést (SSO).

### <a name="constraints"></a>Korlátozások

A következő korlátozások is érvényesek:

- A felhasználónév és a jelszó folyamat nem kompatibilis a feltételes hozzáférés és a többtényezős hitelesítés. Ennek következtében, ha az alkalmazás fut egy Azure AD-bérlő, ahol a bérlői rendszergazda többtényezős hitelesítést igényel, nem használhatja ezt a folyamatot. Sok szervezet csinálja ezt.
- Ez szerkezet egyetlen részére dolgozik és iskola számlák ( nem MSA).
- A folyamat elérhető a .NET asztalon és a .NET Core,de az UWP-n nem.

### <a name="b2c-specifics"></a>B2C sajátosságok

További információ: [Resource Owner Password Credentials (ROPC) with B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Használd.

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`tartalmazza a `AcquireTokenByUsernamePassword`módszert .

A következő minta egy egyszerűsített esetet mutat be.

```csharp
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

A következő minta bemutatja a legfrissebb esetet, a leképezések és azok enyhítései magyarázataival.

```csharp
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

A programra `AcquireTokenByUsernamePassword`alkalmazható összes módosítóról további információt a [AcquireTokenByUsernamePasswordParameterBuilder című](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)témakörben talál.

# <a name="java"></a>[Java](#tab/java)

A következő kivonat az [MSAL Java fejlesztői mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)származik.

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ez a kivonat az [MSAL Python fejlesztői mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)származik.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Ez a folyamat macOS esetén nem támogatott.

---

## <a name="command-line-tool-without-a-web-browser"></a>Parancssori eszköz webböngésző nélkül

### <a name="device-code-flow"></a>Eszközkód-áramlás

Ha olyan parancssori eszközt ír, amely nem rendelkezik webes vezérlőkkel, és nem tudja vagy nem szeretné használni az előző folyamatokat, akkor az eszközkód-folyamatot kell használnia.

Az Azure AD-vel való interaktív hitelesítéshez webböngésző szükséges. További információt [a Webböngészők használata című témakörben talál.](https://aka.ms/msal-net-uses-web-browser) A webböngészőt nem biztosító eszközökön vagy operációs rendszereken lévő felhasználók hitelesítéséhez az eszközkód-folyamat lehetővé teszi, hogy a felhasználó egy másik eszközt, például számítógépet vagy mobiltelefont használjon az interaktív bejelentkezéshez. Az eszközkód-folyamat használatával az alkalmazás jogkivonatokat szerez be egy kétlépéses folyamaton keresztül, amelyet ezekhez az eszközökhöz vagy oses-ekhez terveztek. Ilyen alkalmazások például az iOT vagy parancssori eszközökön (CLI) futó alkalmazások. Az ötlet az, hogy:

1. Amikor felhasználói hitelesítésre van szükség, az alkalmazás egy kódot biztosít a felhasználó számára. A felhasználónak egy másik eszközt, például egy internetkapcsolattal rendelkező okostelefont `https://microsoft.com/devicelogin`kell használnia, hogy egy URL-t használjon, például . Ezután a rendszer kéri a felhasználót, hogy adja meg a kódot. Ez megtörtént, a weboldal vezet a felhasználó egy normál hitelesítési élményt, amely magában foglalja a hozzájárulási kéri, és többtényezős hitelesítés, ha szükséges.

2. Sikeres hitelesítés után a parancssori alkalmazás fogadja a szükséges jogkivonatokat egy hátsó csatornán keresztül, és használja őket a szükséges webes API-hívások végrehajtásához.

### <a name="use-it"></a>Használd.

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`a . `AcquireTokenWithDeviceCode`

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Ez a módszer a következő paramétereket veszi figyelembe:

- A `scopes` hozzáférési jogkivonat kéréséhez.
- A visszahívást `DeviceCodeResult`fogadja.

  ![DeviceCodeResult tulajdonságai](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

A következő mintakód a legfrissebb esetet mutatja be, amely a leképezési és azok mérséklésének magyarázatával szolgál.

```csharp
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
# <a name="java"></a>[Java](#tab/java)

Ez a kivonat az [MSAL Java fejlesztői mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)származik.

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ez a kivonat az [MSAL Python fejlesztői mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)származik.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Ez a folyamat nem vonatkozik a MacOS-ra.

---

## <a name="file-based-token-cache"></a>Fájlalapú tokengyorsítótár

A MSAL.NET alapértelmezés szerint egy memórián belüli jogkivonat-gyorsítótár at biztosít.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>A szerializálás testreszabható a Windows asztali alkalmazásokban és webalkalmazásokban vagy webes API-kban

A . Annak megértéséhez, hogy miért nem biztosított a szerializálás a dobozból, ne feledje, hogy az MSAL .NET asztali vagy .NET Core alkalmazások lehetnek konzol- vagy Windows-alkalmazások (amelyek hozzáférhetnek a fájlrendszerhez), de webes alkalmazások at vagy webes API-kat *is.* Ezek a webes alkalmazások és webes API-k bizonyos gyorsítótár-mechanizmusokat, például adatbázisokat, elosztott gyorsítótárakat és Redis-gyorsítótárakat használhatnak. Ha állandó tokengyorsítótár-alkalmazást szeretne a .NET asztali vagy a .NET Core fájlban, testre kell szabnia a szerializálást.

A tokengyorsítótár-szerializálásban részt vevő osztályok és felületek a következő típusok:

- ``ITokenCache``, amely meghatározza a tokengyorsítótár-szerializálási kérelmekre való előfizetés eseményeit, valamint a gyorsítótár különböző formátumokban (ADAL v3.0, MSAL 2.x és MSAL 3.x = ADAL v5.0) szerializálásának vagy deszerializálásának módszereit.
- ``TokenCacheCallback``az eseményeknek átadott visszahívás, így kezelni tudja a szerializálást. A nemi típusú argumentumokkal ``TokenCacheNotificationArgs``fogják hívni őket.
- ``TokenCacheNotificationArgs``csak az ``ClientId`` alkalmazást és a felhasználóra mutató hivatkozást biztosít, amelyhez a jogkivonat elérhető.

  ![Tokengyorsítótár szerializálási diagramja](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET jogkivonat-gyorsítótárakat hoz `IToken` létre, és az alkalmazás `UserTokenCache` `AppTokenCache` és a tulajdonságok hívásakor gyorsítótárat biztosít. Nem szabad megvalósítani a felületet magad. Egyéni tokengyorsítótár-szerializálás megvalósításakor a feladata a következő:
>
> - Reagáljon `BeforeAccess` `AfterAccess` az eseményekre, illetve az *Async* megfelelőjével. A`BeforeAccess` meghatalmazott felelős a gyorsítótár deszerializálásáért. A `AfterAccess` meghatalmazott felelős a gyorsítótár szerializálásáért.
> - Megérteni, hogy ezek az események egy része tárolja vagy töltse be a blobokat, amelyek az esemény argumentumon keresztül a kívánt tárolóba kerülnek.

A stratégiák eltérőek attól függően, hogy egy nyilvános ügyfélalkalmazás, például egy asztal vagy egy bizalmas ügyfélalkalmazás, például egy webalkalmazás vagy egy webes API vagy egy démonalkalmazás tokengyorsítótár-szerializálást ír.

Az MSAL v2.x óta számos lehetőség közül választhat. A választás attól függ, hogy a gyorsítótárat csak a MSAL.NET formátumra szeretné-e szerializálni, amely az MSAL-hoz, de a platformokon is gyakori egységes formátumú gyorsítótár. Vagy érdemes lehet az ADAL v3 [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) tokengyorsítótár-szerializálását is támogatni.

A tokengyorsítótár szerializálásának testreszabása az SSO-állapot megosztásához ADAL.NET 3.x, ADAL.NET 5.x és MSAL.NET között az [active-directory-dotnet-v1-v2 minta](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)része ismerteti.

### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű tokengyorsítótár-szerializálás (csak MSAL esetén)

A következő példa az asztali alkalmazások tokengyorsítótárának egyéni szerializálásának naiv megvalósítása. Itt a felhasználói jogkivonat-gyorsítótár ugyanabban a mappában található, mint az alkalmazás.

Az alkalmazás létrehozása után az alkalmazás ``TokenCacheHelper.EnableSerialization()`` `UserTokenCache`hívásával és átadásával engedélyezheti a szerializálást.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Ez a segítő osztály a következő kódrészlethez hasonlóan néz ki:

```csharp
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

A [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú kódtárból a Windows, Mac és Linux rendszeren futó asztali alkalmazások nyilvános ügyfélalkalmazásainak termékminőségű tokengyorsítótár-fájlalapú szerializálójának előnézete érhető el. Az alkalmazásokba a következő NuGet csomagból foglalhatja bele: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Jogi nyilatkozat: A Microsoft.Identity.Client.Extensions.Msal könyvtár egy kiterjesztés MSAL.NET. Az e könyvtárakban lévő osztályok a jövőben a MSAL.NET, ahogy van, vagy a változások megszegésével.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Kettős token gyorsítótár szerializálása (MSAL egyesített gyorsítótár + ADAL v3)

Előfordulhat, hogy az egyesített gyorsítótár-formátummal szeretné megvalósítani a tokengyorsítótár szerializálását. Ez a formátum gyakori ADAL.NET 4.x és MSAL.NET 2.x, és más msals az azonos generációs vagy régebbi, ugyanazon a platformon. Merítsen ihletet a következő kódból:

```csharp
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

Ezúttal a segítő osztály a következő kódnak tűnik:

```csharp
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API hívása az asztali alkalmazásból](scenario-desktop-call-api.md)
