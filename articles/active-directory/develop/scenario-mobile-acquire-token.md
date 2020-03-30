---
title: Beszerezni egy token hívni egy webes API (mobil alkalmazások) | Azure
titleSuffix: Microsoft identity platform
description: További információ a webes API-kat meghívjaó mobilalkalmazás létrehozásáról. (Token beszereznie az alkalmazáshoz.)
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
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132453"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Get egy token egy mobilalkalmazás, amely felhívja a webes API-k

Ahhoz, hogy az alkalmazás meghívja a védett webes API-kat, szüksége van egy hozzáférési jogkivonatra. Ez a cikk végigvezeti a folyamaton, hogy a Microsoft Authentication Library (MSAL) használatával tokeneket szerezzen be.

## <a name="define-a-scope"></a>Hatókör definiálása

Amikor jogkivonatot kér, meg kell határoznia egy hatókört. A hatókör határozza meg, hogy az alkalmazás milyen adatokhoz férhet hozzá.  

A hatókör meghatározásának legegyszerűbb módja a kívánt webes `App ID URI` API-k `.default`és a hatókör kombinálása. Ez a definíció közli a Microsoft identitásplatformjával, hogy az alkalmazásnak minden, a portálon beállított hatókörre szüksége van.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Tokenek beszereznie

### <a name="acquire-tokens-via-msal"></a>Tokenek beszerzése az MSAL-on keresztül

Az MSAL lehetővé teszi az alkalmazások számára, hogy a tokeneket csendesen és interaktívan szerezzék be. Amikor a `AcquireTokenSilent()` `AcquireTokenInteractive()`hívás vagy a , MSAL a kért hatókörök hozzáférési jogkivonatot ad vissza. A helyes minta az, hogy egy csendes kérelmet, majd esik vissza egy interaktív kérés.

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
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

Először próbáljon meg csendben megszerezni egy tokent:

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

Ha az `MSALErrorInteractionRequired`MSAL visszatér, próbálja meg interaktív módon beszerezni a jogkivonatokat:

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

