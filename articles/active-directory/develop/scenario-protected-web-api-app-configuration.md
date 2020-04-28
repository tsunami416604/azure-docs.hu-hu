---
title: Védett webes API-alkalmazások konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t, és hogyan konfigurálhatja az alkalmazás kódját.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882471"
---
# <a name="protected-web-api-code-configuration"></a>Védett webes API: kód konfigurálása

A védett webes API-hoz tartozó kód konfigurálásához meg kell ismernie a következőket:

- Mi határozza meg a védett API-kat.
- Tulajdonosi jogkivonat konfigurálása.
- A jogkivonat érvényesítése.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Mi határozza meg a ASP.NET és a ASP.NET Core API-kat védettként?

A webalkalmazásokhoz hasonlóan a ASP.NET és a ASP.NET Core webes API-k is védettek, mert a vezérlő műveletei az **[engedélyezés]** attribútummal vannak előjavítva. A vezérlő műveletei csak akkor hívhatók, ha az API-t egy hitelesítő identitással hívja meg.

A következő kérdéseket kell figyelembe venni:

- Csak egy alkalmazás hívhat meg webes API-t. Hogyan ismeri az API a meghívást az alkalmazás identitásával?
- Ha az alkalmazás egy felhasználó nevében hívja meg az API-t, mi a felhasználó identitása?

## <a name="bearer-token"></a>Tulajdonosi jogkivonat

A fejlécben beállított tulajdonosi jogkivonat, amely az alkalmazás neve tartalmazza az alkalmazás identitásával kapcsolatos információkat. Emellett a felhasználóval kapcsolatos információkat is tartalmaz, kivéve, ha a webalkalmazás nem fogadja el a szolgáltatás és a szolgáltatás közötti hívásokat egy Daemon-alkalmazásból.

Az alábbiakban egy C#-kódrészlet látható, amely az API-t meghívó ügyfelet jeleníti meg, miután beszerezte a tokent a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával:

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Az ügyfélalkalmazás a tulajdonosi jogkivonatot a *webes API*-hoz készült Microsoft Identity platform-végpontra kéri. A webes API az egyetlen alkalmazás, amelynek ellenőriznie kell a jogkivonatot, és meg kell tekintenie a benne található jogcímeket. Az ügyfélalkalmazások soha nem próbálják meg megvizsgálni a jogkivonatokban lévő jogcímeket.
>
> A jövőben a webes API-nak szüksége lehet a jogkivonat titkosítására. Ez a követelmény megakadályozza a hozzáférési jogkivonatokat megtekintő ügyfélalkalmazások hozzáférését.

## <a name="jwtbearer-configuration"></a>JwtBearer-konfiguráció

Ez a szakasz a tulajdonosi jogkivonatok konfigurálását ismerteti.

### <a name="config-file"></a>Konfigurációs fájl

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Kód inicializálása

Ha egy alkalmazás hívása olyan vezérlőre történik, amely egy **[engedélyezés]** attribútummal rendelkezik, a ASP.net és a ASP.net Core Kinyeri a hozzáférési jogkivonatot az engedélyezési fejléc tulajdonosi jogkivonatával. Ezután a hozzáférési tokent továbbítjuk a JwtBearer middleware-re, amely meghívja a .NET-hez készült Microsoft IdentityModel-bővítményeket.

A ASP.NET Core-ben ez a köztes kapcsolat inicializálva van a Startup.cs fájlban.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

A middleware a webes API-hoz kerül a következő utasítással:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 A ASP.NET Core-sablonok jelenleg olyan Azure Active Directory (Azure AD) webes API-kat hoznak létre, amelyek a szervezeten vagy szervezeten belül jelentkeznek be a felhasználókba. Személyes fiókkal nem jelentkezhetnek be a felhasználókba. A sablonokat a Microsoft Identity platform végpontjának használatára is módosíthatja, ha hozzáadja ezt a kódot a Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Az előző kódrészlet a [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63-](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)ben elérhető webes API növekményes oktatóanyagból lett kiASP.net Core nyerve. A **AddProtectedWebApi** metódus, amely a kódrészletnél többet mutat, hívása a Startup.cs.

