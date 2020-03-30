---
title: REST API-jogcímek cseréje – Azure Active Directory B2C
description: REST API-jogcímek cseréje az Active Directory B2C egyéni házirendjeihez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330724"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Forgatókönyv: REST API-jogcímek cseréje az Azure Active Directory B2C egyéni szabályzataihoz

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) lehetővé teszi az identitásfejlesztők számára, hogy egy felhasználói út során integrálják a RESTful API-val való interakciót. A forgatókönyv végén létrehozhat egy Azure AD B2C felhasználói utat, amely kölcsönhatásba lép a [RESTful szolgáltatásokkal.](custom-policy-rest-api-intro.md)

Ebben a forgatókönyvben a vállalati üzletági munkafolyamattal való integrációval gazdagítjuk a felhasználó tokenadatait. A helyi vagy összevont fiókkal való bejelentkezés során az Azure AD B2C meghívja a REST API-t, hogy a felhasználó kiterjesztett profiladatait egy távoli adatforrásból lekérje. Ebben a példában az Azure AD B2C elküldi a felhasználó egyedi azonosítóját, az objectId. A REST API ezután visszaadja a felhasználó számlaegyenlegét (egy véletlen számot). Ezt a mintát használja kiindulási pontként a saját CRM-rendszerével, marketingadatbázisával vagy bármely üzletági munkafolyamattal való integrációhoz.

A kapcsolati tevékenységet érvényesítési technikai profilként is megtervezheti. Ez akkor megfelelő, ha a REST API adatokat fog validálni a képernyőn, és jogcímeket ad vissza. További információ: [Forgatókönyv: REST API-jogcímek cseréjének integrálása az Azure AD B2C felhasználói úton a felhasználói bevitel érvényesítéséhez.](custom-policy-rest-api-claims-validation.md)

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a helyi fiókkal való bejelentkezéshez.
- Ismerje meg, hogyan [integrálhatja a REST API-jogcímek cseréjét az Azure AD B2C egyéni szabályzatába.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>REST API-végpont előkészítése

Ebben a forgatókönyvben rendelkeznie kell egy REST API-t, amely ellenőrzi, hogy a felhasználó Azure AD B2C objectId regisztrálva van-e a háttérrendszerben. Ha regisztrálva van, a REST API a felhasználói fiók egyenlegét adja vissza. Ellenkező esetben a REST API regisztrálja az új fiókot a címtárban, és visszaadja a kezdő egyenleget. `50.00`

A következő JSON-kód bemutatja az Azure AD B2C által a REST API-végpontnak küldött adatokat. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Miután a REST API érvényesíti az adatokat, vissza kell adnia egy HTTP 200 (Ok), a következő JSON-adatokkal:

```json
{
    "balance": "760.50"
}
```

A REST API-végpont beállítása kívül esik a cikk hatókörén. Létrehoztunk egy [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) mintát. A teljes Azure-függvénykódot a [GitHubon](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)érheti el.

## <a name="define-claims"></a>Jogcímek definiálása

A jogcím az adatok ideiglenes tárolását biztosítja az Azure AD B2C-szabályzat végrehajtása során. A jogcímek deklarálhatók a [jogcímséma](claimsschema.md) szakaszban. 

1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema** elemhez.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>A RESTful API műszaki profiljának konfigurálása 

A [restful technikai profil](restful-technical-profile.md) támogatást nyújt a saját RESTful szolgáltatással való kapcsolathoz. Az Azure AD B2C adatokat küld a `InputClaims` RESTful szolgáltatás `OutputClaims` egy gyűjteményben, és adatokat kap vissza egy gyűjteményben. Keresse meg a **ClaimsProviders** elemet a <em>**`TrustFrameworkExtensions.xml`**</em> fájlban, és adjon hozzá egy új jogcímszolgáltatót az alábbiak szerint:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Ebben a példában a `userLanguage` lesz elküldve `lang` a REST szolgáltatás, mint a JSON hasznos adat. A jogcím `userLanguage` értéke az aktuális felhasználói nyelv azonosítóját tartalmazza. További információt a [jogcímfeloldó című](claim-resolver-overview.md)témakörben talál.

A fenti `AuthenticationType` `AllowInsecureAuthInProduction` megjegyzéseket, és adja meg a módosításokat kell végrehajtani, amikor egy éles környezetben. A RESTful API-k éles környezetben való védelméről a [Biztonságos RESTful API című témakörben](secure-rest-api.md)olvashat.

## <a name="add-an-orchestration-step"></a>Vezénylési lépés hozzáadása

[A felhasználói utak](userjourneys.md) explicit elérési utakat határoznak meg, amelyeken keresztül a házirend lehetővé teszi, hogy a függő entitás alkalmazás beszerezze a kívánt jogcímeket a felhasználó számára. A felhasználói út vezénylési szekvenciaként jelenik meg, amelyet a sikeres tranzakcióhoz végig kell követni. Hozzáadhat vagy kivonhat vezénylési lépéseket. Ebben az esetben egy új vezénylési lépés, amely a felhasználói regisztráció vagy a REST API-híváson keresztül az alkalmazásnak megadott információk bővítésére szolgál.

1. Nyissa meg a házirend alapfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
1. Keresse meg `<UserJourneys>` az elemet. Másolja a teljes elemet, majd törölje azt.
1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
1. Illessze `<UserJourneys>` be a kiterjesztések fájlt, `<ClaimsProviders>` bezárása után az elem.
1. Keresse `<UserJourney Id="SignUpOrSignIn">`meg a , és adja hozzá a következő vezénylési lépés az utolsó előtt.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactor az utolsó vezénylési lépés módosításával a `Order` . `8` Az utolsó két vezénylési lépésnek a következőkre kell hasonlítania:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Ismételje meg az utolsó két lépést a **ProfileEdit** és **PasswordReset** felhasználói utakhoz.


## <a name="include-a-claim-in-the-token"></a>Jogcím felvétele a jogkivonatba 

Ha vissza `balance` szeretné küldeni a jogcímet a függő entitás <em> `SocialAndLocalAccounts/` </em> alkalmazásnak, adjon hozzá egy kimeneti jogcímet a fájlhoz. Egy kimeneti jogcím hozzáadása a jogcímet a jogkivonategy sikeres felhasználói út után adja ki, és az alkalmazásnak küldi el. Módosítsa a technikai profil elem a függő `balance` entitás szakaszban, hogy adja hozzá a kimeneti jogcím.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Ismételje meg ezt a lépést a **ProfileEdit.xml**és **passwordreset.xml** felhasználói utak esetén.

Mentse a módosított fájlokat: *TrustFrameworkBase.xml*, és *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*és *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Identitáskezelési keretrendszert**.
1. Válassza **az Egyéni házirend feltöltése**lehetőséget, majd töltse fel a módosított házirendfájlokat: *TrustFrameworkBase.xml*és *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*és *PasswordReset.xml*. 
1. Jelölje ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, és kattintson a **Futtatás gombra.**
1. E-mail cím vagy Facebook-fiók használatával regisztrálhatsz.
1. Az alkalmazásnak küldött jogkivonat `balance` tartalmazza a jogcímet.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>További lépések


## <a name="next-steps"></a>További lépések

Az API-k védelméről az alábbi cikkekben olvashat:

- [Forgatókönyv: Integrálja a REST API-jogcímek cseréjét az Azure AD B2C felhasználói útba vezénylési lépésként](custom-policy-rest-api-claims-exchange.md)
- [Biztosítsa a RESTful API-t](secure-rest-api.md)
- [Hivatkozás: RESTful műszaki profil](restful-technical-profile.md)
