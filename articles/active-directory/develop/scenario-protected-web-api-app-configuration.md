---
title: Védett webes API-alkalmazások konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy védett webes API-t, és konfigurálhatja az alkalmazás kódját.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882471"
---
# <a name="protected-web-api-code-configuration"></a>Védett webes API: Kódkonfiguráció

A védett webes API kódjának konfigurálásához a következőket kell értenie:

- Mi határozza meg az API-kvédett.
- Bemutatóra szóló jogkivonat konfigurálása.
- A jogkivonat érvényesítése.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Mi határozza meg a ASP.NET és ASP.NET Core API-k védett?

A webalkalmazásokhoz hasonlóan a ASP.NET és a ASP.NET Core webes API-k is védettek, mivel vezérlőműveletek az **[Engedélyezés]** attribútummal vannak előrögzítve. A vezérlő műveletek csak akkor hívható meg, ha az API-t engedélyezett identitással hívják meg.

A következő kérdéseket kell figyelembe venni:

- Csak egy alkalmazás hívhatja meg a webes API-t. Honnan tudja az API az alkalmazás identitását, amely meghívja?
- Ha az alkalmazás meghívja az API-t egy felhasználó nevében, mi a felhasználó identitása?

## <a name="bearer-token"></a>Tulajdonosi token

A címben beállított tulajdonosi token, amikor az alkalmazás neve, az alkalmazás identitásával kapcsolatos információkat tartalmaz. A felhasználóadatait is tartalmazza, kivéve, ha a webalkalmazás szolgáltatás-szolgáltatás hívásokat fogad egy démonalkalmazásból.

Íme egy C# kód példa, amely azt mutatja, hogy egy ügyfél hívja az API-t, miután beszerez egy jogkivonatot a Microsoft Authentication Library for .NET (MSAL.NET):

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
> Egy ügyfélalkalmazás kéri a tulajdonosi jogkivonatot a Microsoft identity platform *végpontjára a webes API-hoz.* A webes API az egyetlen alkalmazás, amely ellenőriznie kell a jogkivonatot, és megtekintheti a benne foglalt jogcímeket. Az ügyfélalkalmazások soha ne próbálja meg a jogcímek tokenekben való vizsgálatát.
>
> A jövőben a webes API-t lehet szükség, hogy a jogkivonat titkosítva van. Ez a követelmény megakadályozná a hozzáférési jogkivonatokat megtekintő ügyfélalkalmazások hozzáférését.

## <a name="jwtbearer-configuration"></a>JwtBearer konfiguráció

Ez a szakasz a tulajdonosi jogkivonat konfigurálását ismerteti.

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

Amikor egy alkalmazást egy **[Authorize]** attribútummal rendelkező vezérlőműveletre hívnak meg, ASP.NET és ASP.NET Core kibontja a hozzáférési jogkivonatot az engedélyezési fejléc tulajdonosi jogkivonatából. A hozzáférési jogkivonatezután továbbítja a JwtBearer middleware, amely felhívja a Microsoft IdentityModel Extensions a .NET.

A Core ASP.NET ez a köztes szoftver inicializálva van a Startup.cs fájlban.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

A köztes szoftver hozzáadódik a webes API-hoz ezzel az utasítással:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 A ASP.NET Core-sablonok jelenleg olyan Azure Active Directory (Azure AD) webes API-kat hoznak létre, amelyek a szervezeten vagy bármely szervezeten belül bejelentkeznek a felhasználókba. Nem jelentkeznek be személyes fiókkal rendelkező felhasználók. A sablonokat azonban módosíthatja a Microsoft identity platform végpontjának használatára, ha hozzáadja ezt a kódot Startup.cs:

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

Az előző kódrészlet a [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)ASP.NET Core web API növekményes oktatóanyagából származik. Az **AddProtectedWebApi** metódust, amely többet tesz, mint amennyit a kódrészlet mutat, Startup.cs hívják.

## <a name="token-validation"></a>Token érvényesítése

Az előző kódrészletben a JwtBearer köztes szoftver, mint például az OpenID Connect köztes `TokenValidationParameters`szoftver a webes alkalmazásokban, érvényesíti a token értékét. A jogkivonat szükség szerint visszafejtésre kerül, a jogcímek kibontása és az aláírás ellenőrzése történik. A köztes szoftver ezután ellenőrzi a jogkivonatot az adatok ellenőrzésével:

- Célközönség: A jogkivonat a webes API-hoz van célozva.
- Sub: Egy olyan alkalmazáshoz adták ki, amely meghívhatja a webes API-t.
- Kibocsátó: Egy megbízható biztonsági jogkivonat-szolgáltatás (STS) adta ki.
- Lejárat: Élettartama hatótávolságon belül van.
- Nem babrálták meg.

Különleges ellenőrzések is lehetnek. Például lehetséges, hogy ellenőrizze, hogy az aláíró kulcsok, ha egy jogkivonatba ágyazva, megbízhatóak, és hogy a jogkivonat nem játssza vissza. Végül egyes protokollok speciális érvényesítést igényelnek.

### <a name="validators"></a>Validátorok

Az érvényesítési lépéseket a validátorok rögzítik, amelyeket a [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) nyílt forráskódú függvénytár biztosít. A validátorok a [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)könyvtárforrásfájlban vannak definiálva.

Ez a táblázat a validátorokat ismerteti:

| Validator | Leírás |
|---------|---------|
| **Közönség érvényesítése** | Biztosítja, hogy a jogkivonat az alkalmazás, amely érvényesíti a jogkivonatot az Ön számára. |
| **ValidIssuer** | Biztosítja, hogy a jogkivonatot egy megbízható STS adta ki, ami azt jelenti, hogy olyan személytől származik, akiben megbízik. |
| **ValidateIssuerSigningKey** | Biztosítja, hogy a jogkivonatot érvényesítő alkalmazás megbízik a jogkivonat aláírásához használt kulcsban. Van egy különleges eset, ahol a kulcs be van ágyazva a jogkivonatba. De ez az ügy általában nem merül fel. |
| **ValidateLifetime (Élettartam érvényesítése)** | Biztosítja, hogy a jogkivonat még mindig vagy már érvényes. A validator ellenőrzi, hogy a jogkivonat élettartama a nem korábban megadott tartományban **van-e,** és **lejár-e** a jogcímek. |
| **Érvényesítaláírás** | Biztosítja, hogy a jogkivonatot nem módosították. |
| **ValidateTokenReplay** | Biztosítja, hogy a token ne legyen újralejátszva. Van egy különleges eset néhány egyszeri használatú protokollra. |

A validátorok a **TokenValidationParameters** osztály tulajdonságaihoz vannak társítva. A tulajdonságok inicializálása a ASP.NET és ASP.NET Core konfigurációból.

A legtöbb esetben nem kell módosítania a paramétereket. Az alkalmazások, amelyek nem egyetlen bérlők kivételek. Ezek a webalkalmazások bármely szervezet vagy személyes Microsoft-fiók felhasználóinak fogadására is lehetőséget választhatnak. A kibocsátókat ebben az esetben validálni kell.

## <a name="token-validation-in-azure-functions"></a>Tokenérvényesítés az Azure Functionsben

A bejövő hozzáférési jogkivonatok az Azure Functionsben is érvényesítheti. Az ilyen érvényesítésre a [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [A NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)és a [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)példákat talál.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hatókörök és alkalmazásszerepkörök ellenőrzése a kódban](scenario-protected-web-api-verification-scope-app-roles.md)
