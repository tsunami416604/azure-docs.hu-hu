---
title: Webes API-kat meghívó mobil alkalmazás – token beszerzése az alkalmazáshoz
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre olyan mobil alkalmazást, amely meghívja a webes API-kat (az alkalmazás jogkivonatának beszerzése)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b1d6a0e4bfffaaf4ed0fa836bcf62a7f1104e6a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803768"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Webes API-kat meghívó mobil alkalmazás – jogkivonat beolvasása

A védett webes API-k hívásának megkezdése előtt az alkalmazásnak hozzáférési jogkivonatra lesz szüksége. Ez a cikk végigvezeti a jogkivonat beszerzésének folyamatán a Microsoft Authentication Library (MSAL) használatával.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Ha jogkivonatot kér, meg kell határoznia egy hatókört. A hatókör határozza meg, hogy az alkalmazás milyen adataihoz férhet hozzá.  

A legegyszerűbb módszer a kívánt webes API-`App ID URI` és a hatókör `.default`ének összevonása. Ez azt jelzi, hogy az alkalmazásnak szüksége van a Microsoft Identity platformra, amely a portálon beállított összes hatókört igényli.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Jogkivonatok beolvasása

### <a name="acquire-tokens-via-msal"></a>Jogkivonatok beszerzése a MSAL-on keresztül

A MSAL lehetővé teszi, hogy az alkalmazások csendes és interaktív módon szerezzenek jogkivonatokat. Csak hívja meg ezeket a metódusokat, és a MSAL egy hozzáférési jogkivonatot ad vissza a kért hatókörökhöz. A helyes minta egy csendes kérés végrehajtása és egy interaktív kérelemre való visszatérés.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Először próbálkozzon a tokenek beszerzésével:**

Objective-C:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Swift

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
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
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Ha a MSAL visszaadja a `MSALErrorInteractionRequired`t, próbálja meg interaktív módon beszerezze a jogkivonatokat:**

Objective-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

Az iOS és a macOS rendszerhez készült MSAL különböző módosítókat támogat, ha a tokent interaktívan vagy csendben keresi.
* [Általános paraméterek token beolvasásakor](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Az interaktív tokenek beszerzésének paraméterei](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [A csendes jogkivonat beszerzésének paraméterei](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Az alábbi példa azt mutatja be, hogy a jogkivonat interaktív beolvasása a felhasználó profiljának Microsoft Graph használatával történő olvasásához szükséges minimális kód.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Kötelező paraméterek a MSAL.NET

`AcquireTokenInteractive` csak egy kötelező paraméterrel rendelkezik ``scopes``, amely tartalmazza a karakterláncok enumerálását, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat a Microsoft Graph, a szükséges hatókörök az "engedélyek" szakaszban található minden Microsoft Graph API API-referenciájában találhatók. A [felhasználó névjegyeinek listázásához](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)például a "felhasználó. Read", a "Contacts. Read" hatókört kell használni. Lásd még [Microsoft Graph engedélyek referenciáját](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Ha az alkalmazás létrehozásakor nem adta meg az alkalmazást, az Androidon meg kell adnia a szülő tevékenységet is (`.WithParentActivityOrWindow`használatával, lásd alább), hogy a token visszakerüljön az adott szülő tevékenységre az interakció után. Ha nem ad meg, a rendszer kivételt fog kiváltani `.ExecuteAsync()`meghívásakor.

#### <a name="specific-optional-parameters-in-msalnet"></a>Speciális választható paraméterek a MSAL.NET

##### <a name="withprompt"></a>WithPrompt

a `WithPrompt()` a felhasználóval való interaktivitás vezérlésére szolgál egy kérdés megadásával

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő konstansokat határozza meg:

- ``SelectAccount``: kényszeríti az STS-t, hogy bemutassa a fiók kiválasztása párbeszédpanelt, amely olyan fiókokat tartalmaz, amelyekhez a felhasználó rendelkezik munkamenettel. Ez a beállítás akkor hasznos, ha az alkalmazások fejlesztői számára engedélyezni szeretné a felhasználók számára a különböző identitások közötti választást. Ez a beállítás a MSAL ``prompt=select_account`` küldését hajtja az identitás-szolgáltatónak. Ez az alapértelmezett beállítás, és jó munkát végez a lehető legjobb élmény biztosításához a rendelkezésre álló információk (fiók, a felhasználó munkamenetének jelenléte stb.) alapján. ...). Csak akkor módosítsa, ha nincs megfelelő oka.
- ``Consent``: lehetővé teszi, hogy az alkalmazás fejlesztője kényszerítse a felhasználót abban az esetben is, ha a beleegyező engedélyt megadták. Ebben az esetben a MSAL `prompt=consent` küld az identitás-szolgáltatónak. Ez a beállítás olyan biztonsági szempontokat használó alkalmazásokban használható, ahol a szervezet irányítása megköveteli, hogy a felhasználó az alkalmazás minden egyes használatakor megtekintse a beleegyezik párbeszédpanelt.
- ``ForceLogin``: lehetővé teszi, hogy az alkalmazás fejlesztője akkor is kérje a felhasználótól a hitelesítő adatok megadását, ha ez a felhasználó nem lenne szükséges. Ez a beállítás akkor lehet hasznos, ha a jogkivonat beszerzése meghiúsul, hogy a felhasználó újra bejelentkezzen. Ebben az esetben a MSAL `prompt=login` küld az identitás-szolgáltatónak. Azt is láttuk, hogy az egyes biztonsági irányultságú alkalmazásokban, ahol a szervezeti irányítás megköveteli, hogy a felhasználó újrajelentkezzen – minden alkalommal, amikor hozzáférnek az alkalmazás egyes részeihez.
- ``Never`` (csak a .NET 4,5-es és a WinRT esetén) nem kéri a felhasználót, hanem a rejtett beágyazott webes nézetben tárolt cookie-t próbálja használni (lásd alább: webes nézetek a MSAL.NET-ben). Ha ez a beállítás nem sikerül, és ebben az esetben `AcquireTokenInteractive` kivételt jelez, hogy a felhasználói felületi interakcióra van szükség, és egy másik `Prompt` paramétert kell használnia.
- ``NoPrompt``: a rendszer nem küld figyelmeztetést az identitás-szolgáltatónak. Ez a lehetőség csak Azure AD B2C profil-házirendek szerkesztéséhez használható (lásd a [B2C-specifikusokat](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ez a módosító olyan speciális forgatókönyvekben használatos, ahol a felhasználónak előre jóvá kell hagynia több erőforrást (és nem szeretné használni a növekményes beleegyezést, amelyet általában a MSAL.NET/a Microsoft Identity platform 2.0-s verziójában használ). A részletekért lásd [: a felhasználó beleegyezett több erőforrásra](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Egyéb választható paraméterek

További információ a [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) -hez készült dokumentációban `AcquireTokenInteractive` további opcionális paraméterekről

### <a name="acquire-tokens-via-the-protocol"></a>Jogkivonatok beszerzése a protokollon keresztül

A protokollt nem ajánlott közvetlenül használni. Ha így tesz, az alkalmazás nem támogatja az egyszeri bejelentkezést (SSO), az eszközkezelés és a feltételes hozzáférési forgatókönyveket.

Ha a protokollt használja a Mobile apps-jogkivonatok beszerzéséhez, két kérést kell tennie: Kérjen egy engedélyezési kódot, és cserélje ki egy tokenre.

#### <a name="get-authorization-code"></a>Engedélyezési kód lekérése

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Hozzáférési és frissítési jogkivonat beolvasása

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API meghívása](scenario-mobile-call-api.md)
