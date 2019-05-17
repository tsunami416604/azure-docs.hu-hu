---
title: A mobilalkalmazás, hogy a hívások webes API-k – az alkalmazás jogkivonatának beolvasása |} A Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre, amely meghívja a webes API-kat (az alkalmazás egy token beszerzése) mobilalkalmazás
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88c9215ed221e24099eeb219a4db599a1955920a
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550340"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Mobilalkalmazás, amely meghívja a webes API-k – egy token beszerzése

Megkezdéséhez hívása a védett webes API-k, az alkalmazás kell egy hozzáférési jogkivonatot. Ez a cikk végigvezeti a folyamat az első egy tokent a Microsoft-hitelesítési tár (MSAL) használatával.

## <a name="scopes-to-request"></a>Hatókörök kérni

Ha egy tokent kér, kell hatókör meghatározása. A hatókör határozza meg, hogy az alkalmazás hozzáférhessen-adatokat.  

A legegyszerűbb megközelítés, ha a kívánt webes API-kat kombinálja `App ID URI` a hatókörrel rendelkező `.default`. Ezzel arra utasítja a Microsoft identity platform adott alkalmazáshoz szükséges összes hatókör beállítása a portálon.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>A jogkivonatok lekérésére

### <a name="via-msal"></a>Via MSAL

Az MSAL lehetővé teszi, hogy a tokenek beszerzésére, csendes és interaktív alkalmazásokhoz. Csak az ezen módszerek hívja, és az MSAL adja vissza egy hozzáférési jogkivonatot a kért hatókörhöz. A helyes mintát, hogy hajtsa végre a beavatkozás nélküli kérést, és egy interaktív kérelem térhet vissza.

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

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens.
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means you need to ask the user to sign in. This usually happens
        // when the user's refresh token is expired or when the user has changed the password,
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition.
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

```CSharp
string[] scopes = new string["https://graph.microsoft.com/.default"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException e)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="via-the-protocol"></a>A protokoll segítségével

Nem ajánlott közvetlenül a protokoll használatával. Ha így tesz, az alkalmazás nem támogatja a bizonyos egyszeri bejelentkezés (SSO), eszközkezelés, és a feltételes hozzáférési forgatókönyvek.

A protokoll használatával tokenekhez mobilalkalmazásokhoz, amikor két kérést kell: hozzáférési kód beszerzése és az exchange-, a jogkivonat.

#### <a name="get-authorization-code"></a>Engedélyezési kód beszerzése

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Hozzáférés és a frissítési token beszerzése

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
> [A webes API meghívása](scenario-mobile-call-api.md)
