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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991007"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webes API: kód konfigurálása

Miután regisztrálta a webes API-t, beállíthatja az alkalmazás kódját.

A webes API konfigurálásához használt kód, hogy az alsóbb rétegbeli webes API-kat a webes API-k elleni védelemhez használt kód felett hozza létre. További információ [: Protected web API: app Configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>A OnTokenValidated előfizetett kód

A védett webes API-k kódjának konfigurációjától függően elő kell fizetnünk az API meghívásakor kapott tulajdonosi jogkivonat érvényesítésére:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Eljáró folyamat

A AddAccountToCacheFromJwt () metódusnak a következőket kell tennie:

- A Microsoft Authentication Library (MSAL) bizalmas ügyfélalkalmazás példányának létrehozása.
- Hívja meg a `AcquireTokenOnBehalf` metódust. Ez a hívás azt a tulajdonosi jogkivonatot cseréli le, amelyet az ügyfél a webes API-hoz kapott a tulajdonosi jogkivonattal, de az API-hívás egy alsóbb rétegbeli API-t tartalmaz.

### <a name="instantiate-a-confidential-client-application"></a>Bizalmas ügyfélalkalmazás létrehozása

Ez a folyamat csak a bizalmas ügyfél folyamatában érhető el, így a védett webes API a vagy a metódus használatával biztosítja az ügyfél hitelesítő adatait (az ügyfél titkos vagy a tanúsítványát) a [ConfidentialClientApplicationBuilder osztályhoz](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) `WithClientSecret` `WithCertificate` .

![IConfidentialClientApplication-metódusok listája](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Végül, ahelyett, hogy az identitást az ügyfél titkos vagy egy tanúsítványa alapján igazolja, a bizalmas ügyfélalkalmazások igazolják személyazonosságát az ügyfél-kijelentések használatával.
További információ erről a speciális forgatókönyvről: [bizalmas ügyfél-kijelentések](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>A-ben történő hívás

Az [AcquireTokenOnBehalf metódus](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) meghívásával végezze el a befelé (OBO) hívást a `IConfidentialClientApplication` felületen.

Az `UserAssertion` osztály a webes API által a saját ügyfeleitől kapott tulajdonosi jogkivonatból épül fel. [Két konstruktor](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)létezik:
* Egy JSON Web Token (JWT) tulajdonosi jogkivonatot vesz igénybe
* Az egyik, hogy bármilyen típusú felhasználói állítást használ, egy másik típusú biztonsági jogkivonatot, amelynek típusát a rendszer egy nevű további paraméterben adja meg`assertionType`

![UserAssertion tulajdonságai és módszerei](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

A gyakorlatban az OBO flow-t gyakran használják az alsóbb rétegbeli API-k jogkivonatának beszerzésére és a MSAL.NET felhasználói jogkivonat-gyorsítótárba való tárolására. Ezt úgy teheti meg, hogy a webes API más részei később is meghívhatják az [overrides](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent`` alsóbb rétegbeli API-k meghívásához szükséges felülbírálásokat. Ez a hívás hatással van a tokenek frissítésére, ha szükséges.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
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
