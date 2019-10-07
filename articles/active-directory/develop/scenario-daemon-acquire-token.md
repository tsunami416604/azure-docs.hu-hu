---
title: Daemon-alkalmazás webes API-k meghívása (tokenek beszerzése az alkalmazáshoz) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre olyan Daemon-alkalmazást, amely webes API-kat hív meg (tokenek beszerzése)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562356"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Webes API-kat meghívó Daemon-alkalmazás – jogkivonat beszerzése

A bizalmas ügyfélalkalmazás létrehozása után a jogkivonat meghívásával ``AcquireTokenForClient``, a hatókör átadásával, valamint a jogkivonat frissítésének kényszerítésével vagy nem történő megszerzésével kaphat jogkivonatot az alkalmazáshoz.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítő adatokra vonatkozó kérelem hatóköre az erőforrás neve, majd `/.default`a. Ez a jelölés azt jelzi, hogy az Azure AD az alkalmazás regisztrálása során statikusan deklarált **alkalmazási szintű engedélyeket** használ. Emellett, ahogy azt korábban is láttuk, a bérlői rendszergazdának kell megadnia az API-engedélyeket

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

A MSAL. Python, a konfigurációs fájl a következő kódrészlethez hasonlóan fog kinézni:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Összes

Az ügyfél hitelesítő adataihoz használt hatókörnek mindig resourceId + "/.default" értéknek kell lennie

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD-(v 1.0-) erőforrások esetén

> [!IMPORTANT]
> A MSAL (Microsoft Identity platform Endpoint) esetében egy v 1.0 hozzáférési jogkivonatot elfogadó erőforrás hozzáférési jogkivonatát kérdezi le az Azure AD, amely az utolsó perjel előtt mindent megtesz, és használja erőforrás-azonosítóként.
> Ezért ha például az Azure SQL ( **https://database.windows.net** ) az erőforrás egy perjelet (az Azure SQL: `https://database.windows.net/` esetében) végződő célközönséget vár, akkor a hatókört `https://database.windows.net//.default` kell kérnie (jegyezze fel a dupla perjelet). Lásd még MSAL.NET probléma [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Az erőforrás URL-címének záró perjele ki van hagyva, ami az SQL-hitelesítési hibát okozta.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Alkalmazás-jogkivonat gyorsítótára

`AcquireTokenForClient` A MSAL.net-ben az **alkalmazás-jogkivonat gyorsítótára** (az összes többi AcquireTokenXX-módszer a felhasználói jogkivonat-gyorsítótár `AcquireTokenSilent` használata) `AcquireTokenForClient` nem `AcquireTokenSilent` hívja meg a **felhasználói** jogkivonat-gyorsítótárat használó hívás előtt. `AcquireTokenForClient`ellenőrzi az **alkalmazás** -jogkivonat gyorsítótárát, és frissíti azt.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Ha még nem rendelkezik a választott nyelvhez tartozó könyvtárral, érdemes lehet közvetlenül a protokollt használni:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem közös titokkal

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>További információ a protokollról

További információt a protokoll dokumentációjában talál: [A Microsoft Identity platform és a OAuth 2,0 ügyfél-hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="did-you-use-the-resourcedefault-scope"></a>Használta az erőforrás/. alapértelmezett hatókört?

Ha hibaüzenet jelenik meg arról, hogy érvénytelen hatókört használt, akkor valószínűleg nem használta a `resource/.default` hatókört.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Elfelejtette, hogy rendszergazdai engedélyt adjon? Daemon-alkalmazások szükségesek!

Ha hibaüzenet jelenik meg, amikor az API-nak nem megfelelő jogosultsága van a **művelet végrehajtásához**, a bérlő rendszergazdájának engedélyeket kell adnia az alkalmazás számára. Lásd a fenti ügyfélalkalmazás regisztrálásának 6. lépését.
Általában a következő hibához hasonló hibaüzenet jelenik meg:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – webes API meghívása](scenario-daemon-call-api.md)