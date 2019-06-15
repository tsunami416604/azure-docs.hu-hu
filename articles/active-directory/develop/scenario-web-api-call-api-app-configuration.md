---
title: Webes API-t, hogy hívások alsóbb rétegbeli webes API-k (alkalmazás kód konfiguráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webes API-t, hogy a hívások webes API-k (alkalmazás kód konfigurálása)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62cf65e275d8a9b909bf60103ccbd84e91e4574
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785052"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Webes API-t, hogy a hívások webes API-k – helykódot

A webes API regisztrálása után beállíthatja az alkalmazás kódját.

A kódot a webes API konfigurálása, hogy az alsóbb rétegbeli webes API-k meghívja a webes API védelmére szolgáló kód épül. További információ: [védett webes API - alkalmazás konfigurációja](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Az előfizetett OnTokenValidated kód

Felül a kód konfiguráció bármely védett webes API-k elő kell fizetnie, az API meghívásakor fogadott tulajdonosi jogkivonat érvényesítése:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
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

A AddAccountToCacheFromJwt() módszert kell:

- Az MSAL bizalmas ügyfélalkalmazást hozható létre.
- Hívás `AcquireTokenOnBehalf` Exchange a tulajdonosi jogkivonatot, amely által az ügyfél a webes API-t, szemben a tulajdonosi jogkivonat ugyanahhoz a felhasználóhoz, de az API segítségével egy alsóbb rétegbeli API-t.

### <a name="instantiate-a-confidential-client-application"></a>Bizalmas ügyfélalkalmazást hozható létre

Ez a folyamat csak érhető el a bizalmas ügyfél folyamatban a védett webes API ügyfél-hitelesítő adatok (titkos Ügyfélkód vagy tanúsítvány) biztosít így a [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) keresztül a `WithClientSecret` vagy `WithCertificate`módszereket, illetve.

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

### <a name="how-to-call-on-behalf-of"></a>A alapú meghatalmazásos meghívása

A--meghatalmazásos (OBO) hívás meghívásával történik a [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) metódust a `IConfidentialClientApplication` felületet.

A `ClientAssertion` által a webes API-t a saját ügyfelektől fogadott tulajdonosi jogkivonat alapján készült. Nincsenek [két konstruktorral](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), amely egy JWT tulajdonosi jogkivonat vesz igénybe, a másikat, amely a felhasználó helyességi feltétel bármilyen típusú (más típusú biztonsági jogkivonat, egy kiegészítő paraméterrel nevű majd megadott típusának `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

A gyakorlatban egy token beszerzéséhez az alsóbb rétegbeli API-hoz, és tárolja az MSAL.NET felhasználói jogkivonatok gyorsítótárát, hogy a webes API-k más részein később meghívhatja a gyakran használják a OBO folyamatot a [felülbírálások](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) , ``AcquireTokenOnSilent`` az alsóbb rétegbeli API-k meghívására. Ennek hatása a használható a jogkivonatokat, ha szükséges.

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

## <a name="protocol"></a>Protocol

A--meghatalmazásos protokoll kapcsolatos további információkért lásd: [a Microsoft identity platform és az OAuth 2.0-alapú meghatalmazásos folyamat](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alkalmazás egy token beszerzése](scenario-web-api-call-api-acquire-token.md)
