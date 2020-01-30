---
title: Webes API-kat meghívó webes API konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-t meghívó webes API-kat (az alkalmazás kódjának konfigurációja)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834093"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webes API: kód konfigurálása

Miután regisztrálta a webes API-t, beállíthatja az alkalmazás kódját.

A webes API konfigurálásához használt kód, hogy az alsóbb rétegbeli webes API-kat a webes API-k elleni védelemhez használt kód felett hozza létre. További információ [: Protected web API: app Configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

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

Ez a folyamat csak a bizalmas ügyfél folyamatában érhető el, így a védett webes API ügyfél-hitelesítő adatokat (az ügyfél titkos kulcsát vagy tanúsítványát) biztosít a [ConfidentialClientApplicationBuilder osztálynak](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) a `WithClientSecret` vagy a `WithCertificate` metódus használatával.

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

A befelé (OBO) hívást a [AcquireTokenOnBehalf metódusnak](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) a `IConfidentialClientApplication` felületen való meghívásával teheti meg.

A `UserAssertion` osztály a webes API által a saját ügyfeleitől kapott tulajdonosi jogkivonatból épül fel. [Két konstruktor](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)létezik:
* Egy JSON Web Token (JWT) tulajdonosi jogkivonatot vesz igénybe
* Az egyik, hogy bármilyen felhasználói állítást használ, egy másik típusú biztonsági jogkivonatot, amelynek típusát a `assertionType` nevű további paraméter határozza meg.

![UserAssertion tulajdonságai és módszerei](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

A gyakorlatban az OBO flow-t gyakran használják az alsóbb rétegbeli API-k jogkivonatának beszerzésére és a MSAL.NET felhasználói jogkivonat-gyorsítótárba való tárolására. Ezt úgy teheti meg, hogy a webes API más részei később meghívják a ``AcquireTokenOnSilent`` [felülbírálásait](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) az alsóbb rétegbeli API-k meghívásához. Ez a hívás hatással van a tokenek frissítésére, ha szükséges.

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
# <a name="javatabjava"></a>[Java](#tab/java)

A beérkező (OBO) folyamat az alárendelt webes API meghívására szolgáló token beszerzésére szolgál. Ebben a folyamatban a webes API egy tulajdonosi jogkivonatot kap a felhasználó által delegált engedélyekkel az ügyfélalkalmazás számára, majd ezt a tokent egy másik hozzáférési tokenre cseréli le az alárendelt webes API meghívásához.

Az alábbi kód a rugós biztonsági keretrendszer `SecurityContextHolder` a webes API-ban a hitelesített tulajdonosi jogkivonat lekérésére használatos. Ezután a MSAL Java Library használatával szerzi be az alsóbb rétegbeli API-tokent a `acquireToken` hívással, `OnBehalfOfParameters`. A MSAL gyorsítótárazza a tokent, hogy az API-hoz való további hívások a gyorsítótárazott token beszerzéséhez `acquireTokenSilently` használhatják a következőt:.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

A beérkező (OBO) folyamat az alárendelt webes API meghívására szolgáló token beszerzésére szolgál. Ebben a folyamatban a webes API egy tulajdonosi jogkivonatot kap a felhasználó által delegált engedélyekkel az ügyfélalkalmazás számára, majd ezt a tokent egy másik hozzáférési tokenre cseréli le az alárendelt webes API meghívásához.

Egy Python webes API-nak bizonyos middleware-t kell használnia az ügyféltől kapott tulajdonosi jogkivonat ellenőrzéséhez. A webes API ezután a MSAL Python Library használatával szerezheti be az alsóbb rétegbeli API hozzáférési tokenjét a [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metódus meghívásával. A folyamatot bemutató minta a MSAL Python szolgáltatással még nem érhető el.

---

A [Node. js-ben és a Azure Functionsban](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)is láthatja az OBO flow megvalósításának példáját.

## <a name="protocol"></a>Protocol (Protokoll)

További információ az OBO protokollról: [Microsoft Identity platform és OAuth 2,0 on-Half-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: az alkalmazás jogkivonatának beszerzése](scenario-web-api-call-api-acquire-token.md)
