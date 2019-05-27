---
title: REST API-jogcímek cseréje – Azure Active Directory B2C |} A Microsoft Docs
description: Adja hozzá az Active Directory B2C-vel egyéni szabályzatok REST API-val jogcím cseréje.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e705c12782310597ea14d5253aba8b6a1a004e6d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952785"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Adja hozzá a REST API-val jogcím cseréje az Azure Active Directory B2C-vel egyéni szabályzatok

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

RESTful API-val való interakció is hozzáadhat a [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben. Ez a cikk bemutatja, hogyan hozhat létre egy Azure AD B2C felhasználói interakciósorozat, együttműködő RESTful-szolgáltatásokat.

A kapcsolati a jogcímek az exchange között a REST API-jogcímek és az Azure AD B2C-vel is tartalmaz. Jogcím cseréje a következő jellemzőkkel rendelkeznek:

- Megtervezhetők úgy, mint egy vezénylési lépés.
- Egy külső műveletet is indíthat. Például a külső adatbázis azt is naplózhat egy eseményt.
- Olyan érték beolvasása, és tárolja a felhasználói adatbázis használható.
- Módosíthatja a folyamat végrehajtása. 

A forgatókönyv, amely ebben a cikkben szerepel az alábbi műveleteket tartalmazza:

1. Keresse meg a felhasználó egy külső rendszerben.
2. A város, ahol regisztrálva van-e az adott felhasználó kaphat.
3. Ezt az attribútumot térjen vissza az alkalmazás jogcímként.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).
- REST API-végpont használatával kommunikálhat. A cikk használ egy egyszerű Azure-függvény példaként. Az Azure-függvény létrehozásához lásd: [az első függvény létrehozása az Azure Portalon](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Készítse elő az API-hoz

Ebben a szakaszban az Azure-függvény értéket kap előkészítése `email`, és visszaadja az érték `city` , amely használható az Azure AD B2C által jogcímként.

Módosítsa a run.csx fájlt az Azure-függvény, amely létrehozta a következő kóddal: 

```
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

## <a name="configure-the-claims-exchange"></a>A jogcímek az exchange konfigurálása

A technikai profil biztosít a konfiguráció a jogcím Exchange-hez. 

Nyissa meg a *TrustFrameworkExtensions.xml* fájlt, és adja hozzá a következő XML-elemeket belül a **ClaimsProvider** elemet.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

A **InputClaims** elem definiálja a jogcímeket, a REST-szolgáltatás küldött. Ebben a példában a jogcím értéke `givenName` a REST-szolgáltatás, mint a jogcím küldendő `email`. A **OutputClaims** elem definiálja a jogcímeket, amelyek várhatóan a REST-szolgáltatás.

## <a name="add-the-claim-definition"></a>Adja hozzá a jogcím-definíció

Adja hozzá a definíciót a `city` belül a **BuildingBlocks** elemet. Ez az elem a TrustFrameworkExtensions.xml fájl elején annak.

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

## <a name="add-an-orchestration-step"></a>Egy vezénylési lépés hozzáadása

Nincsenek számos használati esetek, amikor egy vezénylési lépés használható a REST API-hívás. Egy vezénylési lépésként használat frissítés után a felhasználó sikeresen végrehajtotta egy feladat, például az első regisztráció külső rendszerre, vagy a profil frissítése, hogy az adatok szinkronizálását. Ebben az esetben szolgál, mivel megvédi a profil szerkesztése után az alkalmazásnak megadott információkat.

Lépés hozzáadása a profil szerkesztése felhasználói interakciósorozat. Miután a felhasználó hitelesítése (vezénylési lépésekből 1 – 4 a következő XML formátumú), és a felhasználó megadta a frissített profil információkat (5. lépés). Másolás a profil felhasználói interakciósorozat XML-kódot a Szerkesztés a *TrustFrameworkBase.xml* fájlt a *TrustFrameworkExtensions.xml* belül fájlt a **UserJourneys** elem. Végezze el a módosítást, a 6. lépés.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

A felhasználói út tartalomdefinícióinak végső XML példához hasonlóan kell kinéznie:

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Az igényt hozzáadása

Szerkessze a *ProfileEdit.xml* fájlt, és `<OutputClaim ClaimTypeReferenceId="city" />` , a **OutputClaims** elemet.

Az új jogcímet ad hozzá, miután a technikai profil példához hasonlóan néz ki:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Töltse fel a módosításokat, és tesztelése

1. (Nem kötelező:) Mentse a meglévő verziót (Letöltés) a fájlok folytatás előtt.
2. Töltse fel a *TrustFrameworkExtensions.xml* és *ProfileEdit.xml* , és válassza ki a meglévő fájl felülírásához.
3. Válassza ki **B2C_1A_ProfileEdit**.
4. A **válassza ki az alkalmazás** az egyéni házirend áttekintése lapon válassza ki a nevű webalkalmazás *webapp1* , amely korábban regisztrálva. Győződjön meg arról, hogy a **válasz URL-cím** van `https://jwt.ms`.
4. Válassza ki **Futtatás most**. Jelentkezzen be a fiók hitelesítő adatait, és kattintson a **Folytatás**.

Ha minden helyesen van beállítva, a jogkivonat tartalmazza-e az új jogcímet `city`, a következő értékkel `Redmond`.

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

## <a name="next-steps"></a>További lépések

- A kapcsolati érvényesítési profil is tervezhet. További információkért lásd: [forgatókönyv: A felhasználói bevitel auditáló integrálása a REST API-val jogcím cseréje az Azure AD B2C felhasználói interakciósorozatban szereplő](active-directory-b2c-rest-api-validation-custom.md).
- [További információkat kell gyűjteni a felhasználók a profilszerkesztés módosítása](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
