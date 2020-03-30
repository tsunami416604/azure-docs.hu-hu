---
title: REST API jogcímek cseréje érvényesítésként
titleSuffix: Azure AD B2C
description: Forgatókönyv az Azure AD B2C felhasználói út létrehozásához, amely a RESTful szolgáltatásokkal kommunikál.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330821"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Forgatókönyv: Rest API-jogcímek cseréjének integrálása az Azure AD B2C felhasználói útba a felhasználói bevitel érvényesítéséhez

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) adakai identitáskezelési keretrendszer (IEF) lehetővé teszi az identitásfejlesztők számára, hogy egy FELHASZNÁLÓI út során integrálják a RESTful API-val való interakciót.  A forgatókönyv végén létrehozhat egy Azure AD B2C felhasználói utat, amely a [RESTful szolgáltatásokkal](custom-policy-rest-api-intro.md) együttműködve ellenőrzi a felhasználói bevitelt.

Ebben a forgatókönyvben hozzáadjuk a felhasználók számára, hogy adja meg a hűségszámot az Azure AD B2C regisztrációs oldalon. Ezeket az adatokat a REST API-ba elküldve ellenőrizsszük, hogy az e-mail és a hűségszám ezen kombinációja le van-e képezve egy promóciós kódhoz. Ha a REST API talál egy promóciós kódot a felhasználó, vissza kerül az Azure AD B2C. Végül a promóciós kód kerül be a jogkivonat-jogcímek az alkalmazás számára, hogy felhasználja.

Az interakciót vezénylési lépésként is tervezheti. Ez akkor megfelelő, ha a REST API nem ellenőrzi az adatokat a képernyőn, és mindig jogcímeket ad vissza. További információ: [Forgatókönyv: Rest API-jogcímek cseréjének integrálása az Azure AD B2C felhasználói út vezénylési lépésként.](custom-policy-rest-api-claims-exchange.md)

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a helyi fiókkal való bejelentkezéshez.
- Ismerje meg, hogyan [integrálhatja a REST API-jogcímek cseréjét az Azure AD B2C egyéni szabályzatába.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>REST API-végpont előkészítése

Ebben a forgatókönyvben rendelkeznie kell egy REST API-t, amely ellenőrzi, hogy egy e-mail cím regisztrálva van-e a háttérrendszer egy hűségazonosítóval. Ha regisztrálva van, a REST API-nak vissza kell adnia egy regisztrációs promóciós kódot, amelyet az ügyfél az alkalmazáson belüli áruk megvásárlásához használhat. Ellenkező esetben a REST API-nak http 409-es hibaüzenetet kell visszaadnia: "A(z) "{loyalty ID}" hűségazonosító nincs társítva a következő höz: "{email}" e-mail cím.".

A következő JSON-kód bemutatja az Azure AD B2C által a REST API-végpontnak küldött adatokat. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Miután a REST API érvényesíti az adatokat, vissza kell adnia egy HTTP 200 (Ok), a következő JSON-adatokkal:

```json
{
    "promoCode": "24534"
}
```

Ha az ellenőrzés sikertelen, a REST API-nak http 409-es (Ütközés) http-t kell visszaadnia a `userMessage` JSON-elemmel. Az IEF elvárja a `userMessage` REST API-t visszaadott állítást. Ez a jogcím karakterláncként jelenik meg a felhasználó számára, ha az érvényesítés sikertelen.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>A RESTful API műszaki profiljának konfigurálása 

A [restful technikai profil](restful-technical-profile.md) támogatást nyújt a saját RESTful szolgáltatáshoz való kapcsolódáshoz. Az Azure AD B2C adatokat küld a `InputClaims` RESTful szolgáltatás `OutputClaims` egy gyűjteményben, és adatokat kap vissza egy gyűjteményben. Keresse meg a **ClaimsProviders** elemet, és adjon hozzá egy új jogcímszolgáltatót az alábbiak szerint:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Ebben a példában a `userLanguage` lesz elküldve `lang` a REST szolgáltatás, mint a JSON hasznos adat. A jogcím `userLanguage` értéke az aktuális felhasználói nyelv azonosítóját tartalmazza. További információt a [jogcímfeloldó című](claim-resolver-overview.md)témakörben talál.

A fenti `AuthenticationType` `AllowInsecureAuthInProduction` megjegyzéseket, és adja meg a módosításokat kell végrehajtani, amikor egy éles környezetben. A RESTful API-k éles környezetben való védelméről a [Biztonságos RESTful API című témakörben](secure-rest-api.md)olvashat.

## <a name="validate-the-user-input"></a>A felhasználói bevitel ellenőrzése

Ahhoz, hogy a regisztráció során megkaphassa a felhasználó hűségszámát, engedélyeznie kell, hogy a felhasználó beírja ezeket az adatokat a képernyőn. Adja hozzá a **hűségazonosító** kimeneti jogcímet a regisztrációs laphoz, és adja hozzá `OutputClaims` a meglévő regisztrációs technikai profil szakasz eleméhez. Adja meg a kimeneti jogcímek teljes listáját a jogcímek képernyőn megjelenő sorrendjének szabályozásához.  

Adja hozzá az érvényesítési technikai profilhivatkozást a `REST-ValidateProfile`regisztrációs technikai profilhoz, amely a . Az új érvényesítési technikai profil hozzáadódik az `<ValidationTechnicalProfiles>` alapházirendben meghatározott gyűjtemény tetejéhez. Ez a viselkedés azt jelenti, hogy csak a sikeres érvényesítés után az Azure AD B2C továbblép a fiók létrehozásához a címtárban.   

1. Keresse meg a **ClaimsProviders** elemet. Új jogcímszolgáltató hozzáadása az alábbiak szerint:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Jogcím felvétele a jogkivonatba 

Ha vissza szeretné küldeni a promóciós kód igényt a függő <em> `SocialAndLocalAccounts/` </em> entitás alkalmazás, adjon hozzá egy kimeneti jogcímet a fájlhoz. A kimeneti jogcím lehetővé teszi, hogy a jogcím sikeres felhasználói út után hozzáadódik a jogkivonathoz, és elküldésre kerül az alkalmazásnak. Módosítsa a technikai profil elem a függő `promoCode` entitás szakaszban hozzáadja a kimeneti jogcímként.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Identitáskezelési keretrendszert**.
1. Válassza **az Egyéni házirend feltöltése**lehetőséget, majd töltse fel a módosított házirendfájlokat: *TrustFrameworkExtensions.xml*és *SignUpOrSignin.xml*. 
1. Jelölje ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, és kattintson a **Futtatás gombra.**
1. Önnek képesnek kell lennie arra, hogy iratkozzon fel egy e-mail címet.
1. Kattintson a **Regisztráció most** linkre.
1. A **Hűségazonosító**mezőbe írja be a 1234-es beírást, és kattintson a **Folytatás**gombra. Ezen a ponton meg kell kapnia egy érvényesítési hibaüzenetet.
1. Váltson másik értékre, és kattintson a **Folytatás gombra.**
1. Az alkalmazásnak küldött jogkivonat `promoCode` tartalmazza a jogcímet.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>További lépések

Az API-k védelméről az alábbi cikkekben olvashat:

- [Forgatókönyv: Integrálja a REST API-jogcímek cseréjét az Azure AD B2C felhasználói útba vezénylési lépésként](custom-policy-rest-api-claims-exchange.md)
- [Biztosítsa a RESTful API-t](secure-rest-api.md)
- [Hivatkozás: RESTful műszaki profil](restful-technical-profile.md)
