---
title: Mobilalkalmazás, hogy a hívások webes API-k – az alkalmazás egy token beszerzése |} A Microsoft identity platform
description: Ismerje meg a mobilalkalmazások, amely meghívja a webes API-k (tokenbeolvasás az alkalmazás)
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
ms.openlocfilehash: 6933bfbbff574495655ef9065a786fa313b02bd6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075175"
---
# <a name="mobile-app-that-calls-web-apis---acquire-a-token"></a>Mobilalkalmazás, amely meghívja a webes API - jogkivonat beszerzése

Megkezdéséhez hívása a védett webes API-k, az alkalmazás kell egy hozzáférési jogkivonatot. Ez a szakasz végigvezeti a folyamatot a Microsoft-hitelesítési tár (MSAL) használatával jogkivonat beszerzéséhez.

## <a name="scopes-to-request"></a>Hatókörök kérni

Ha kéri a jogkivonatokat, hatókör mindig szükség. A hatókör határozza meg, hogy az alkalmazás hozzáférhessen-adatokat.  

A legegyszerűbb úgy, hogy a kívánt webes API-k `App ID URI` a hatókörrel rendelkező `.default`. Közli a Microsoft identity az adott alkalmazáshoz szükséges a portálon beállított összes hatókör.

Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="acquiring-tokens"></a>-Jogkivonatok beszerzésének

### <a name="via-msal"></a>via MSAL

Az MSAL lehetővé teszi, hogy a tokenek beszerzésére, csendes és interaktív alkalmazásokhoz. Csak az ezen módszerek hívja, és az MSAL vissza egy hozzáférési jogkivonatot ad vissza a kért hatókörhöz. A megfelelő mintára a beavatkozás nélküli kérelem és a egy interaktív kérelemre tartalék végrehajtásához.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently
// Result is in our silent callback (success or error)
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or >1 account */
        }
    }
});    

[...]

// No accounts found, interactively request a token 
// TODO: Create an interactive callback to catch successful or failed request
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize our app 
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means we need to ask the user to sign-in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
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

    // Token is ready via silent acquisition 
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

### <a name="via-protocol"></a>Protokoll segítségével

Nem ajánlott közvetlenül a protokoll szemben. Az alkalmazás nem lesz képes a számos egyszeri bejelentkezéses (SSO) forgatókönyvek, és nem lesz képes Eszközkezelés és a feltételes hozzáférési forgatókönyvek támogatásához.

A mobile apps, a protokollal a jogkivonatok lekérésének lépéseiről, ha 2 kéréseit kell: hozzáférési kód beszerzése és az exchange-, a jogkivonat. 

#### <a name="getting-authorization-code"></a>Engedélyezési kód beolvasása

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="getting-access-and-refresh-token"></a>Hozzáférés és a frissítési jogkivonat beolvasása

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
