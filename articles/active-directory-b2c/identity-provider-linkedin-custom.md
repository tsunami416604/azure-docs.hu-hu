---
title: Bejelentkezés beállítása LinkedIn-fiókkal egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Állítsa be a bejelentkezést egy LinkedIn-fiókkal az Azure Active Directory B2C-ben egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188171"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása LinkedIn-fiókkal az Azure Active Directory B2C egyéni szabályzataival

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a felhasználók számára egy LinkedIn-fiókból [egyéni szabályzatok](custom-policy-overview.md) használatával az Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.
- LinkedIn-fiók – Ha még nem rendelkezik [ilyenel, hozzon létre egy fiókot.](https://www.linkedin.com/start/join)
- LinkedIn Page - A következő szakaszban létrehozott LinkedIn-alkalmazáshoz [linkedIn-lapra](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) van szükség.

## <a name="create-an-application"></a>Alkalmazás létrehozása

A LinkedIn identitásszolgáltatóként való használatához az Azure AD B2C-ben létre kell hoznia egy LinkedIn-alkalmazást.

### <a name="create-app"></a>Alkalmazás létrehozása

1. Jelentkezzen be a [LinkedIn alkalmazáskezelő](https://www.linkedin.com/secure/developer?newapp=) webhelyére a LinkedIn-fiók hitelesítő adataival.
1. Válassza az **Alkalmazás létrehozása** lehetőséget.
1. Adjon meg egy **alkalmazásnevet**.
1. Adja meg a LinkedIn-oldal nevének megfelelő **vállalatnevet.** Hozzon létre egy LinkedIn-oldalt, ha még nem rendelkezik ilyensel.
1. (Nem kötelező) Adja meg **az adatvédelmi irányelv URL-címét**. Érvényes URL-címnek kell lennie, de nem kell elérhető végpontnak lennie.
1. Adja meg **az üzleti e-mail .**
1. **Alkalmazásem-kép** feltöltése. Az emblémaképnek négyzet alakúnak kell lennie, méretei pedig legalább 100x100 képpont.
1. Hagyja meg az alapértelmezett beállításokat a **Termékek** szakaszban.
1. Tekintse át a **jogi értelemben**szereplő információkat. Ha elfogadja a feltételeket, jelölje be a jelölőnégyzetet.
1. Válassza az **Alkalmazás létrehozása** lehetőséget.

### <a name="configure-auth"></a>Hitelesítés konfigurálása

1. Válassza az **Auth** lapot.
1. Rögzítse az **ügyfélazonosítót**.
1. Felfedése és rögzítése az **ügyfél titok**.
1. Az **OAuth 2.0 beállítások csoportban**adja meg a következő **átirányítási URL-címet.** Cserélje `your-tenant` le a bérlő nevét. Használja **az összes kisbetűt** a bérlői névhez, még akkor is, ha az Azure AD B2C-ben nagybetűkkel van definiálva.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben korábban rögzített ügyféltitok tárolásához kell.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
5. Válassza **a Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
6. A **Beállítások** `Manual`területen válassza a lehetőséget.
7. Adja meg a házirendkulcs **nevét.** Például: `LinkedInSecret`. A *B2C_1A_* előtag automatikusan hozzáadódik a kulcs nevéhez.
8. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
9. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
10. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók egy LinkedIn-fiók használatával jelentkezzenek be, meg kell határoznia a fiókot olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

A LinkedIn-fiók jogcímszolgáltatóként definiálása a házirend bővítményfájlban található **ClaimsProviders** elemhez való hozzáadásával.

1. Nyissa meg a *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** fájlt a szerkesztőben. Ez a fájl az [egyéni házirend kezdőcsomagjában][starter-pack] található, amelyet az egyik előfeltétel részeként töltött le.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
1. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Cserélje le **a client_id** értékét a korábban rögzített LinkedIn-alkalmazás ügyfélazonosítójára.
1. Mentse a fájlt.

### <a name="add-the-claims-transformations"></a>Jogcímátalakítások hozzáadása

A LinkedIn technikai profil megköveteli a **ExtractGivenNameFromLinkedInResponse** és **ExtractSurNameFromLinkedInResponse** jogcímátalakítások hozzá kell adni a listához claimsTransformations. Ha nincs megadva **a fájlban definiált Jogcímátalakítási** elem, adja hozzá a szülő XML-elemeket az alábbi módon. A jogcímátalakításokhoz egy **új, nullStringClaim**nevű jogcímtípusra is szükség van.

Adja hozzá a **BuildingBlocks** elemet a *TrustFrameworkExtensions.xml* fájl tetejéhez. Egy példa a *TrustFrameworkBase.xml fájlban* található.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

Most már rendelkezik egy szabályzatot úgy, hogy az Azure AD B2C tudja, hogyan kommunikáljon a LinkedIn-fiókkal. Próbálja meg feltölteni a házirend bővítményfájlját, és erősítse meg, hogy eddig nem volt-e problémája.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
2. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. A rendelkezésre álló sablon támpontként hoz létre egy meglévő sablonfelhasználói út másolatát, majd úgy módosítja azt, hogy az is rendelkezik a LinkedIn-identitásszolgáltatóval.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagban.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs vagy bejelentkezési képernyőn. Ha egy LinkedIn-fiókhoz hozzáad egy **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor egy felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely tartalmazza `Order="1"` a létrehozott felhasználói út.
2. A **ClaimsProviderSelections**csoportban adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `LinkedInExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy LinkedIn-fiók egy jogkivonat fogadására.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange-elemet,** ügyelve arra, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil azonosítójával. Például: `LinkedIn-OAUTH`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInLinkedIn.xml*fájlra.
2. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInLinkedIn`.
3. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a létrehozott új felhasználói út (SignUpSignLinkedIn) azonosítójának.
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van jelölve az **Alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**gombra kattintva.

## <a name="migration-from-v10-to-v20"></a>Áttelepítés az 1.0-s és 2.0-s alkalmazásközött

LinkedIn nemrég [frissítette API-k a v1.0 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). A meglévő konfiguráció áttelepítéséhez az új konfigurációba, használja a következő szakaszokban található információkat a technikai profil elemeinek frissítéséhez.

### <a name="replace-items-in-the-metadata"></a>A metaadatok elemeinek cseréje

A **TechnicalProfile**meglévő **metaadat-elemében** frissítse a következő **elemelemeket:**

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Címzett:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Elemek hozzáadása a metaadatokhoz

A **TechnicalProfile metaadatai** között adja hozzá a következő **TechnicalProfile** **elemelemeket:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>A OutputClaims frissítése

A **technicalprofile**meglévő **OutputClaims** elemében frissítse a következő **OutputClaim** elemeket:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Címzett:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Új OutputClaimsTransformation elemek hozzáadása

A **TechnicalProfile OutputClaimsTransformations** elemében adja hozzá a következő **OutputClaimsTransformation** elemeket: **TechnicalProfile**

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Az új jogcímátalakítások és jogcímtípusok meghatározása

Az utolsó lépésben új jogcímátalakításokat adott hozzá, amelyeket meg kell határozni. A jogcímátalakítások meghatározásához vegye fel őket a **jogcímek listájára.** Ha nincs megadva **a fájlban definiált Jogcímátalakítási** elem, adja hozzá a szülő XML-elemeket az alábbi módon. A jogcímátalakításokhoz egy **új, nullStringClaim**nevű jogcímtípusra is szükség van.

A **BuildingBlocks** elemet a fájl tetejének közelében kell hozzáadni. Tekintse meg példaként a *TrustframeworkBase.xml fájlt.*

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>E-mail cím beszerzése

A LinkedIn-áttelepítés részeként az 1.0-s és a 2.0-s alkalmazás között egy másik API-hoz való további hívás szükséges az e-mail-cím megszerzéséhez. Ha a regisztráció során meg kell szereznie az e-mail címet, tegye a következőket:

1. Hajtsa végre a fenti lépéseket, hogy az Azure AD B2C a LinkedIn-nel egyeztethese a felhasználó bejelentkezését. Az összevonás részeként az Azure AD B2C megkapja a LinkedIn hozzáférési jogkivonatát.
2. Mentse a LinkedIn-hozzáférési jogkivonatot jogcímre. [Lásd az utasításokat itt](idp-pass-through-custom.md).
3. Adja hozzá a következő jogcímszolgáltatót, `/emailAddress` amely a kérelmet a LinkedIn API-jához adja. A kérelem engedélyezéséhez szüksége van a LinkedIn-hozzáférési jogkivonatra.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Adja hozzá a következő vezénylési lépést a felhasználói úthoz, hogy az API-jogcímszolgáltató akkor aktiválódik, amikor egy felhasználó bejelentkezik a LinkedIn használatával. Győződjön meg `Order` róla, hogy megfelelően frissíti a számot. Adja hozzá ezt a lépést közvetlenül a LinkedIn technikai profilját kiváltó vezénylési lépés után.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Az e-mail cím beszerzése a LinkedIn-től a regisztráció során nem kötelező. Ha úgy dönt, hogy nem kapja meg az e-mailt a LinkedIn-től, de a regisztráció során is szüksége van rá, a felhasználónak manuálisan kell megadnia az e-mail címet, és érvényesítenie kell azt.

A LinkedIn-identitásszolgáltatót használó házirend teljes mintáját az [Egyéni házirend kezdőcsomagjában tetszés szerint.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
