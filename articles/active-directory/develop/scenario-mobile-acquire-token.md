---
title: Webes API-kat meghívó mobil alkalmazás – token beszerzése az alkalmazáshoz | Microsoft Identity platform
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
ms.openlocfilehash: d49717355cab5441d26608fa12333bd1b8b73d44
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413530"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Webes API-kat meghívó mobil alkalmazás – jogkivonat beolvasása

A védett webes API-k hívásának megkezdése előtt az alkalmazásnak hozzáférési jogkivonatra lesz szüksége. Ez a cikk végigvezeti a jogkivonat beszerzésének folyamatán a Microsoft Authentication Library (MSAL) használatával.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Ha jogkivonatot kér, meg kell határoznia egy hatókört. A hatókör határozza meg, hogy az alkalmazás milyen adataihoz férhet hozzá.  

A legegyszerűbb módszer a kívánt webes API-k `App ID URI` és a hatókör `.default`összevonása. Ez azt jelzi, hogy az alkalmazásnak szüksége van a Microsoft Identity platformra, amely a portálon beállított összes hatókört igényli.

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

## <a name="get-tokens"></a>Jogkivonatok beolvasása

### <a name="via-msal"></a>Via MSAL

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

### <a name="mandatory-parameters"></a>Kötelező paraméterek

`AcquireTokenInteractive`csak egy kötelező paramétert ``scopes``tartalmaz, amely tartalmazza a karakterláncok enumerálását, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat a Microsoft Graph, a szükséges hatókörök az "engedélyek" szakaszban található minden Microsoft Graph API API-referenciájában találhatók. A [felhasználó névjegyeinek listázásához](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)például a "felhasználó. Read", a "Contacts. Read" hatókört kell használni. Lásd még [Microsoft Graph engedélyek referenciáját](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Ha az alkalmazás létrehozásakor nem adta meg az alkalmazást, az Androidon meg kell adnia a szülő tevékenységet is (a használatával `.WithParentActivityOrWindow`, lásd alább), hogy a token visszakerüljön az adott szülő tevékenységre az interakció után. Ha nem ad meg, kivételt fog a rendszer a hívásakor `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Megadott választható paraméterek

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`a felhasználóval való interaktivitás vezérlésére szolgál egy kérdés megadásával

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Az osztály a következő konstansokat határozza meg:

- ``SelectAccount``: kényszeríti az STS-t, hogy bemutassa a fiók kiválasztása párbeszédpanelt, amely olyan fiókokat tartalmaz, amelyekhez a felhasználó rendelkezik munkamenettel. Ez a beállítás akkor hasznos, ha az alkalmazások fejlesztői számára engedélyezni szeretné a felhasználók számára a különböző identitások közötti választást. Ezzel a beállítással a MSAL ``prompt=select_account`` küldheti az identitás-szolgáltatónak. Ez az alapértelmezett beállítás, és jó munkát végez a lehető legjobb élmény biztosításához a rendelkezésre álló információk (fiók, a felhasználó munkamenetének jelenléte stb.) alapján. ...). Csak akkor módosítsa, ha nincs megfelelő oka.
- ``Consent``: lehetővé teszi, hogy az alkalmazás fejlesztője kényszerítse a felhasználót abban az esetben is, ha beleegyezett a beleegyező engedély megadására. Ebben az esetben a MSAL elküldi `prompt=consent` az identitás-szolgáltatónak. Ez a beállítás olyan biztonsági szempontokat használó alkalmazásokban használható, ahol a szervezet irányítása megköveteli, hogy a felhasználó az alkalmazás minden egyes használatakor megtekintse a beleegyezik párbeszédpanelt.
- ``ForceLogin``: lehetővé teszi, hogy az alkalmazás fejlesztője akkor is kérje a felhasználótól a hitelesítő adatok megadását, ha ez a felhasználó nem lenne szükséges. Ez a beállítás akkor lehet hasznos, ha a jogkivonat beszerzése meghiúsul, hogy a felhasználó újra bejelentkezzen. Ebben az esetben a MSAL elküldi `prompt=login` az identitás-szolgáltatónak. Azt is láttuk, hogy az egyes biztonsági irányultságú alkalmazásokban, ahol a szervezeti irányítás megköveteli, hogy a felhasználó újrajelentkezzen – minden alkalommal, amikor hozzáférnek az alkalmazás egyes részeihez.
- ``Never``(csak a .NET 4,5 és a WinRT esetében) nem fogja kérni a felhasználót, hanem a rejtett beágyazott webes nézetben tárolt cookie-t próbálja használni (lásd alább: Webes nézetek a MSAL.NET-ben). Ha ez a beállítás nem sikerül, és ebben az `AcquireTokenInteractive` esetben a rendszer kivételt jelez arról, hogy a felhasználói felületi interakcióra van szükség, és egy `Prompt` másik paramétert kell használnia.
- ``NoPrompt``: A rendszer nem küld figyelmeztetést az identitás-szolgáltatónak. Ez a lehetőség csak Azure AD B2C profil-házirendek szerkesztéséhez használható (lásd a [B2C](https://aka.ms/msal-net-b2c-specificities)-specifikusokat).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ez a módosító olyan speciális forgatókönyvekben használatos, ahol a felhasználónak előre jóvá kell hagynia több erőforrást (és nem szeretné használni a növekményes beleegyezést, amelyet általában a MSAL.NET/a Microsoft Identity platform 2.0-s verziójában használ). A részletekért lásd [: a felhasználó beleegyezett több erőforrásra](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="other-optional-parameters"></a>Egyéb választható paraméterek

További információ az `AcquireTokenInteractive` [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) dokumentációjában található további választható paraméterekről

### <a name="via-the-protocol"></a>A protokollon keresztül

A protokollt nem ajánlott közvetlenül használni. Ha így tesz, az alkalmazás nem támogatja az egyszeri bejelentkezést (SSO), az eszközkezelés és a feltételes hozzáférési forgatókönyveket.

Ha a protokollt használja a Mobile apps-jogkivonatok beszerzéséhez, két kérést kell tennie: Kérjen egy engedélyezési kódot, és cserélje ki egy tokenre.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API meghívása](scenario-mobile-call-api.md)
