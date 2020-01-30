---
title: REST API követelések cseréje – Azure Active Directory B2C
description: REST API a jogcímeket a Active Directory B2C egyéni házirendjeihez adja hozzá.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1caf0b3f6396cc008e77bb96e686fe78f462850b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849096"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>REST API jogcímek hozzáadása egyéni házirendekhez Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A REST API-val a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeihez](custom-policy-overview.md) is hozzáadhat interakciókat. Ez a cikk bemutatja, hogyan hozhat létre olyan Azure AD B2C felhasználói utat, amely a REST-szolgáltatásokkal kommunikál.

Az interakció magában foglalja a jogcímek REST API jogcímek és Azure AD B2C közötti információcserét. A jogcímek cseréje a következő jellemzőkkel rendelkezik:

- Előkészítési lépésként is megtervezhető.
- Külső műveletet indíthat el. Például naplózhat egy eseményt egy külső adatbázisban.
- Egy érték beolvasására, majd a felhasználói adatbázisba való tárolására használható.
- Módosíthatja a végrehajtás folyamatát.

A cikkben szereplő forgatókönyv a következő műveleteket tartalmazza:

1. Keresse meg a felhasználót egy külső rendszeren.
2. Szerezze be azt a várost, ahol a felhasználó regisztrálva van.
3. Ezt az attribútumot jogcímként visszaküldi az alkalmazásnak.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit.
- Egy REST API végpont, amely együttműködik a szolgáltatással. Ez a cikk egy egyszerű Azure-függvényt használ példaként. Az Azure-függvény létrehozásával kapcsolatban tekintse [meg az első függvény létrehozása a Azure Portalban](../azure-functions/functions-create-first-azure-function.md)című témakört.

## <a name="prepare-the-api"></a>Az API előkészítése

Ebben a szakaszban előkészíti az Azure-függvényt, hogy megkapja a `email`értékét, majd visszaadja a Azure AD B2C által igénybe vehető `city` értékét.

Módosítsa a létrehozott Azure-függvény Run. CSX fájlját a következő kód használatára:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>A jogcím-Exchange konfigurálása

A technikai profil biztosítja a jogcím-Exchange konfigurációját.

Nyissa meg a *TrustFrameworkExtensions. XML* fájlt, és adja hozzá a következő **ClaimsProvider** XML-elemet a **ClaimsProviders** elemhez.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

A **szabályzattípushoz** elem határozza meg a REST szolgáltatásnak eljuttatott jogcímeket. Ebben a példában a jogcím `givenName` a REST szolgáltatásnak a jogcím `email`. A **OutputClaims** elem határozza meg a REST szolgáltatástól várt jogcímeket.

A fenti megjegyzések `AuthenticationType` és `AllowInsecureAuthInProduction`, hogy milyen módosításokat kell végeznie az éles környezetbe való áttéréskor. A REST API-k éles környezetben történő biztonságossá tételéhez tekintse meg a [biztonságos REST API-k alapszintű hitelesítéssel](secure-rest-api-dotnet-basic-auth.md) és [biztonságos REST API](secure-rest-api-dotnet-certificate-auth.md)-k hitelesítéssel című témakörét.

## <a name="add-the-claim-definition"></a>Jogcím-definíció hozzáadása

Adja meg a `city` definícióját a **BuildingBlocks** elemben. Ezt az elemet a TrustFrameworkExtensions. xml fájl elején találja.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Előkészítési lépés hozzáadása

Sok olyan felhasználási eset van, ahol a REST API hívást összehangoló lépésként lehet használni. Az előkészítési lépésként egy külső rendszer frissítéseként is használható, miután egy felhasználó sikeresen végrehajtotta a feladatot, például az első regisztrációt, vagy a profil frissítését, hogy szinkronizálva legyen az információ. Ebben az esetben a profil szerkesztése után az alkalmazásnak biztosított információk kiegészítésére kerül sor.

Adjon hozzá egy lépést a profil szerkesztése felhasználói útra. A felhasználó hitelesítése után (a 1-4-es lépések a következő XML-ben), és a felhasználó megadta a frissített profil adatait (5. lépés). Másolja a profil szerkesztése felhasználói út XML-kódját a *TrustFrameworkBase. XML* fájlból a *TrustFrameworkExtensions. XML* fájlba a **UserJourneys** elemen belül. Ezután végezze el a módosítást 6. lépésként.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

A felhasználói utazás utolsó XML-fájljának a következő példához hasonlóan kell kinéznie:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Jogcím hozzáadása

Szerkessze a *ProfileEdit. XML* fájlt, és adja hozzá `<OutputClaim ClaimTypeReferenceId="city" />` a **OutputClaims** elemhez.

Az új jogcím hozzáadása után a technikai profil a következő példához hasonlóan néz ki:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>A módosítások feltöltése és tesztelés

1. Választható A folytatás előtt mentse a fájlok meglévő verzióját (letöltéssel).
2. Töltse fel a *TrustFrameworkExtensions. XML* és a *ProfileEdit. XML* fájlt, és jelölje be a meglévő fájl felülírásához.
3. Válassza a **B2C_1A_ProfileEdit**lehetőséget.
4. Az egyéni házirend áttekintés lapján jelölje ki az **alkalmazás kiválasztása** lehetőséget, majd válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. Győződjön meg arról, hogy a **Válasz URL-címe** `https://jwt.ms`.
4. Válassza a **Futtatás most**lehetőséget. Jelentkezzen be a fiókja hitelesítő adataival, majd kattintson a **Folytatás**gombra.

Ha minden helyesen van beállítva, a jogkivonat tartalmazza az új jogcímet `city`, `Redmond`értékkel.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Következő lépések

Az interakciót érvényesítő profilként is megtervezheti. További információ [: bemutató: REST API jogcím-cserék integrálása a Azure ad B2C felhasználói úton a felhasználói adatok érvényesítése során](custom-policy-rest-api-claims-validation.md).

[Módosítsa a profil szerkesztését, hogy további információkat gyűjtsön a felhasználóktól](custom-policy-custom-attributes.md)

[Hivatkozás: REST-technikai profil](restful-technical-profile.md)

Az API-k védelméről a következő cikkekben tájékozódhat:

* [A REST API biztonságossá tétele egyszerű hitelesítéssel (Felhasználónév és jelszó)](secure-rest-api-dotnet-basic-auth.md)
* [A REST API biztonságossá tétele Ügyféltanúsítványok használatával](secure-rest-api-dotnet-certificate-auth.md)
