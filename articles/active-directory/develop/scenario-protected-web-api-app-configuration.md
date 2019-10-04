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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536888"
---
# <a name="protected-web-api-code-configuration"></a>Védett webes API-hoz: Kódkonfiguráció

Adja meg a védett webes API kódját, mi határozza meg API-k, védettként, tulajdonosi jogkivonattal konfigurálása és ellenőrzése a jogkivonat kell.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Mi határozza meg ASP.NET/ASP.NET Core API-k védettként?

Például a web apps, a ASP.NET/ASP.NET Core webes API-k "védett" mivel a tartományvezérlő lépések van fűzve előtagként a `[Authorize]` attribútum. Így a tartományvezérlő műveletek nem hívható meg csak akkor, ha az identitás, amelynek jogosítványa van meghívja az API-t.

Vegye figyelembe a következő kérdéseket:

- Hogyan, hogy a webes API-t, amely meghívja ezt az alkalmazás identitását? (Csak az alkalmazás egy webes API-t hívhatja.)
- Ha az alkalmazás neve a webes API egy felhasználó nevében, mi az a felhasználó identitása?

## <a name="bearer-token"></a>Tulajdonosi jogkivonat

Az alkalmazás identitását, és a felhasználó információt (kivéve, ha a webalkalmazás-démon alkalmazásból szolgáltatások közötti hívások fogad el), a tulajdonosi jogkivonatot, amely a fejlécében van beállítva, ha az alkalmazás neve tárolt.

Íme egy C# kód példa bemutatja egy ügyfél az API meghívása után azt tokenbeolvasás a Microsoft-hitelesítési tár .NET (MSAL.NET):

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
> A tulajdonosi jogkivonatot a Microsoft identity platform végpont ügyfélalkalmazás által kért *a webes API*. A webes API-t kell ellenőrizni a jogkivonatot, és a benne található jogcímeket megtekintése egyetlen alkalmazás. Ügyfélalkalmazások soha ne próbálja meg vizsgálja meg a jogcímeket a jogkivonatokban. (A webes API-t igényel, a jövőben, hogy a jogkivonat titkosítva legyen-e. Ez a követelmény megakadályozná, hogy az ügyfélalkalmazások, amelyek megtekintheti a hozzáférési jogkivonatok hozzáférés.)

## <a name="jwtbearer-configuration"></a>JwtBearer konfiguráció

Ez a szakasz ismerteti a tulajdonosi jogkivonattal konfigurálása.

### <a name="config-file"></a>A konfigurációs fájl

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

Ha egy alkalmazás egy vezérlő műveletet, amely tartalmazza a neve egy `[Authorize]` attribútum, ASP.NET/ASP.NET Core a hívó kérelem az engedélyezési fejléc tulajdonosi jogkivonat megvizsgál, és kinyeri a hozzáférési jogkivonatot. A jogkivonat ezután a JwtBearer közbenső szoftver, amely meghív IdentityModel bővítmények a Microsoft .NET-keretrendszerhez készült lesznek továbbítva.

Az ASP.NET Core az a közbenső szoftver inicializálása a Startup.cs fájlban:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

A közbenső szoftver ezeket az utasításokat hozzáadta a webes API-hoz:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Jelenleg az ASP.NET Core-sablonok az Azure Active Directory (Azure AD) webes API-kat jelentkezzen létre a szervezet vagy bármely szervezet személyes fiókkal nem rendelkező felhasználóit. Azonban egyszerűen módosíthatja az azokat a Microsoft identity platform végpont használatára Ez a kód hozzáadja a Startup.cs fájlban:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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

## <a name="token-validation"></a>Érvényesítési jogkivonat

A JwtBearer közbenső szoftverek, például a web apps szolgáltatásban, az OpenID Connect közbenső szoftver által van irányítva `TokenValidationParameters` a jogkivonat érvényesítéséhez. A jogkivonatot a rendszer visszafejti a (igény szerint), a jogcímek ki kell olvasni, és ellenőrzi az aláírást. A közbenső szoftver majd érvényesíti a jogkivonatot az adatok ellenőrzésével:

- Célja, hogy a webes API-t (célközönség).
- Ki, hogy engedélyezte a webes API-k (al) meghívásához.
- A bocsátotta megbízható biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) (kibocsátó).
- Élettartama (lejárati) hatótávolságon belül van.
- Ez nem lett illetéktelenül (aláírás).

Emellett speciális ellenőrzések lehet. Például akkor lehet aláírási kulcsokat (Ha a beágyazott jogkivonat) megbízható, hogy, hogy a jogkivonat nincs folyamatban visszajátszani ellenőrzése. Végül néhány protokoll szükséges adott ellenőrzések.

### <a name="validators"></a>Érvényesítők

Az ellenőrzési lépések érvényesítők, amelyek mind rögzíti a rendszer a [IdentityModel bővítmények a Microsoft .NET-keretrendszerhez készült](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) nyílt forráskódú kódtár, egy forrásfájl: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Ez a táblázat ismerteti a érvényesítők:

| Érvényesítési | Leírás |
|---------|---------|
| `ValidateAudience` | Biztosítja a jogkivonatot, amely érvényesíti a jogkivonatot (a számomra). |
| `ValidateIssuer` | Biztosítja a jogkivonatot egy megbízható STS bocsátotta (a valaki megbízható). |
| `ValidateIssuerSigningKey` | Az alkalmazás a token bizalmi kapcsolatok ellenőrzése a jogkivonat aláírásához használt kulcs biztosítja. (Különleges eset, ahol a jogkivonatot a kulcs van beágyazva. Általában nem szükséges.) |
| `ValidateLifetime` | Biztosítja a token érvényességét továbbra is (vagy már). Az érvényesítő ellenőrzi, hogy a jogkivonat élettartama (`notbefore` és `expires` jogcímek) hatótávolságon belül van. |
| `ValidateSignature` | Biztosítja a jogkivonat még nem módosították illetéktelenül. |
| `ValidateTokenReplay` | Biztosítja a jogkivonat nem játssza vissza. (Az egyes onetime használata protokollokat különleges eset.) |

A érvényesítők rendszer a tulajdonságait a `TokenValidationParameters` osztályhoz, inicializálja a ASP.NET/ASP.NET konfigurációján magukat. A legtöbb esetben nem kell módosítani a paraméterek. Nincs egyetlen kivétel, alkalmazások, amelyek egyetlen bérlő nem. (Azt jelenti, webalkalmazások, amely bármely szervezet vagy személyes Microsoft-fiókok a felhasználó fogadja el.) Ebben az esetben a kibocsátó érvényesíteni kell.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ellenőrizze a hatókörök és alkalmazás-szerepkörök a kódban](scenario-protected-web-api-verification-scope-app-roles.md)
