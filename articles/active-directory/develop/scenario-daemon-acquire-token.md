---
title: Jogkivonatok beszerzése webes API-k meghívásához (Daemon-alkalmazás) – Microsoft Identity platform | Azure
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38df99f0a4932f477e900382c7ff1ae7b50febe9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702470"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Webes API-kat meghívó Daemon-alkalmazás – jogkivonat beszerzése

A bizalmas ügyfélalkalmazás létrehozása után a ``AcquireTokenForClient``meghívásával, a hatókör átadásával, illetve a token frissítésének kényszerítésével vagy nem a jogkivonat frissítésével beszerezheti az alkalmazás jogkivonatát.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítési folyamatra vonatkozó kérelem hatóköre az erőforrás neve, amelyet a `/.default`követ. Ez a jelölés azt jelzi, hogy az Azure AD az alkalmazás regisztrálása során statikusan deklarált **alkalmazási szintű engedélyeket** használ. Emellett, ahogy azt korábban is láttuk, a bérlői rendszergazdának kell megadnia az API-engedélyeket

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A MSAL Pythonban a konfigurációs fájl a következő kódrészlethez hasonlóan fog kinézni:

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
> Ezért ha például az Azure SQL ( **https://database.windows.net** ), az erőforrás egy perjelet (az Azure sql: `https://database.windows.net/` ) végződő célközönséget vár, a `https://database.windows.net//.default` hatókörét kell kérnie (jegyezze fel a dupla perjelet). Lásd még: MSAL.NET probléma [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): az erőforrás URL-címének záró perjele ki van hagyva, ami SQL-hitelesítési hibát okozott.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Az alkalmazáshoz tartozó jogkivonatok beszerzéséhez a platformtól függően `AcquireTokenForClient` vagy azzal egyenértékű jogosultságot kell használnia.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
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
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ez a [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)kivonata.

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

### <a name="protocol"></a>Protocol (Protokoll)

Ha még nem rendelkezik a választott nyelvhez tartozó könyvtárral, érdemes lehet közvetlenül a protokollt használni:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: hozzáférési jogkivonat-kérelem közös titokkal

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: hozzáférési jogkivonat kérése tanúsítvánnyal

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

További információkért tekintse meg a protokoll dokumentációját: [Microsoft Identity platform és a OAuth 2,0 Client hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Alkalmazás-jogkivonat gyorsítótára

A MSAL.NET-ben a `AcquireTokenForClient` az **alkalmazás-jogkivonat gyorsítótárát** használja (az összes többi AcquireTokenXX-módszer a felhasználói jogkivonat-gyorsítótárat használja), mielőtt meghívja a `AcquireTokenSilent`t a **felhasználói** jogkivonat gyorsítótárának meg`AcquireTokenForClient` hívása előtt `AcquireTokenSilent` használja. `AcquireTokenForClient` ellenőrzi az **alkalmazás** -jogkivonat gyorsítótárát, és frissíti azt.

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

## <a name="next-steps"></a>Következő lépések

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – webes API meghívása](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – webes API meghívása](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – webes API meghívása](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
