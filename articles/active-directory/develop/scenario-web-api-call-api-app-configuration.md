---
title: Webes API-kat meghívjaó webes API konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat webes API-k (az alkalmazás kódkonfigurációja)
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
ms.openlocfilehash: 4129f1a89575c9a0e7cd6a0090168df659356c1b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885106"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívja: Kódkonfiguráció

Miután regisztrálta a webes API-t, konfigurálhatja az alkalmazás kódját.

A webes API-k konfigurálásához használt kód, amely lehívja az alsóbb rétegbeli webes API-kat, a webes API-k védelmére használt kódon alapul. További információ: [Protected web API: App configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>OnTokenValidated-ra előfizetett kód

A védett webes API-k kódkonfigurációján felül elő kell fizetnie a tulajdonosi jogkivonat érvényesítésére, amelyet az API megnevezésekén kap:

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

## <a name="on-behalf-of-flow"></a>Az áramlás nevében

Az AddAccountToCacheFromJwt() metódusnak:

- Microsoft Authentication Library (MSAL) bizalmas ügyfélalkalmazás példányosítson.
- Hívja `AcquireTokenOnBehalf` meg a módszert. Ez a hívás kicseréli a tulajdonosi jogkivonatot, amelyet az ügyfél a webes API-hoz szerzett meg egy tulajdonosi jogkivonattal ugyanahhoz a felhasználóhoz, de rendelkezik az API-val, amely egy alsóbb rétegbeli API-t hív meg.

### <a name="instantiate-a-confidential-client-application"></a>Bizalmas ügyfélalkalmazás példányosítása

Ez a folyamat csak a bizalmas ügyfélfolyamatban érhető el, így a védett webes API ügyfélhitelesítő adatokat (ügyféltitkos `WithCertificate` kulcsot vagy tanúsítványt) biztosít a [ConfidentialClientApplicationBuilder osztálynak](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) a vagy a `WithClientSecret` metóduson keresztül.

![Az IConfidentialClientApplication metódusok listája](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

Végül ahelyett, hogy egy ügyféltitokon vagy tanúsítványon keresztül bizonyítaná személyazonosságukat, a bizalmas ügyfélalkalmazások ügyfélállítások használatával igazolhatják személyazonosságukat.
A speciális forgatókönyvről további információt a Bizalmas ügyfélállítások című [témakörben talál.](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Hogyan hívd fel a nevében

A kapcsolati cím (OBO) hívását úgy kezdeményezheti, hogy `IConfidentialClientApplication` felhívja a felületen az [AcquireTokenOnBehalf metódust.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)

Az `UserAssertion` osztály a webes API által a saját ügyfeleitől kapott tulajdonosi jogkivonatból épül fel. Két [konstruktor](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)van:
* Egy, hogy vesz egy JSON Web Token (JWT) bemutatóra token
* Az egyik, hogy vesz bármilyen felhasználói állítás, egy másik fajta biztonsági jogkivonat, amelynek típusa majd meg van adva egy további paraméter neve`assertionType`

![UserAssertion tulajdonságok és metódusok](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

A gyakorlatban az OBO-folyamat gyakran egy új felhasználó egy alsóbb rétegbeli API-t, és tárolja azt a MSAL.NET felhasználói jogkivonat-gyorsítótárban. Ezt úgy teheti meg, hogy a webes API más ``AcquireTokenOnSilent`` részei később megszólíthassák az alsóbb rétegbeli API-k hívásához szükséges [felülbírálásokat.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) Ez a hívás szükség esetén a jogkivonatok frissítését eredményezi.

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

Az On-behalf-of (OBO) folyamat egy jogkivonat beszerzésére szolgál az alsóbb rétegbeli webes API-hoz. Ebben a folyamatban a webes API kap egy tulajdonosi jogkivonatot a felhasználó által delegált engedélyeket az ügyfélalkalmazásból, majd kicseréli ezt a jogkivonatot egy másik hozzáférési jogkivonatot az alsóbb rétegbeli webes API hívásához.

Az alábbi kód a spring `SecurityContextHolder` security keretrendszer a webes API-ban az érvényesített tulajdonosi jogkivonat bekéréséhez. Ezután az MSAL Java-kódtár segítségével szerez `acquireToken` meg `OnBehalfOfParameters`egy jogkivonatot az alsóbb rétegbeli API-hoz a hívás használatával. Az MSAL gyorsítótárazza a jogkivonatot, `acquireTokenSilently` hogy az API későbbi hívásai a gyorsítótárazott jogkivonat lekéréséhez használhatók.

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

Az On-behalf-of (OBO) folyamat egy jogkivonat beszerzésére szolgál az alsóbb rétegbeli webes API-hoz. Ebben a folyamatban a webes API kap egy tulajdonosi jogkivonatot a felhasználó által delegált engedélyeket az ügyfélalkalmazásból, majd kicseréli ezt a jogkivonatot egy másik hozzáférési jogkivonatot az alsóbb rétegbeli webes API hívásához.

A Python webes API-t kell használninéhány köztes az ügyféltől kapott tulajdonosi jogkivonat érvényesítéséhez. A webes API ezután beszerezheti a hozzáférési jogkivonatot [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) az alsóbb rétegbeli API-hoz az MSAL Python-kódtár használatával a metódus hívásával. A folyamatot az MSAL Python-nal bemutató minta még nem érhető el.

---

Az OBO-folyamat implementációjának példáját a [Node.js és](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)az Azure Functions függvényekben is megtekintheti.

## <a name="protocol"></a>Protocol (Protokoll)

Az OBO protokollról további információt a [Microsoft identity platform és az OAuth 2.0 On-Behalf-Of flow című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívja: Jogkivonat beszerzése az alkalmazáshoz](scenario-web-api-call-api-acquire-token.md)
