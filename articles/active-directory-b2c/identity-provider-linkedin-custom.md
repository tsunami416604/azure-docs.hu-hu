---
title: Bejelentkezés beállítása LinkedIn-fiókkal egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Bejelentkezés beállítása LinkedIn-fiókkal Azure Active Directory B2C egyéni házirendek használatával.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188171"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása LinkedIn-fiókkal egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a LinkedIn-fiók felhasználói számára a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) .

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](custom-policy-get-started.md).
- LinkedIn-fiók – ha még nem rendelkezik ilyennel, [hozzon létre egy fiókot](https://www.linkedin.com/start/join).
- LinkedIn lap – szükség van egy [LinkedIn-oldalra](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) , amelyet a következő szakaszban létrehozott LinkedIn-alkalmazáshoz szeretne rendelni.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ha a LinkedIn-t identitás-szolgáltatóként szeretné használni Azure AD B2Cban, létre kell hoznia egy LinkedIn-alkalmazást.

### <a name="create-app"></a>Alkalmazás létrehozása

1. Jelentkezzen be a [LinkedIn Application Management](https://www.linkedin.com/secure/developer?newapp=) webhelyre a LinkedIn-fiókja hitelesítő adataival.
1. Válassza az **alkalmazás létrehozása**lehetőséget.
1. Adja meg az **alkalmazás nevét**.
1. Adja meg a LinkedIn-oldal nevének megfelelő **vállalat** nevét. Hozzon létre egy LinkedIn-oldalt, ha még nem rendelkezik ilyennel.
1. Választható Adja meg az **adatvédelmi szabályzat URL-címét**. Érvényes URL-címnek kell lennie, de nem kell elérhető végpontnak lennie.
1. Adjon meg egy **üzleti e-mailt**.
1. Töltse fel az **alkalmazás emblémájának** képét. Az embléma képének négyzet alakúnak kell lennie, és a méreteinek legalább 100x100 képpont méretűnek kell lennie.
1. Hagyja meg az alapértelmezett beállításokat a **Products (termékek** ) szakaszban.
1. Tekintse át a **jogi feltételekben**bemutatott információkat. Ha elfogadja a feltételeket, jelölje be a jelölőnégyzetet.
1. Válassza az **alkalmazás létrehozása**lehetőséget.

### <a name="configure-auth"></a>Hitelesítés konfigurálása

1. Válassza az **Auth (hitelesítés** ) fület.
1. Jegyezze fel az **ügyfél-azonosítót**.
1. Az **ügyfél titkos kulcsának**felfedése és rögzítése.
1. A **OAuth 2,0 beállítások**területen adja hozzá a következő **átirányítási URL-címet**. Cserélje le a `your-tenant`t a bérlő nevére. A bérlő neveként használja az **összes** kisbetűt, még akkor is, ha az Azure ad B2C nagybetűvel van definiálva.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
6. A **Beállítások**területen válassza a `Manual`lehetőséget.
7. Adja meg a szabályzat kulcsának **nevét** . Például: `LinkedInSecret`. A rendszer automatikusan hozzáadja a kulcs nevét a *B2C_1A_* előtaghoz.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat**beállításnál válassza a `Signature`lehetőséget.
10. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók LinkedIn-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Definiáljon egy LinkedIn-fiókot jogcím-szolgáltatóként, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *SocialAndLocalAccounts/* * TrustFrameworkExtensions. XML** * fájlt a szerkesztőben. Ez a fájl az egyes előfeltételek egyikének részeként letöltött [egyéni házirend alapszintű csomagban][starter-pack] található.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

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

1. Cserélje le a **client_id** értékét a korábban rögzített LinkedIn-alkalmazás ügyfél-azonosítójával.
1. Mentse a fájlt.

### <a name="add-the-claims-transformations"></a>Jogcím-átalakítások hozzáadása

A LinkedIn technikai profilja megköveteli, hogy a **ExtractGivenNameFromLinkedInResponse** és a **ExtractSurNameFromLinkedInResponse** jogcím-átalakítások hozzá legyenek adva a ClaimsTransformations listájához. Ha nem rendelkezik a fájlban definiált **ClaimsTransformations** elemmel, adja hozzá a SZÜLŐ XML-elemeket az alább látható módon. A jogcímek átalakításához egy új, **nullStringClaim**nevű jogcím-típusra is szükség van.

Adja hozzá a **BuildingBlocks** elemet a *TrustFrameworkExtensions. XML* fájl elejéhez. Példa: *TrustFrameworkBase. XML* .

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

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most már van egy házirend konfigurálva, hogy Azure AD B2C tudja, hogyan kommunikálhat a LinkedIn-fiókjával. Próbálja meg feltölteni a szabályzat kiterjesztési fájlját annak ellenőrzéséhez, hogy az eddig nem rendelkezik problémákkal.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs vagy bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy a LinkedIn Identity Provider is legyen.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagban.
2. A `Id="SignUpOrSignIn"`tartalmazó **UserJourney** elem teljes tartalmának megkeresése és másolása.
3. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha **ClaimsProviderSelection** elemet ad hozzá egy LinkedIn-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely tartalmazza a létrehozott felhasználói út `Order="1"`.
2. A **ClaimsProviderSelections**területen adja hozzá a következő elemet. A **TargetClaimsExchangeId** értékét állítsa be a megfelelő értékre, például `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy kommunikáljon egy LinkedIn-fiókkal a jogkivonat fogadásához.

1. Keresse meg a felhasználói úton `Order="2"`t tartalmazó **OrchestrationStep** .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `LinkedIn-OAUTH`.

3. Mentse a *TrustFrameworkExtensions. XML* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

A Azure AD B2Ckel folytatott kommunikáció egy, a B2C-bérlőben regisztrált alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignInLinkedIn. XML fájlba*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInLinkedIn`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignLinkedIn).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.

## <a name="migration-from-v10-to-v20"></a>Migrálás v 1.0-ről v 2.0-ra

A LinkedIn nemrég [frissítette az API-kat 1.0-s és v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)-s verzióra. Ha a meglévő konfigurációt az új konfigurációra szeretné áttelepíteni, az alábbi részekben található információk segítségével frissítheti a technikai profil elemeit.

### <a name="replace-items-in-the-metadata"></a>A metaadatokban szereplő elemek cseréje

A **kivonatjogcím**meglévő **metaadat** elemében frissítse a következő **elem** elemeit:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Cél:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Elemek hozzáadása a metaadatokhoz

A Kivonatjogcím **metaadataiban** adjahozzá a következő **elem** elemeit:

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>A OutputClaims frissítése

A **kivonatjogcím**meglévő **OutputClaims** frissítse a következő **OutputClaim** elemeket:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Cél:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Új OutputClaimsTransformation elemek hozzáadása

A Kivonatjogcím **OutputClaimsTransformations** adja hozzáa következő **OutputClaimsTransformation** elemeket:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Az új jogcím-átalakítások és jogcím típusának megadása

Az utolsó lépésben új jogcím-átalakításokat adott hozzá, amelyeket meg kell határozni. A jogcímek átalakításának definiálásához adja hozzá őket a **ClaimsTransformations**listájához. Ha nem rendelkezik a fájlban definiált **ClaimsTransformations** elemmel, adja hozzá a SZÜLŐ XML-elemeket az alább látható módon. A jogcímek átalakításához egy új, **nullStringClaim**nevű jogcím-típusra is szükség van.

A **BuildingBlocks** elemet hozzá kell adni a fájl elejéhez. Példaként tekintse meg a *TrustframeworkBase. xml fájlt* .

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

### <a name="obtain-an-email-address"></a>E-mail-cím beszerzése

A LinkedIn 1.0-ről v 2.0-ra való áttelepítésének részeként egy másik API meghívása szükséges az e-mail cím beszerzéséhez. Ha a regisztráció során meg kell szereznie az e-mail-címet, tegye a következőket:

1. A fenti lépések végrehajtásával engedélyezheti, hogy a Azure AD B2C összevonása a LinkedIn használatával, hogy a felhasználó bejelentkezzen. Az összevonás részeként Azure AD B2C fogadja a LinkedIn hozzáférési jogkivonatát.
2. Mentse a LinkedIn hozzáférési tokent egy jogcímbe. [Tekintse meg az itt található utasításokat](idp-pass-through-custom.md).
3. Adja hozzá a következő jogcím-szolgáltatót, amely elvégzi a kérést a LinkedIn `/emailAddress` API-hoz. A kérelem engedélyezéséhez szüksége lesz a LinkedIn hozzáférési jogkivonatra.

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

4. Adja hozzá a következő előkészítési lépést a felhasználói útra, hogy az API jogcím-szolgáltató aktiválódik, amikor egy felhasználó bejelentkezik a LinkedIn használatával. Ügyeljen rá, hogy megfelelően frissítse a `Order` számot. Adja hozzá ezt a lépést közvetlenül a LinkedIn műszaki profilt kiváltó előkészítési lépés után.

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

Az e-mail-cím beszerzése a LinkedIn-ből a regisztráció során nem kötelező. Ha úgy dönt, hogy nem kívánja beolvasni az e-mailt a LinkedIn-től, de a regisztráció során egyet kér, a felhasználónak manuálisan kell megadnia az e-mail-címet és hitelesítenie.

A LinkedIn Identity providert használó szabályzat teljes mintájának megtekintéséhez tekintse meg az [egyéni házirend alapszintű csomagját](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