Az MSAL iOS és macOS rendszeren támogatja a különböző módosítókat, hogy interaktívan vagy csendben szerezzenek be egy jogkivonatot:
* [A token megszerzésének gyakori paraméterei](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Interaktív jogkivonat beszerzésének paraméterei](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Csendes token beszerzésének paraméterei](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

A következő példa bemutatja a minimális kódot, hogy egy token interaktívan. A példa a Microsoft Graph segítségével olvassa be a felhasználó profilját.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Kötelező paraméterek a MSAL.NET

`AcquireTokenInteractive`csak egy kötelező `scopes`paraméterrel rendelkezik: . A `scopes` paraméter enumerálja azokat a karakterláncokat, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat a Microsoft Graph, megtalálhatja a szükséges hatóköröket az egyes Microsoft Graph API-k API-hivatkozásában. A hivatkozásban lépjen az "Engedélyek" szakaszra. 

A felhasználó [névjegyeinek listázásához](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)például használja a "User.Read", "Contacts.Read" tartományt. További információt a [Microsoft Graph engedélyek hivatkozása című témakörben talál.](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)

Android on megadhatja a szülőtevékenységet, amikor `PublicClientApplicationBuilder`létrehozza az alkalmazást a használatával. Ha akkor nem adja meg a fölérendelt tevékenységet, később `.WithParentActivityOrWindow` a következő szakaszban megadott módon adhatja meg. Ha megadja a fölérendelt tevékenységet, akkor a jogkivonat a kapcsolati tevékenység után visszatér a fölérendelt tevékenységhez. Ha nem adja meg, akkor `.ExecuteAsync()` a hívás kivételt okoz.

#### <a name="specific-optional-parameters-in-msalnet"></a>Speciális választható paraméterek a MSAL.NET

A következő szakaszok ismertetik a nem kötelező paramétereket MSAL.NET. 

##### <a name="withprompt"></a>Prompt (Gyors)

A `WithPrompt()` paraméter egy kérdés megadásával szabályozza a felhasználóval való interaktivitást.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő állandókat határozza meg:

- `SelectAccount`kényszeríti a biztonsági jogkivonat-szolgáltatást (STS) a fiókkijelölési párbeszédpanel bemutatására. A párbeszédpanel azokat a fiókokat tartalmazza, amelyekhez a felhasználó munkamenetet tart. Ezt a beállítást akkor használhatja, ha azt szeretné, hogy a felhasználó válasszon a különböző identitások közül. Ez a beállítás az `prompt=select_account` MSAL-t az identitásszolgáltatónak való küldésre ösztönzi. 
    
    Az `SelectAccount` állandó az alapértelmezett, és hatékonyan biztosítja a lehető legjobb élményt a rendelkezésre álló információk alapján. A rendelkezésre álló információk közé tartozhat a fiók, a munkamenet jelenléte a felhasználó számára, és így tovább. Ne módosítsa ezt az alapértelmezett beállítást, hanincs rá jó oka.
- `Consent`lehetővé teszi, hogy a felhasználóbeleegyezést kérjen, még akkor is, ha korábban megadták a hozzájárulást. Ebben az esetben az `prompt=consent` MSAL elküldi az identitásszolgáltatónak. 

    Előfordulhat, hogy az `Consent` állandót a biztonságra összpontosító alkalmazásokban szeretné használni, ahol a szervezet irányítási vállalata megköveteli a felhasználóktól, hogy minden alkalommal lássák a hozzájárulási párbeszédpanelt, amikor az alkalmazást használják.
- `ForceLogin`lehetővé teszi, hogy a szolgáltatás akkor is kérje a felhasználótól a hitelesítő adatokat, ha nincs rá szükség. 

    Ez a beállítás akkor lehet hasznos, ha a jogkivonat-beszerzés sikertelen, és szeretné, hogy a felhasználó újra jelentkezzen be. Ebben az esetben az `prompt=login` MSAL elküldi az identitásszolgáltatónak. Ezt a beállítást olyan biztonságközpontú alkalmazásokban is használhatja, ahol a szervezet irányítási vállalata megköveteli a felhasználótól, hogy minden alkalommal jelentkezzen be, amikor az alkalmazás adott részeihez fér hozzá.
- `Never`csak a .NET 4.5 és a Windows Runtime (WinRT) esetén. Ez az állandó nem kéri a felhasználót, de megpróbálja használni a rejtett beágyazott webes nézetben tárolt cookie-t. További információt a [Webböngészők használata MSAL.NET című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)

    Ha ez a `AcquireTokenInteractive` beállítás nem sikerül, kivételt küld, hogy értesítse, hogy felhasználói felületi interakcióra van szükség. Ezután egy másik `Prompt` paramétert kell használnia.
- `NoPrompt`nem küld kérdést az identitásszolgáltatónak. 

    Ez a beállítás csak az Azure Active Directory B2C-ben lévő profilházirendek szerkesztéséhez hasznos. További információ: [B2C specifics](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>Extrascopetohozzájárulással

Használja `WithExtraScopeToConsent` a módosító egy speciális forgatókönyv, ahol azt szeretné, hogy a felhasználó több erőforrás előzetes beleegyezését adja. Ezt a módosítót akkor használhatja, ha nem szeretne növekményes hozzájárulást használni, amelyet általában MSAL.NET vagy a Microsoft identity platform 2.0-s rendszerével használnak. További információ: [A felhasználó hozzájárulásának előzetes biztosítása több erőforráshoz.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

Íme egy példa egy kódra: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Egyéb választható paraméterek

A további választható paraméterekről `AcquireTokenInteractive`a [AcquireTokenInteractiveParameterBuilder referenciadokumentációjában](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)olvashat.

### <a name="acquire-tokens-via-the-protocol"></a>Tokenek beszerzése a protokollon keresztül

Nem javasoljuk, hogy közvetlenül használja a protokollt a tokenek lekéréséhez. Ha igen, majd az alkalmazás nem támogatja az egyes forgatókönyvek, amelyek magukban foglalják az egyszeri bejelentkezés (SSO), eszközkezelés és feltételes hozzáférés.

Ha a protokoll segítségével kap tokeneket a mobilalkalmazásokhoz, két kérést kell kérnie: 

* Szerezzen be egy engedélyezési kódot.
* Cserélje ki a kódot egy tokenre.

#### <a name="get-an-authorization-code"></a>Engedélyezési kód beszerezni

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Hozzáférés és a token frissítése

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API hívása](scenario-mobile-call-api.md)
