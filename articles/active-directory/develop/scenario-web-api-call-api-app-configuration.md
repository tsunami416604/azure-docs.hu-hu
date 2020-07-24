---
title: Webes API-kat meghívó webes API konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-t meghívó webes API-kat (az alkalmazás kódjának konfigurációja)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: eff5f68569d1878e1b802f2db4151d246bcc07c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026424"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webes API: kód konfigurálása

Miután regisztrálta a webes API-t, beállíthatja az alkalmazás kódját.

A webes API konfigurálásához használt kód, hogy az alsóbb rétegbeli webes API-kat a webes API-k elleni védelemhez használt kód felett hozza létre. További információ [: Protected web API: app Configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Ügyfél-titkok vagy Ügyféltanúsítványok

Mivel a webes API most egy alárendelt webes API-t hív meg, meg kell adnia egy ügyfél-titkos vagy ügyféltanúsítványt a fájl *appsettings.js* .

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

Az ügyfél titkos kulcsa helyett megadhat egy ügyféltanúsítványt. A következő kódrészlet a Azure Key Vaultban tárolt tanúsítvány használatát mutatja be.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

A Microsoft. Identity. Web számos módszert kínál a tanúsítványok leírására, a konfiguráció vagy a kód alapján. Részletekért lásd: [Microsoft. Identity. Web wiki – tanúsítványok használata](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) a githubon.

## <a name="startupcs"></a>Startup.cs

Ha azt szeretné, hogy a webes API meghívja az alárendelt webes API-kat, a Microsoft. Identity. Web használatával adja hozzá a `.AddMicrosoftWebApiCallsWebApi()` sort, `.AddMicrosoftWebApiAuthentication(Configuration)` majd válassza ki a jogkivonat-gyorsítótár implementációját, például `.AddInMemoryTokenCaches()` a *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
   services.AddMicrosoftWebApiAuthentication(Configuration)
           .AddMicrosoftWebApiCallsWebApi()
           .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Akárcsak a Web Apps esetében, különböző jogkivonat-gyorsítótár-implementációkat is választhat. Részletekért lásd: [Microsoft Identity web wiki-token cache szerializálás](https://aka.ms/ms-id-web/token-cache-serialization) a githubon.

Ha biztos abban, hogy a webes API-nak konkrét hatókörökre lesz szüksége, akkor a következő argumentumként adhatja át őket: `AddMicrosoftWebApiCallsWebApi` .

# <a name="java"></a>[Java](#tab/java)

A beérkező (OBO) folyamat az alárendelt webes API meghívására szolgáló token beszerzésére szolgál. Ebben a folyamatban a webes API egy tulajdonosi jogkivonatot kap a felhasználó által delegált engedélyekkel az ügyfélalkalmazás számára, majd ezt a tokent egy másik hozzáférési tokenre cseréli le az alárendelt webes API meghívásához.

Az alábbi kód a rugós biztonsági keretrendszert használja `SecurityContextHolder` a webes API-ban az ellenőrzött tulajdonosi jogkivonat beszerzéséhez. Ezután a MSAL Java Library használatával szerzi be az alsóbb rétegbeli API-tokent a `acquireToken` hívásával `OnBehalfOfParameters` . A MSAL gyorsítótárazza a jogkivonatot, így az API-hoz `acquireTokenSilently` való későbbi hívások a gyorsítótárazott token beszerzésére használhatják.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

A beérkező (OBO) folyamat az alárendelt webes API meghívására szolgáló token beszerzésére szolgál. Ebben a folyamatban a webes API egy tulajdonosi jogkivonatot kap a felhasználó által delegált engedélyekkel az ügyfélalkalmazás számára, majd ezt a tokent egy másik hozzáférési tokenre cseréli le az alárendelt webes API meghívásához.

Egy Python webes API-nak bizonyos middleware-t kell használnia az ügyféltől kapott tulajdonosi jogkivonat ellenőrzéséhez. A webes API ezt követően a metódus meghívásával lekérheti az alsóbb rétegbeli API hozzáférési tokenjét a MSAL Python Library használatával [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Az API használatára példaként tekintse [meg a Microsoft-Authentication-Library-for-Python a githubon történő tesztelési kódját](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Tekintse meg a 53-es [probléma](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) ugyanazon tárházban való megvitatását is, amely megkerüli a középső rétegbeli alkalmazások szükségességét.

---

Megtekintheti az OBO flow megvalósításának példáját is [Node.js és Azure functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

További információ az OBO protokollról: [Microsoft Identity platform és OAuth 2,0 on-Half-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: az alkalmazás jogkivonatának beszerzése](scenario-web-api-call-api-acquire-token.md)
