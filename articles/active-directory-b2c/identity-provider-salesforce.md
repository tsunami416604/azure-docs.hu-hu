---
title: Regisztráció és bejelentkezés beállítása Salesforce-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával Salesforce-fiókkal rendelkező ügyfelek számára biztosítson regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 129809a83bcebdcf80b05a7300dd9acf862e5886
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900399"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Salesforce-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Salesforce-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) Salesforce-fiókot szeretne használni, létre kell hoznia egy alkalmazást a Salesforce **app Managerben**. További információ: az [alapszintű csatlakoztatott alkalmazások beállításainak konfigurálása](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)és [az OAuth-beállítások engedélyezése az API-integrációhoz](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Jelentkezzen be a Salesforce](https://login.salesforce.com/).
1. A menüben válassza a **telepítés** lehetőséget.
1.  Bontsa ki az **alkalmazások** csomópontot, majd válassza az **app Manager** lehetőséget.
1. Válassza az **új csatlakoztatott alkalmazás** lehetőséget.
1. Az **alapszintű információ** területen adja meg a következőket:
    1. **Csatlakoztatott alkalmazás neve** – a csatlakoztatott alkalmazás neve megjelenik az App Managerben és az App Launcher csempén. A névnek egyedinek kell lennie a szervezeten belül. 
    1. **API neve** 
    1. **Kapcsolattartási E-mail cím** – a Salesforce tartozó kapcsolattartási e-mail cím
1. Az **API (OAuth-beállítások engedélyezése)** területen válassza a **OAuth-beállítások engedélyezése** lehetőséget.
    1. A **visszahívási URL-cím** mezőbe írja be a értéket `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a helyére a `your-tenant-name` bérlő nevét. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
    1. A **kiválasztott OAuth-hatókörökben** válassza **a hozzáférés az alapvető információkhoz (azonosító, profil, e-mail cím, telefonszám)** lehetőséget, és **engedélyezze az egyedi azonosítóhoz (OpenID) való hozzáférést**.
    1. Válassza **a titkos kód megkövetelése webkiszolgálói folyamathoz** lehetőséget.
1. Adja meg a **configure ID tokent** 
    1. Állítsa a **jogkivonatot** 5 percig érvényes értékre.
    1. Válassza a **standard jogcímek belefoglalása** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. Másolja a **fogyasztói kulcs** és a **fogyasztói titok** értékeit. Mindkettőre szüksége lesz, hogy konfigurálja a Salesforce identitás-szolgáltatóként a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-salesforce-account-as-an-identity-provider"></a>Salesforce-fiók konfigurálása identitás-szolgáltatóként

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider** lehetőséget.
1. Adjon meg egy **nevet**. Írja be például a következőt: *Salesforce*.
1. A **metaadatok URL-címe** mezőben adja meg az [Salesforce OpenID Connect konfigurációs dokumentum](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)URL-címét. A homokozóban a login.salesforce.com helyére a test.salesforce.com kerül. Egy Közösség esetében a login.salesforce.com helyére a közösségi URL-cím kerül, például a username.force.com/.well-known/openid-configuration. Az URL-címnek HTTPS-nek kell lennie.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Az **ügyfél-azonosító** mezőben adja meg a korábban rögzített alkalmazás azonosítóját.
1. Az **ügyfél titkos kulcsa** mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **hatókörben** adja meg a következőt: `openid id profile email` .
1. Hagyja meg a **Válasz típusa** és a **válasz mód** alapértelmezett értékeit.
1. Választható A **tartományhoz tartozó tipp** esetében adja meg a következőt: `contoso.com` . További információ: [közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Az **Identity Provider jogcímek leképezése** területen válassza ki a következő jogcímeket:

    - **Felhasználói azonosító**: *Sub*
    - **Megjelenítendő név**: *név*
    - **Utónév**: *given_name*
    - **Vezetéknév**: *family_name*
    - **E-mail**: *e-mail*

1. Válassza a **Mentés** lehetőséget.
::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `SalesforceSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Salesforce-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A Salesforce-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat fájlkiterjesztés fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OIDC">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. A **metaadatok** a [Salesforce OpenID Connect konfigurációs dokumentum](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)URL-címére vannak beállítva. A homokozóban a login.salesforce.com helyére a test.salesforce.com kerül. Egy Közösség esetében a login.salesforce.com helyére a közösségi URL-cím kerül, például a username.force.com/.well-known/openid-configuration. Az URL-címnek HTTPS-nek kell lennie.
5. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
6. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a Salesforce-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az a Salesforce-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha hozzáad egy **ClaimsProviderSelection** elemet egy Salesforce-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `SalesforceExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. Ebben az esetben a művelet arra vonatkozik, hogy a Azure AD B2C kommunikáljon egy Salesforce-fiókkal a jogkivonat fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OIDC" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `Salesforce-OIDC`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Salesforce-identitás szolgáltatójának hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyet a Salesforce-identitás szolgáltatója szeretne.
1. A **közösségi identitás-szolgáltatók** területen válassza a **Salesforce** lehetőséget.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInSalesforce.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInSalesforce`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_Salesforce`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignSalesforce).
1. Mentse a módosításokat, töltse fel a fájlt.
1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Az **alkalmazás kiválasztása** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** lehetőségre, és válassza a Salesforce lehetőséget a Salesforce való bejelentkezéshez és az egyéni szabályzat teszteléséhez.

::: zone-end

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [adhat át Salesforce tokent az alkalmazásnak](idp-pass-through-user-flow.md).
