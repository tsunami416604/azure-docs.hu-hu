---
title: Védett webes API – alkalmazás kódjának konfigurálása | Azure
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
ms.openlocfilehash: 9fdc30df1f932a35702b01d7146017c4ca82c91a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562318"
---
# <a name="protected-web-api-code-configuration"></a>Védett webes API: Kódkonfiguráció

A védett webes API-hoz tartozó kód konfigurálásához meg kell ismernie, hogy mi határozza meg a védett API-kat, hogyan konfigurálhatja a tulajdonosi jogkivonatot, és hogyan érvényesítheti a jogkivonatot.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Mi határozza meg a ASP.NET/ASP.NET Core API-kat védettként?

A webalkalmazásokhoz hasonlóan a ASP.net/ASP.net Core webes API-k "védettek", mert a vezérlő műveletei az `[Authorize]` attribútummal előtaggal vannak ellátva. Így a vezérlő műveletek csak akkor hívhatók meg, ha az API-t egy engedélyekkel rendelkező identitás hívja meg.

Vegye figyelembe a következő kérdéseket:

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

Ha egy alkalmazás hívása egy `[Authorize]` attribútumot tartalmazó vezérlő műveletre történik, a ASP.net/ASP.net Core a hívó kérelem engedélyezési fejlécében lévő tulajdonosi jogkivonatot tekinti meg, és Kinyeri a hozzáférési jogkivonatot. A rendszer ezután továbbítja a tokent a JwtBearer middleware-hez, amely meghívja a .NET-hez készült Microsoft IdentityModel-bővítményeket.

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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Jogkivonat ellenőrzése

A JwtBearer middleware, mint például az OpenID Connect middleware a web Appsben, a `TokenValidationParameters` rendszer a token érvényesítésére irányítja. A jogkivonat visszafejtése (szükség szerint), a jogcímek kibontása és az aláírás ellenőrzése megtörtént. A middleware ezt követően ellenőrzi a tokent, hogy ellenőrzi az alábbi adatforrást:

- A webes API-ra (célközönség) irányul.
- Olyan alkalmazás számára lett kiadva, amely számára engedélyezett a webes API (Sub) hívása.
- A tanúsítványt egy megbízható biztonsági jogkivonat szolgáltatás (STS) adta ki.
- Az élettartama a hatótávolságon (lejárat) belül van.
- Nem módosították a következővel: (aláírás).

Speciális érvényesítés is lehetséges. Ellenőrizheti például, hogy az aláíró kulcsok (a tokenbe ágyazva) megbízhatóak-e, és hogy a jogkivonat nincs-e visszajátszva. Végezetül bizonyos protokollok speciális érvényesítést igényelnek.

### <a name="validators"></a>Érvényesítőket

Az érvényesítési lépések a [Microsoft IdentityModel Extensions for .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) nyílt forráskódú függvénytárában, egyetlen forrásfájlban vannak rögzítve. [Microsoft. IdentityModel. tokenek/érvényesítő. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

A következő táblázat ismerteti a validatorokat:

| Validator | Leírás |
|---------|---------|
| `ValidateAudience` | Gondoskodik arról, hogy a jogkivonat a tokent érvényesítő alkalmazáshoz legyen (nekem). |
| `ValidateIssuer` | Gondoskodik arról, hogy a tokent egy megbízható STS állította ki (valakitől, akiben megbízik). |
| `ValidateIssuerSigningKey` | Gondoskodik arról, hogy a jogkivonatot érvényesítő alkalmazás megbízza a jogkivonat aláírásához használt kulcsot. (Speciális eset, ha a kulcs be van ágyazva a tokenbe. Általában nem szükséges.) |
| `ValidateLifetime` | Gondoskodik arról, hogy a jogkivonat még mindig (vagy már) érvényes legyen. Az érvényesítő ellenőrzi, hogy a jogkivonat élettartama (`notbefore` és `expires` jogcímei) a tartományon belül van-e. |
| `ValidateSignature` | Gondoskodik arról, hogy a jogkivonat ne legyen illetéktelenül módosítva. |
| `ValidateTokenReplay` | Gondoskodik arról, hogy a jogkivonat ne legyen újrajátszva. (Speciális eset néhány, egyszeri használatú protokollhoz.) |

A validatorok az `TokenValidationParameters` osztály tulajdonságaihoz vannak társítva, amelyek a ASP.net/ASP.net alapkonfigurációból lettek inicializálva. A legtöbb esetben nem kell módosítania a paramétereket. Egyetlen Bérlővel rendelkező alkalmazások esetében egyetlen kivétel van. (Ez a webalkalmazások, amelyek bármely szervezetből vagy személyes Microsoft-fiókokból fogadnak felhasználókat.) Ebben az esetben a kiállítót érvényesíteni kell.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hatókörök és alkalmazás-szerepkörök ellenőrzése a kódban](scenario-protected-web-api-verification-scope-app-roles.md)
