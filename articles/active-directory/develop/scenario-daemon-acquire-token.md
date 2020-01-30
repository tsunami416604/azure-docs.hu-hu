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
ms.openlocfilehash: b2d388160c6ca744b10c17bda17c59e22940f98b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775243"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Webes API-kat meghívó Daemon-alkalmazás – jogkivonat beszerzése

A bizalmas ügyfélalkalmazás létrehozása után a `AcquireTokenForClient`meghívásával, a hatókör átadásával, valamint a token frissítésének megkezdésével megadhatja az alkalmazás jogkivonatát.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítési folyamatra vonatkozó kérelem hatóköre az erőforrás neve, amelyet a `/.default`követ. Ez a jelölés azt mutatja Azure Active Directory (Azure AD) számára, hogy az alkalmazás regisztrálása során statikusan deklarált *alkalmazási szintű engedélyeket* használjon. Emellett a bérlői rendszergazdának is meg kell adni ezeket az API-engedélyeket.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A MSAL Pythonban a konfigurációs fájl az alábbi kódrészlethez hasonlít:

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

### <a name="azure-ad-v10-resources"></a>Azure AD-erőforrások (v 1.0)

Az ügyfél hitelesítő adataihoz használt hatókörnek mindig az erőforrás-AZONOSÍTÓnak kell lennie, majd `/.default`.

> [!IMPORTANT]
> Ha a MSAL hozzáférési jogkivonatot kér egy olyan erőforráshoz, amely 1,0 hozzáférési tokent fogad el, akkor az Azure AD a kért hatókörből elemezi a kívánt célközönséget, ha az utolsó perjel előtt mindent megtesz, és erőforrás-azonosítóként használja azt.
> Tehát ha például Azure SQL Database (**https:\//Database.Windows.net**), az erőforrás egy olyan célközönséget vár, amely perjeltel végződik (Azure SQL Database, `https://database.windows.net/`), `https://database.windows.net//.default`hatókörét kell kérnie. (Jegyezze fel a dupla perjelet.) Lásd még: MSAL.NET probléma [#747: az erőforrás URL-címének záró perjele ki van hagyva, ami SQL-hitelesítési hibát okozott](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Az alkalmazáshoz tartozó jogkivonatok beszerzéséhez a platformtól függően `AcquireTokenForClient` vagy annak megfelelőjét kell használnia.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ez a kód a [MSAL Java dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)lett kinyerve.

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

    /* Call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocol (Protokoll)

Ha még nem rendelkezik a választott nyelvhez tartozó könyvtárral, érdemes lehet közvetlenül a protokollt használni:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Első eset: hozzáférés a jogkivonat-kérelemhez közös titok használatával

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Második eset: hozzáférés a jogkivonat-kérelemhez tanúsítvány használatával

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
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

A MSAL.NET `AcquireTokenForClient` az alkalmazás-jogkivonat gyorsítótárát használja. (Az összes többi AcquireToken*XX* -módszer a felhasználói jogkivonat gyorsítótárát használja.) A `AcquireTokenForClient`meghívása előtt ne hívjon `AcquireTokenSilent`, mert a `AcquireTokenSilent` a *felhasználói* jogkivonat gyorsítótárát használja. `AcquireTokenForClient` ellenőrzi az *alkalmazás* -jogkivonat gyorsítótárát, és frissíti azt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="did-you-use-the-resourcedefault-scope"></a>Használta az erőforrás/. alapértelmezett hatókört?

Ha hibaüzenet jelenik meg arról, hogy érvénytelen hatókört használt, valószínűleg nem használta a `resource/.default` hatókört.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Elfelejtette, hogy rendszergazdai engedélyt adjon? Daemon-alkalmazások szükségesek!

Ha az API meghívásakor nem **rendelkezik megfelelő jogosultsággal a művelet elvégzéséhez** , a bérlői rendszergazdának engedélyeket kell adnia az alkalmazás számára. Lásd a fenti ügyfélalkalmazás regisztrálásának 6. lépését.
Általában a következőhöz hasonló hibaüzenet jelenik meg:

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
