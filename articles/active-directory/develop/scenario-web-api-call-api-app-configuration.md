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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919749"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó webes API – kód konfigurálása

Miután regisztrálta a webes API-t, beállíthatja az alkalmazás kódját.

A webes API-t úgy konfiguráló kód, hogy az alárendelt webes API-kat a webes API-k elleni védelemhez használt kód felett hozza létre. További információ: [Protected web API-app Configuration](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>A OnTokenValidated előfizetett kód

A védett webes API-k kódjának konfigurációjától függően elő kell fizetnünk az API meghívásakor kapott tulajdonosi jogkivonat érvényesítésére:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Meghatalmazásos folyamat

A AddAccountToCacheFromJwt () metódusnak a következőket kell tennie:

- MSAL-alapú bizalmas ügyfélalkalmazás létrehozása.
- Hívja meg `AcquireTokenOnBehalf` az ügyfél által a webes API-hoz beszerzett tulajdonosi jogkivonatot, az ugyanahhoz a felhasználóhoz tartozó tulajdonosi jogkivonattal, de az API-t egy alsóbb rétegbeli API meghívásához.

### <a name="instantiate-a-confidential-client-application"></a>Bizalmas ügyfélalkalmazás létrehozása

Ez a folyamat csak a bizalmas ügyfél folyamatában érhető el, így a védett webes API-k az `WithClientSecret` vagy `WithCertificate` metódusokon keresztül biztosítják az ügyfél hitelesítő adatait (az ügyfél titkos vagy a tanúsítványát) a [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) .

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
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

Végül, az ügyfél titkos kulcsa vagy a tanúsítvány helyett a bizalmas ügyfélalkalmazások is igazolják identitásukat az ügyfél-kijelentések használatával.
Ez a speciális forgatókönyv részletesen szerepel az [ügyfél-kijelentésekben](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>A-ben történő hívás

A beérkező (OBO) hívás a [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) metódus meghívásával történik a `IConfidentialClientApplication` felületen.

A `UserAssertion` a webes API által a saját ügyfeleitől kapott tulajdonosi jogkivonatból épül fel. [Két konstruktor](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)létezik, amelyek egy JWT tulajdonosi jogkivonatot vesznek igénybe, és a felhasználó bármilyen típusú felhasználói állítást (más típusú biztonsági jogkivonatot) használ, és ezt a típust egy `assertionType`nevű további paraméter határozza meg.

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

A gyakorlatban az OBO-folyamat gyakran használatos jogkivonat beszerzéséhez egy alsóbb rétegbeli API számára, és tárolja azt a MSAL.NET felhasználói jogkivonat-gyorsítótárában, hogy a webes API más részei később is meghívhatják a ``AcquireTokenOnSilent`` [felülbírálásait](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) az alsóbb rétegbeli API-k meghívásához. Ez a hívás hatással van a tokenek frissítésére, ha szükséges.

```CSharp
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

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

Az [NodeJS-ben és a Azure Functionsban](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)is láthat egy példát a flow megvalósításának nevében.

## <a name="protocol"></a>Protocol (Protokoll)

A (z) szolgáltatással kapcsolatos további információkért lásd [a Microsoft Identity platform és a OAuth 2,0-alapú folyamatát](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Token beszerzése az alkalmazáshoz](scenario-web-api-call-api-acquire-token.md)
