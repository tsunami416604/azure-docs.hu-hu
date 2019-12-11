---
title: Védett webes API-alkalmazások konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t, és hogyan konfigurálhatja az alkalmazás kódját.
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
ms.openlocfilehash: 7f78fa35096b7e17d3736190bfa49619c2c81520
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965398"
---
# <a name="protected-web-api-code-configuration"></a>Védett webes API: kód konfigurálása

A védett webes API-hoz tartozó kód konfigurálásához meg kell ismernie, hogy mi határozza meg a védett API-kat, hogyan konfigurálhatja a tulajdonosi jogkivonatot, és hogyan érvényesítheti a jogkivonatot.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Mi határozza meg a ASP.NET/ASP.NET Core API-kat védettként?

A webalkalmazásokhoz hasonlóan a ASP.NET/ASP.NET Core webes API-k "védettek", mert a vezérlő műveletei a `[Authorize]` attribútummal vannak előjavítva. Így a vezérlő műveletek csak akkor hívhatók meg, ha az API-t egy engedélyekkel rendelkező identitás hívja meg.

A következő kérdéseket kell figyelembe venni:

- Hogyan ismeri a webes API a meghívást az alkalmazás identitásával? (Csak az alkalmazás hívhat meg webes API-t.)
- Ha az alkalmazás a felhasználó nevében a webes API-t hívja meg, mi a felhasználó identitása?

## <a name="bearer-token"></a>Tulajdonosi jogkivonat

Az alkalmazás identitásával és a felhasználóval kapcsolatos információk (kivéve, ha a webalkalmazás nem fogadja el a szolgáltatások közötti hívásokat egy Daemon-alkalmazásból), az alkalmazás hívásakor a fejlécben beállított tulajdonosi jogkivonatban található.

Az alábbi C# kódrészlet egy olyan ügyfelet mutat be, amely az API-t hívja meg, miután beszerezte a tokent a .net-hez készült Microsoft Authentication Library (MSAL.net) használatával:

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> A tulajdonosi jogkivonatot egy ügyfélalkalmazás kérelmezte a *webes API*-hoz készült Microsoft Identity platform végpontja számára. A webes API az egyetlen alkalmazás, amelynek ellenőriznie kell a jogkivonatot, és meg kell tekintenie a benne található jogcímeket. Az ügyfélalkalmazások soha nem próbálják meg megvizsgálni a jogkivonatokban lévő jogcímeket. (A webes API-nak a jövőben a jogkivonat titkosítása szükséges. Ez a követelmény megakadályozza a hozzáférési jogkivonatokat megtekintő ügyfélalkalmazások hozzáférését.)

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

Ha egy alkalmazás egy `[Authorize]` attribútumot tartalmazó vezérlő műveletre van meghívva, a ASP.NET/ASP.NET Core a hívó kérelem engedélyezési fejlécében a tulajdonosi jogkivonatot tekinti meg, és kibontja a hozzáférési jogkivonatot. A rendszer ezután továbbítja a tokent a JwtBearer middleware-hez, amely meghívja a .NET-hez készült Microsoft IdentityModel-bővítményeket.

A ASP.NET Core-ben ez a middleware a Startup.cs fájlban van inicializálva:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

A middleware a webes API-hoz kerül a következő utasítással:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Jelenleg a ASP.NET Core sablonok olyan Azure Active Directory (Azure AD) webes API-kat hoznak létre, amelyek a szervezeten belül vagy bármely szervezeten belül, személyes fiókkal nem rendelkező felhasználók számára jelentkeznek be. A Microsoft Identity platform végpontját azonban egyszerűen módosíthatja úgy, hogy hozzáadja ezt a kódot a Startup.cs-fájlhoz:

