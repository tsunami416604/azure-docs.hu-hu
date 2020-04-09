---
title: Geték beszerzése webes API (démonalkalmazás) hívásához - Microsoft identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívja (tokenek beolvasása) démonalkalmazást
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a81f3ffb7ec190943c50127b129523badf0ef0a7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882981"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>A webes API-kat meghívjaDó Démon alkalmazás – token beszerzése

Miután egy bizalmas ügyfélalkalmazást készített, beszerezhet egy jogkivonatot `AcquireTokenForClient`az alkalmazáshoz a hatókör hívásával, a hatókör átadásával, és szükség esetén kényszerítheti a jogkivonat frissítését.

## <a name="scopes-to-request"></a>A kért hatókörök

Az ügyfél hitelesítő adatfolyamát kérő hatókör az erőforrás `/.default`neve, amelyet a követ. Ez a jelölés azt jelzi az Azure Active Directorynak (Azure AD), hogy az alkalmazásregisztráció során statikusan deklarált *alkalmazásszintű engedélyeket* használja. Emellett ezeket az API-engedélyeket egy bérlői rendszergazdának kell megadnia.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

Az MSAL Pythonban a konfigurációs fájl a következő kódrészletnek tűnik:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD (1.0-s) erőforrások

Az ügyfél hitelesítő adataihoz használt hatókörnek mindig `/.default`a rendszernek kell lennie az erőforrás-azonosítónak, amelyet a követ.

> [!IMPORTANT]
> Amikor az MSAL hozzáférési jogkivonatot kér egy 1.0-s verziójú hozzáférési jogkivonatot fogadó erőforráshoz, az Azure AD úgy elemzi a kívánt közönséget a kért hatókörből, hogy mindent az utolsó perjel előtt vesz igénybe, és erőforrás-azonosítóként használja.
> Tehát, mint az Azure SQL Database (**https:\//database.windows.net**), az erőforrás elvárja, `https://database.windows.net/`hogy a közönség, hogy `https://database.windows.net//.default`véget ér egy perjel (az Azure SQL Database, ), akkor meg kell kérni a hatókörét. (Megjegyzés: a kettős perjel.) Lásd még: [MSAL.NET #747: Az erőforrás url-jének záró perjelje kimarad, ami sql hitelesítési hibát okozott.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Az alkalmazás jogkivonatának beszerzéséhez a `AcquireTokenForClient` platformtól függően használni fogja vagy annak megfelelőjét.

# <a name="net"></a>[.NET](#tab/dotnet)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

Ez a kód az [MSAL Java fejlesztői mintákból származik.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocol (Protokoll)

Ha még nem rendelkezik könyvtárral a választott nyelvhez, célszerű lehet közvetlenül használni a protokollt:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Első eset: A jogkivonat-kérelem elérése egy közös titkos kulcs használatával

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Második eset: A jogkivonat-kérelem elérése tanúsítvány használatával

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

További információt a protokoll dokumentációjában talál: [A Microsoft identity platform és az OAuth 2.0 ügyfélhitelesítő adatok áramlása](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Alkalmazástoken-gyorsítótár

A MSAL.NET `AcquireTokenForClient` az alkalmazásjogkivonat-gyorsítótárat használja. (Az összes többi AcquireToken*XX* metódus a felhasználói jogkivonat-gyorsítótárat használja.) Ne hívjon `AcquireTokenSilent` hívás `AcquireTokenForClient`előtt, `AcquireTokenSilent` mert a *felhasználói* jogkivonat-gyorsítótárat használja. `AcquireTokenForClient`ellenőrzi magát az *alkalmazástoken* gyorsítótárát, és frissíti azt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="did-you-use-the-resourcedefault-scope"></a>Használta az erőforrás/.default hatókört?

Ha hibaüzenet jelenik meg arról, hogy érvénytelen hatókört használt, `resource/.default` valószínűleg nem használta a hatókört.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Elfelejtette megadni az admin beleegyezését? Daemon apps szükség ez!

Ha az API-híváskor elégtelen jogosultságokat kap **a művelethiba végrehajtásához,** a bérlői rendszergazdának engedélyeket kell adnia az alkalmazásnak. Lásd a fenti Ügyfélalkalmazás regisztrálása című 6.
Általában a következő hibának tűnő hiba jelenik meg:

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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Démonalkalmazás – webes API hívása](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Démonalkalmazás – webes API hívása](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Démonalkalmazás – webes API hívása](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
