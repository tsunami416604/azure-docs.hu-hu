---
title: Védett webes API - alkalmazás helykódot |} Az Azure
description: Ismerje meg, hogyan hozhat létre egy védett webes API-t, és konfigurálja az alkalmazás kódjában.
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
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074965"
---
# <a name="protected-web-api---code-configuration"></a>Védett webes API - kód-konfiguráció

Sikerült beállítani a védett webes API kódját, a védett API-k Miből, szükséges tulajdonosi jogkivonat konfigurálása és ellenőrzése a jogkivonat kell.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>Miből ASP.NET/ASP.NET Core API-k védelme?

Például a web Apps, a a ASP.NET/ASP.NET webes API-k "védett" alapvető, mivel a tartományvezérlő lépések van fűzve előtagként a `[Authorize]` attribútum. Emiatt a tartományvezérlő műveletek lze volat pouze, ha az identitás, amelynek jogosítványa van meghívja az API-t.

Vegye figyelembe a következő kérdéseket:

- Hogyan, hogy a webes API-t az alkalmazáshoz, amely meghívja az (csak alkalmazás meghívhatja egy webes API-k)?
- Ha az alkalmazás neve a webes API egy felhasználó nevében, mi az a felhasználó identitása?

## <a name="bearer-token"></a>Tulajdonosi jogkivonat

Az alkalmazás identitását, és a felhasználó információt (kivéve, ha a webalkalmazás fogadja el a szolgáltatások közötti hívások démon-alkalmazás), a tulajdonosi jogkivonatot, amely a fejlécében van beállítva, ha az alkalmazás hívása tárolt.

Íme egy C# kód példa bemutatja egy ügyfél az API meghívása után a jogkivonatot az MSAL.NET beszerzése.

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
> A tulajdonosi jogkivonatot a Microsoft identity platform végpont ügyfélalkalmazás által kért **a webes API**. A webes API-t kell ellenőrizni a jogkivonatot, és tekintse meg a jogcímeket tartalmaz egyetlen alkalmazás. Az ügyfélalkalmazások számára soha nem tekintse meg a tokenek jogcímeit (a webes API sikerült döntse el, a jövőben ezt megköveteli, hogy a jogkivonat titkosítva legyen-e, és ez megszünteti az ügyfélalkalmazás, amely is feltörhetők nyissa meg a hozzáférési jogkivonatok).

## <a name="jwtbearer-configuration"></a>JwtBearer konfiguráció

Ez a szakasz ismerteti, milyen konfigurálnia kell a tulajdonosi jogkivonatot.

### <a name="config-file"></a>Konfigurációs fájl

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

Ha az alkalmazás neve a tartományvezérlő műveleti üzemben egy `[Authorize]` attribútum, ASP.NET/ASP.NET core megvizsgálja a hívó kérelem az engedélyezési fejléc tulajdonosi jogkivonat, és kinyeri a hozzáférési jogkivonatot, amely ezután továbbítja a JwtBearer közbenső szoftverek, amelyek a Microsoft Identity Modellbővítményektől meghívja a .NET-hez.

Az ASP.NET Core, a közbenső szoftver az inicializálása a `Startup.cs` fájlt.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

A közbenső szoftver az alábbi utasítás hozzáadta a webes API-hoz:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Jelenleg az ASP.NET Core-sablonokat létrehozni az Azure AD 1.0-s verziójú webes API-kat. Azonban egyszerűen módosíthatja ezeket adja hozzá a következő kódot a Microsoft identity platform végpont használata a `Startup.cs` fájlt.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Érvényesítési jogkivonat

A JwtBearer közbenső szoftverek, például a web apps szolgáltatásban, az OpenID Connect közbenső szoftver által van irányítva az `TokenValidationParameters` a jogkivonat érvényesítéséhez. A jogkivonatot a rendszer visszafejti a (ha szükséges), a jogcímek ki kell olvasni, és ellenőrzi az aláírást. Ezt követően a jogkivonat érvényesítése ellenőrzésével a következő adatokat:

- Ez vonatkozik a webes API-t (célközönség)
- Ki olyan alkalmazás, amely számára engedélyezett a webes API (al) hívása
- A kiállítás módjától szerint egy megbízható irányelvrendszert biztonsági jogkivonat kiszolgáló (STS) (kibocsátó)
- A jogkivonatok élettartama (lejárati) hatótávolságon belül van
- Ez nem lett illetéktelenül (aláírás)

Emellett speciális ellenőrzések lehet. Például akkor lehet aláírási kulcsokat (Ha a beágyazott jogkivonat) megbízható, hogy, hogy a jogkivonat nincs folyamatban visszajátszani ellenőrzése. Végül néhány protokoll szükséges adott ellenőrzések.

### <a name="validators"></a>Érvényesítők

Az ellenőrzési lépések érvényesítők, amelyek mind az rögzítve lesznek a [a Microsoft Identity modell bővítmény a .NET-hez](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) nyílt forráskódú kódtár, egy forrásfájl: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

A érvényesítőket a következő táblázat ismerteti:

| Érvényesítési | Leírás |
|---------|---------|
| `ValidateAudience` | Biztosítja, hogy a jogkivonat, amely érvényesíti a jogkivonatot (a számomra). |
| `ValidateIssuer` | Biztosítja, hogy a jogkivonat egy megbízható STS által kiállított (a valaki megbízható). |
| `ValidateIssuerSigningKey` | Az alkalmazás a token bizalmi kapcsolatok ellenőrzése a jogkivonat (ahol a kulcs van ágyazva a jogkivonatot, és általában nem szükséges különleges eset) aláírásához használt kulcs biztosítja. |
| `ValidateLifetime` | Biztosítja, hogy a jogkivonat még mindig (vagy már) érvényes. Kész úgy, hogy a jogkivonat élettartama (`notbefore`, `expires` jogcímek) hatótávolságon belül van. |
| `ValidateSignature` | Biztosítja, hogy a jogkivonat nem módosítják. |
| `ValidateTokenReplay` | A jogkivonat nem visszajátszani biztosítja (az egyes onetime használata protokollokat különleges eset). |

A érvényesítők rendszer a tulajdonságait a `TokenValidationParameters` osztályhoz, inicializálja a ASP.NET/ASP.NET konfigurációján magukat. A legtöbb esetben nem kell módosítani a paraméterek. Az alkalmazásokat, amelyek nem egyetlen bérlők egy kivételt (azaz a web Apps alkalmazások, amely fogadja el a felhasználók bármely szervezet vagy személyes Microsoft-fiókok) – ebben az esetben a kibocsátó érvényesíteni kell.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-protected-web-api-production.md)
