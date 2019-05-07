---
title: Webes API-k (az alkalmazás-jogkivonatok beszerzésének) – a Microsoft identity platform démon alkalmazás hívása
description: Ismerje meg, hogyan hozhat létre egy démon alkalmazást, hogy a hívások webes API-k (-jogkivonatok beszerzésének)
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075370"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Démon alkalmazás, amely meghívja a webes API - jogkivonat beszerzése

A bizalmas ügyfélalkalmazás jön létre, ha az alkalmazás egy token szerezheti be meghívásával ``AcquireTokenForClient``, a hatókör és kényszerített vagy nem a jogkivonat frissítését.

## <a name="scopes-to-request"></a>Hatókörök kérni

A hatókör kéréséhez tartozó ügyfél-hitelesítő adat folyamatát az erőforrás neve követ `/.default`. Ezen jelölés arra utasítja az Azure AD-ban a **szintű Alkalmazásengedélyek** statikusan deklarálva az alkalmazás regisztrációja során. Is, mivel korábban már látott ezen API engedélyt kell biztosítani a bérlői rendszergazda

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

Az MSAL. Python, a következő kódrészletet a konfigurációs fájl jelenne meg:

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

A hatókör használt ügyfél-hitelesítő adatok mindig kell resourceId + "/ .default formátummá lesznek feloldva"

### <a name="case-of-v10-resources"></a>Az 1.0-s verziójú erőforrások eset

> [!IMPORTANT]
> Az MSAL (v2.0-végpontra) egy hozzáférési jogkivonatot kér egy erőforrást egy 1.0-s verziójú jogkivonatot elfogadása az Azure AD a kért hatókörhöz a kívánt célközönség elemzi a minden, a legutóbbi / előtti és használhatná az erőforrás-azonosítója alapján.
> Ezért ha, például az Azure SQL (**https://database.windows.net**) az erőforrás vár egy adott célközönségnek, záró perjelek (az Azure SQL: `https://database.windows.net/`), szüksége lesz egy hatókörének kérése `https://database.windows.net//.default` (vegye figyelembe a dupla perjellel). Lásd még az MSAL.NET probléma [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Záró perjellel erőforrás URL-cím nincs megadva, amely az sql-hitelesítési hiba okozza.

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

#### <a name="application-token-cache"></a>Alkalmazás-jogkivonatok gyorsítótárát

Az MSAL.NET `AcquireTokenForClient` használja a **alkalmazás tokengyorsítótárral** (a többi AcquireTokenXX módszer használata a felhasználói jogkivonatok gyorsítótárának) ne hívja a `AcquireTokenSilent` hívása előtt `AcquireTokenForClient` , `AcquireTokenSilent` használja a **felhasználói** jogkivonat a gyorsítótárban. `AcquireTokenForClient` ellenőrzi a **alkalmazás** token gyorsítótár magát, és frissíti.

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

Ha nincs még egy kódtár a tetszőleges nyelven, érdemes közvetlenül a protokoll használatát:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: A közös titkos kulcsot a hozzáférési jogkivonat kérése

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat kérése tanúsítvánnyal

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

### <a name="learn-more-about-the-protocol"></a>További információ a protokoll

További információt a protokoll-dokumentációjában talál: [Az Azure Active Directory v2.0, az OAuth 2.0-ügyfél hitelesítő adatait a flow](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="did-you-use-the-resourcedefault-scope"></a>Az erőforrás/.default formátummá lesznek feloldva hatókör használatával?

Ha a hibaüzenet arról tájékoztat, hogy egy érvénytelen hatókör használta, valószínűleg nem használ a `resource/.default` hatókör.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Elfelejtett adja meg a rendszergazdai jóváhagyás? Démon alkalmazások rá!

Ha hibaüzenetet kap, az API hívásakor **nincs megfelelő jogosultsága a művelet elvégzéséhez**, a bérlői rendszergazdának kell engedélyt az alkalmazásnak. Regisztráljon a fenti ügyfélalkalmazás 6. lépése.
Általában megjelenik, és a hiba, például a következő hiba leírása:

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
> [Démon alkalmazások – egy webes API meghívása](scenario-daemon-call-api.md)