---
title: Regisztráció és bejelentkezés beállítása egy másik Azure AD B2C bérlő Azure AD B2C fiókjából
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával a regisztrációt és a bejelentkezést az alkalmazások egy másik bérlője Azure AD B2C fiókkal rendelkező ügyfelek számára biztosíthatja.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8a0d69ea57eb5b8b2a074c37d4798a99c576ce95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538177"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Regisztráció és bejelentkezés beállítása egy másik Azure AD B2C bérlő Azure AD B2C fiókjából

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan lehet összevonást beállítani egy másik Azure AD B2C Bérlővel. Ha az alkalmazások védve vannak a Azure AD B2C, ez lehetővé teszi, hogy más Azure AD-B2C's származó felhasználók bejelentkezzenek a meglévő fiókjaikkal. A következő ábrán a felhasználók bejelentkezhetnek a *Contoso* Azure ad B2C által védett alkalmazásba a *Fabrikam* Azure ad B2C-bérlője által felügyelt fiókkal 

![Azure AD B2C összevonása egy másik Azure AD B2C Bérlővel](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

A bejelentkezés engedélyezése a felhasználók számára egy másik Azure AD B2C bérlőtől (például Fabrikam) származó fiókkal a Azure AD B2Cban (például contoso):

1. Hozzon létre egy [felhasználói folyamatot](tutorial-create-user-flows.md)vagy egy [Egyéni szabályzatot](custom-policy-get-started.md).
1. Ezután hozzon létre egy alkalmazást a Azure AD B2Cban az ebben a szakaszban ismertetett módon. 

Alkalmazás létrehozásához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a másik Azure AD B2C bérlőt tartalmazó könyvtárat használja (például Fabrikam.com).
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *ContosoApp*.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely identitás-szolgáltató vagy szervezeti címtárban (a felhasználók felhasználói folyamatokkal történő hitelesítéséhez)**.
1. Az **átirányítási URI** területen válassza a **web** lehetőséget, majd adja meg a következő URL-címet az összes kisbetűvel, ahol a `your-B2C-tenant-name` helyére a Azure ad B2C bérlő neve kerül (például contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Az engedélyek területen jelölje be a **rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez** jelölőnégyzetet.
1. Válassza a **Regisztráció** lehetőséget.
1. A **Azure ad B2C-Alkalmazásregisztrációk** lapon válassza ki a létrehozott alkalmazást, például *ContosoApp*.
1. Jegyezze fel az alkalmazás – Áttekintés lapon megjelenő **alkalmazást (ügyfél-azonosítót)** . Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.
1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **tanúsítványok & Secrets** elemet.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg az ügyfél titkos kódjának leírását a **Leírás** mezőben. Például: *clientsecret1*.
1. A **lejárat** szakaszban válassza ki azt az időtartamot, amelynek a titka érvényes, majd válassza a **Hozzáadás** lehetőséget.
1. Jegyezze fel a titkos kulcs **értékét**. Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a címtárat használja, amely tartalmazza azt a Azure AD B2C bérlőt, amelyhez konfigurálni kívánja az összevonást (például contoso). Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza (például contoso).
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider** lehetőséget.
1. Adjon meg egy **nevet**. Adja meg például a *Fabrikam* értéket.
1. A **metaadatok URL-címéhez** adja meg a következő URL-címet a `{tenant}` Azure ad B2C bérlő tartománynevének (például Fabrikam) a helyére. Cserélje le a `{policy}` -t a másik bérlőben konfigurált szabályzat nevére:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Az **ügyfél-azonosító** mezőben adja meg a korábban rögzített alkalmazás azonosítóját.
1. Az **ügyfél titkos kulcsa** mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **hatókörben** adja meg a következőt: `openid` .
1. Hagyja meg a **Válasz típusa** és a **válasz mód** alapértelmezett értékeit.
1. Választható A **tartományhoz** tartozó mutatónál adja meg a [közvetlen bejelentkezéshez](direct-signin.md#redirect-sign-in-to-a-social-provider)használni kívánt tartománynevet. Például: *fabrikam.com*.
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

A Azure AD B2C bérlőben korábban létrehozott alkalmazás-kulcsot kell tárolnia.

1. Győződjön meg arról, hogy a címtárat használja, amely tartalmazza azt a Azure AD B2C bérlőt, amelyhez konfigurálni kívánja az összevonást (például contoso). Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza (például contoso).
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `FabrikamAppSecret`.  A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs neveként a létrehozáskor, ezért a következő szakaszban található XML-ben szereplő hivatkozás a *B2C_1A_FabrikamAppSecret*.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban feljegyzett ügyfél-titkot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók a másik Azure AD B2C (Fabrikam) használatával jelentkezzenek be, meg kell adnia a többi Azure AD B2Ct jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A jogcímek szolgáltatóként való megAzure AD B2C adásához vegyen fel egy Azure AD B2C a **ClaimsProvider** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Fabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Frissítse a következő XML-elemeket a megfelelő értékkel:

    |XML-elem  |Érték  |
    |---------|---------|
    |ClaimsProvider\Domain  | A [közvetlen bejelentkezéshez](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)használt tartománynév. Adja meg a közvetlen bejelentkezéshez használni kívánt tartománynevet. Például: *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Ez az érték a bejelentkezési képernyőjén a bejelentkezés gombján jelenik meg. Például: *Fabrikam*. |
    |Metaadatok \ client_id|Az identitás-szolgáltató alkalmazás-azonosítója. Frissítse az ügyfél-azonosítót a másik Azure AD B2C bérlőben korábban létrehozott alkalmazás-AZONOSÍTÓval.|
    |Metadata\METADATA|Egy olyan URL-cím, amely az OpenID Connect Identity Provider konfigurációs dokumentumra mutat, amely az OpenID Well-known Configuration Endpoint néven is ismert. Adja meg a következő URL-címet `{tenant}` , amely a másik Azure ad B2C bérlő (Fabrikam) tartománynevét helyettesíti. Cserélje le a `{tenant}` -t a másik bérlőben konfigurált szabályzat nevére, és `{policy]` a szabályzat neve: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Frissítse a **StorageReferenceId** értékét a korábban létrehozott házirend-kulcs nevére. Például: `B2C_1A_FabrikamAppSecret`.| 
    

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a másik Azure AD B2C Bérlővel. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
1. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési oldalakon még nem érhető el. Ha elérhetővé szeretné tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az Azure AD Identity Provider is legyen:

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
1. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
1. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
1. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInFabrikam`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a regisztrációs vagy bejelentkezési oldalon található Identity Provider gombhoz. Ha Azure AD B2C **ClaimsProviderSelection** elemet ad hozzá, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely `Order="1"` a *TrustFrameworkExtensions.xmlban* létrehozott felhasználói útra vonatkozik.
1. A **ClaimsProviderSelections** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `FabrikamExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="FabrikamExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy a másik Azure AD B2C kommunikáljon a tokenek fogadásához. Csatolja a gombot egy művelethez az Azure AD B2C jogcím-szolgáltató technikai profiljának összekapcsolásával:

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
1. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt **azonosítóhoz** :

    ```xml
    <ClaimsExchange Id="FabrikamExchange" TechnicalProfileReferenceId="Fabrikam-OpenIdConnect" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil **azonosítójával** . Például: `Fabrikam-OpenIdConnect`.

1. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Azure AD B2C identitás-szolgáltató hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a Azure AD B2C identitás-szolgáltatót.
1. A **közösségi identitás-szolgáltatók** területen válassza a **Fabrikam** lehetőséget.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.
1. A regisztrációs vagy bejelentkezési oldalon válassza a *Fabrikam* lehetőséget a másik Azure ad B2C Bérlővel való bejelentkezéshez.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInFabrikam.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInFabrikam`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például: `http://contoso.com/B2C_1A_signup_signin_fabrikam`.
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának. Például: *SignUpSignInFabrikam*.
1. Mentse a módosításokat, és töltse fel a fájlt.
1. Az **Egyéni házirendek** területen válassza ki az új szabályzatot a listában.
1. Az **alkalmazás kiválasztása** legördülő menüben válassza ki a korábban létrehozott Azure ad B2C alkalmazást. Például: *testapp1*.
1. Válassza a **Futtatás most** lehetőséget 
1. A regisztrációs vagy bejelentkezési oldalon válassza a *Fabrikam* lehetőséget a másik Azure ad B2C Bérlővel való bejelentkezéshez.

::: zone-end

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [adhat át más Azure ad B2C tokent az alkalmazásnak](idp-pass-through-user-flow.md).