```CSharp
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

Ez ASP.NET Core a kódrészlet a [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)webhelyről származó webes API növekményes oktatóanyagból lett kinyerve. A `AddProtectedWebApi` metódust, amely sokkal több, a Startup.cs van meghívva

## <a name="token-validation"></a>Jogkivonat ellenőrzése

A JwtBearer middleware-t, például az OpenID Connect middleware-t a web Appsben `TokenValidationParameters` irányítja a jogkivonat érvényesítéséhez. A jogkivonat visszafejtése (szükség szerint), a jogcímek kibontása és az aláírás ellenőrzése megtörtént. A middleware ezt követően ellenőrzi a tokent, hogy ellenőrzi az alábbi adatforrást:

- A webes API-ra (célközönség) irányul.
- Olyan alkalmazás számára lett kiadva, amely számára engedélyezett a webes API (Sub) hívása.
- A tanúsítványt egy megbízható biztonsági jogkivonat szolgáltatás (STS) adta ki.
- Az élettartama a hatótávolságon (lejárat) belül van.
- Nem módosították a következővel: (aláírás).

Speciális érvényesítés is lehetséges. Ellenőrizheti például, hogy az aláíró kulcsok (a tokenbe ágyazva) megbízhatóak-e, és hogy a jogkivonat nincs-e visszajátszva. Végezetül bizonyos protokollok speciális érvényesítést igényelnek.

### <a name="validators"></a>Érvényesítőket

Az érvényesítési lépéseket a rendszer az érvényesítő szolgáltatásban rögzíti, amelyek a .NET nyílt forráskódú kódtár [Microsoft IdentityModel bővítményeiben](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) találhatók, egyetlen forrásfájlban: [Microsoft. IdentityModel. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

A következő táblázat ismerteti a validatorokat:

| Validator | Leírás |
|---------|---------|
| `ValidateAudience` | Gondoskodik arról, hogy a jogkivonat a tokent érvényesítő alkalmazáshoz legyen (nekem). |
| `ValidateIssuer` | Gondoskodik arról, hogy a tokent egy megbízható STS állította ki (valakitől, akiben megbízik). |
| `ValidateIssuerSigningKey` | Gondoskodik arról, hogy a jogkivonatot érvényesítő alkalmazás megbízza a jogkivonat aláírásához használt kulcsot. (Speciális eset, ha a kulcs be van ágyazva a tokenbe. Általában nem szükséges.) |
| `ValidateLifetime` | Gondoskodik arról, hogy a jogkivonat még mindig (vagy már) érvényes legyen. Az érvényesítő ellenőrzi, hogy a jogkivonat élettartama (`notbefore` és `expires` jogcímek) a tartományon belül van-e. |
| `ValidateSignature` | Gondoskodik arról, hogy a jogkivonat ne legyen illetéktelenül módosítva. |
| `ValidateTokenReplay` | Gondoskodik arról, hogy a jogkivonat ne legyen újrajátszva. (Speciális eset néhány, egyszeri használatú protokollhoz.) |

A validatorok a `TokenValidationParameters` osztály tulajdonságaihoz vannak társítva, amelyek a ASP.NET/ASP.NET alapkonfigurációból lettek inicializálva. A legtöbb esetben nem kell módosítania a paramétereket. Egyetlen Bérlővel rendelkező alkalmazások esetében egyetlen kivétel van. (Ez a webalkalmazások, amelyek bármely szervezetből vagy személyes Microsoft-fiókokból fogadnak felhasználókat.) Ebben az esetben a kiállítót érvényesíteni kell.

## <a name="token-validation-in-azure-functions"></a>Jogkivonat-érvényesítés Azure Functions

Az Azure functions szolgáltatásban is ellenőrizhető a bejövő hozzáférési tokenek ellenőrzése. Az Azure functions-ben a [DotNet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), a [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)és a [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)szolgáltatásban találhat példákat a jogkivonatok érvényesítésére.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hatókörök és alkalmazás-szerepkörök ellenőrzése a kódban](scenario-protected-web-api-verification-scope-app-roles.md)
