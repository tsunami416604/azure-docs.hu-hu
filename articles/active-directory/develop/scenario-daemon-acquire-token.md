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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28520edd8500be0da52996e6484a0407fb03c8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056449"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Webes API-kat meghívó Daemon-alkalmazás – jogkivonat beszerzése

A bizalmas ügyfélalkalmazás létrehozása után az alkalmazáshoz ``AcquireTokenForClient`` meghívásával, a hatókör átadásával, illetve a token frissítésének kényszerítésével, illetve a visszatartásával kaphat jogkivonatot.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítési folyamatra vonatkozó kérelem hatóköre az erőforrás neve, majd `/.default`. Ez a jelölés azt jelzi, hogy az Azure AD az alkalmazás regisztrálása során statikusan deklarált **alkalmazási szintű engedélyeket** használ. Emellett, ahogy azt korábban is láttuk, a bérlői rendszergazdának kell megadnia az API-engedélyeket

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A MSAL. Python, a konfigurációs fájl a következő kódrészlethez hasonlóan fog kinézni:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD-(v 1.0-) erőforrások esetén

Az ügyfél hitelesítő adataihoz használt hatókörnek mindig resourceId + "/.default" értéknek kell lennie

> [!IMPORTANT]
> A v 1.0 hozzáférési jogkivonatot elfogadó erőforrás hozzáférési jogkivonatának MSAL az Azure AD a kért hatókörből elemezi a kívánt célközönséget azáltal, hogy az utolsó perjel előtt mindent megtesz, és használja erőforrás-azonosítóként.
> Ezért ha, például az Azure SQL ( **https://database.windows.net** ) esetében az erőforrás egy perjelet (az Azure sql: `https://database.windows.net/` esetében) végződő célközönséget vár, akkor a `https://database.windows.net//.default` hatókörét kell kérnie (jegyezze fel a dupla perjelet). Lásd még MSAL.NET probléma [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Az erőforrás URL-címének záró perjele ki van hagyva, ami az SQL-hitelesítési hibát okozta.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Az alkalmazáshoz tartozó jogkivonatok beszerzéséhez a platformtól függően `AcquireTokenForClient` vagy azzal egyenértékű értéket kell használnia.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
    print(result["expires_in"])  # You don't normally need to care about this.
                                 # It will be good for at least 5 minutes.
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

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

További információt a protokoll dokumentációjában talál: [A Microsoft Identity platform és a OAuth 2,0 ügyfél-hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Alkalmazás-jogkivonat gyorsítótára

A MSAL.NET-ben a `AcquireTokenForClient` az **alkalmazás-jogkivonat gyorsítótárát** használja (az összes többi AcquireTokenXX-módszer a felhasználói jogkivonat-gyorsítótárat használja), mielőtt a `AcquireTokenForClient` hívása előtt meghívja a `AcquireTokenSilent` metódust, `AcquireTokenSilent` a **felhasználói** jogkivonat gyorsítótárát használja. a `AcquireTokenForClient` ellenőrzi, hogy az **alkalmazás** -jogkivonat gyorsítótára magát, és frissíti.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="did-you-use-the-resourcedefault-scope"></a>Használta az erőforrás/. alapértelmezett hatókört?

Ha hibaüzenet jelenik meg arról, hogy érvénytelen hatókört használt, valószínűleg nem használta a `resource/.default` hatókört.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Elfelejtette, hogy rendszergazdai engedélyt adjon? Daemon-alkalmazások szükségesek!

Ha hibaüzenet jelenik meg, amikor az API-nak nem **megfelelő jogosultsága van a művelet végrehajtásához**, a bérlő rendszergazdájának engedélyeket kell adnia az alkalmazás számára. Lásd a fenti ügyfélalkalmazás regisztrálásának 6. lépését.
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