## <a name="token-validation"></a>Jogkivonat ellenőrzése

Az előző kódrészletben a JwtBearer middleware, például az OpenID Connect middleware a web Appsben, a (z `TokenValidationParameters`) értéke alapján érvényesíti a jogkivonatot. A tokent szükség szerint visszafejti a rendszer, a jogcímeket kinyeri, és ellenőrzi az aláírást. A middleware ezt követően ellenőrzi a tokent, hogy ellenőrzi az alábbi adatforrást:

- Célközönség: a jogkivonat a webes API-t célozza meg.
- Sub: olyan alkalmazás számára lett kiadva, amely számára engedélyezett a webes API meghívása.
- Kiállító: egy megbízható biztonsági jogkivonat szolgáltatás (STS) adta ki.
- Lejárat: az élettartama a tartományon belül van.
- Aláírás: nem lett illetéktelenül módosítva.

Speciális érvényesítés is lehetséges. Ellenőrizheti például, hogy az aláíró kulcsok egy tokenbe ágyazva vannak-e, megbízhatók-e, és hogy a jogkivonat nincs-e visszajátszva. Végezetül bizonyos protokollok speciális érvényesítést igényelnek.

### <a name="validators"></a>Érvényesítőket

Az érvényesítési lépések a [Microsoft IdentityModel Extensions for .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) nyílt forráskódú kódtár által biztosított validatorokban vannak rögzítve. A validatorok meghatározása a könyvtár forrásfájl [Microsoft. IdentityModel. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)fájljában történik.

Ez a táblázat a validatorokat ismerteti:

| Validator | Leírás |
|---------|---------|
| **ValidateAudience** | Gondoskodik arról, hogy a jogkivonat az alkalmazáshoz legyen hitelesítve, amely érvényesíti a jogkivonatot. |
| **ValidateIssuer** | Gondoskodik arról, hogy a tokent egy megbízható STS bocsátotta ki, ami azt jelenti, hogy a jogkivonatot megbízhatónak minősíti. |
| **ValidateIssuerSigningKey** | Gondoskodik arról, hogy a jogkivonatot érvényesítő alkalmazás megbízza a jogkivonat aláírásához használt kulcsot. Van egy speciális eset, amikor a kulcs be van ágyazva a tokenbe. Ez az eset azonban általában nem fordul elő. |
| **ValidateLifetime** | Gondoskodik arról, hogy a jogkivonat még mindig vagy már érvényes legyen. Az érvényesítő ellenőrzi, hogy a jogkivonat élettartama a **notBefore** által megadott tartományba esik-e, és hogy a jogcímek **lejárnak** -e. |
| **ValidateSignature** | Gondoskodik arról, hogy a jogkivonat ne legyen illetéktelenül módosítva. |
| **ValidateTokenReplay** | Gondoskodik arról, hogy a jogkivonat ne legyen újrajátszva. Van egy speciális eset néhány már használatban lévő protokollhoz. |

A validatorok a **TokenValidationParameters** osztály tulajdonságaival vannak társítva. A tulajdonságok a ASP.NET és a ASP.NET Core konfigurációból lesznek inicializálva.

A legtöbb esetben nem kell módosítania a paramétereket. Azok az alkalmazások, amelyek nem önálló bérlők, kivételek. Ezek a webalkalmazások elfogadják a felhasználókat bármely szervezettől vagy személyes Microsoft-fiókból. Ebben az esetben a kiállítókat érvényesíteni kell.

## <a name="token-validation-in-azure-functions"></a>Jogkivonat-érvényesítés Azure Functions

A bejövő hozzáférési jogkivonatokat is ellenőrizheti Azure Functionsban. Ilyen érvényesítési példákat a [Microsoft .net](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), a [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)és a [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)tartalmaz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hatókörök és alkalmazás-szerepkörök ellenőrzése a kódban](scenario-protected-web-api-verification-scope-app-roles.md)
