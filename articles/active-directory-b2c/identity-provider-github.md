---
title: Regisztráció és bejelentkezés beállítása GitHub-fiókkal
titleSuffix: Azure AD B2C
description: Adja meg a regisztrációt, és jelentkezzen be az ügyfeleknek a GitHub-fiókokkal az alkalmazásaiban Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 38eee59ecffa0c09403f47678e588b678e038413
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98537974"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Hozzon létre egy GitHub-fiókkal történő regisztrációt és bejelentkezést Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) GitHub-fiókkal kívánja engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [GitHub fejlesztői](https://github.com/settings/developers) portálon. További információ: OAuth- [alkalmazás létrehozása](https://docs.github.com/en/free-pro-team@latest/developers/apps/creating-an-oauth-app). Ha még nem rendelkezik GitHub-fiókkal, regisztrálhat a következő címen: [https://www.github.com/](https://www.github.com/) .

1. Jelentkezzen be a [GitHub-fejlesztőbe](https://github.com/settings/developers) a GitHub-hitelesítő adataival.
1. Válassza a **OAuth-alkalmazások** lehetőséget, majd az **új OAuth alkalmazást**.
1. Adja meg az **alkalmazás nevét** és a **Kezdőlap URL-címét**.
1. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` az **engedélyezési visszahívási URL-címet**. Cserélje le a helyére `your-tenant-name` a Azure ad B2C bérlő nevét. A bérlő nevének megadásakor használja az összes kisbetűt, még akkor is, ha a bérlőt nagybetűvel definiálják Azure AD B2Cban.
1. Kattintson az **alkalmazás regisztrálása** elemre.
1. Másolja ki az **ügyfél-azonosító** és az **ügyfél titkos kulcsának** értékeit. Mindkét eszközhöz hozzá kell adnia az identitás-szolgáltatót a bérlőhöz.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **GitHub (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *GitHub*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott GitHub-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített ügyfél-titkot.
1. Válassza a **Mentés** lehetőséget.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `GitHubSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók GitHub-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Megadhat egy GitHub-fiókot jogcím-szolgáltatóként, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAUTH2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
1. Mentse a fájlt.

### <a name="add-the-claims-transformations"></a>Jogcím-átalakítások hozzáadása

A GitHub technikai profilja megköveteli, hogy a **CreateIssuerUserId** jogcím-átalakítások hozzá legyenek adva a ClaimsTransformations listájához. Ha nem rendelkezik a fájlban definiált **ClaimsTransformations** elemmel, adja hozzá a SZÜLŐ XML-elemeket az alább látható módon. A jogcímek átalakításához egy új, **numericUserId** nevű jogcím-típusra is szükség van.

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a numericUserId jogcímet a **ClaimsSchema** elemhez.
1. Keresse meg a [ClaimsTransformations](claimstransformations.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a CreateIssuerUserId jogcím-átalakításokat a **ClaimsTransformations** elemhez.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a GitHub-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az is a GitHub-identitás szolgáltatója legyen.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInGitHub`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha egy GitHub-fiókhoz ad hozzá egy **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `GitHubExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben Azure AD B2C, hogy egy GitHub-fiókkal kommunikáljon a tokenek fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `GitHub-OAuth`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-github-identity-provider-to-a-user-flow"></a>GitHub-identitás szolgáltatójának hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a GitHub-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **GitHub** lehetőséget.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInGitHub.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInGitHub`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_github`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignGitHub).
1. Mentse a módosításokat, töltse fel a fájlt.
1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Az **alkalmazás kiválasztása** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Válassza a **Futtatás most** lehetőséget, majd a GitHub használatával jelentkezzen be a githubon, és tesztelje az egyéni szabályzatot.

::: zone-end
