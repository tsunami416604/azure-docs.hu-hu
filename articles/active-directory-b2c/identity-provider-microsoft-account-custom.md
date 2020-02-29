---
title: Microsoft-fiók fiókkal történő bejelentkezés beállítása egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Egyéni szabályzatok használata a Microsoft-fiók (MSA) identitás-szolgáltatóként való engedélyezéséhez az OpenID Connect (OIDC) protokoll használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188117"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Microsoft-fiók egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti a bejelentkezést egy Microsoft-fiók felhasználói számára [Egyéni szabályzatok](custom-policy-overview.md) használatával Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](custom-policy-get-started.md).
- Ha még nem rendelkezik Microsoft-fiók, hozzon létre egyet a [https://www.live.com/](https://www.live.com/).

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Ha engedélyezni szeretné a bejelentkezést a Microsoft-fiók felhasználók számára, regisztrálnia kell egy alkalmazást az Azure AD-bérlőn belül. Az Azure AD-bérlő nem azonos a Azure AD B2C Bérlővel.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *MSAapp1*.
1. A **támogatott fióktípus**területen válassza a **fiókok lehetőséget a szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)** .
1. Az **átirányítási URI (nem kötelező)** területen válassza a **web** lehetőséget, és írja be `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a szövegmezőbe. Cserélje le a `your-tenant-name`t a Azure AD B2C bérlő nevére.
1. **Regisztráció** kiválasztása
1. Jegyezze fel az alkalmazás – Áttekintés lapon megjelenő **alkalmazást (ügyfél-azonosítót)** . Erre akkor van szükség, amikor egy későbbi szakaszban konfigurálja a jogcím-szolgáltatót.
1. **Tanúsítványok kiválasztása & Secrets**
1. Kattintson az **új ügyfél titka** elemre.
1. Adja meg a titok **leírását** , például *MSA*, majd kattintson a **Hozzáadás**gombra.
1. Jegyezze fel az **érték** oszlopban látható alkalmazás jelszavát. Ezt az értéket a következő szakaszban kell használni.

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha az Azure AD-ból `family_name` és `given_name` jogcímeket szeretne beszerezni, az alkalmazáshoz választható jogcímeket konfigurálhat az Azure Portal felhasználói felületen vagy az alkalmazás jegyzékfájljában. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md).

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Keresse meg és válassza ki a **Azure Active Directory**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció (előzetes verzió)** lehetőséget.
1. Válassza a **választható jogcím hozzáadása**lehetőséget.
1. Válassza ki a konfigurálni kívánt jogkivonat-típust.
1. Válassza ki a hozzáadandó választható jogcímeket.
1. Kattintson az **Hozzáadás** parancsra.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

Most, hogy létrehozta az alkalmazást az Azure AD-bérlőben, az alkalmazás ügyfél-titkos kulcsát az Azure AD B2C-bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. A **Beállítások**területen válassza a `Manual`lehetőséget.
1. Adja meg a szabályzat kulcsának **nevét** . Például: `MSASecret`. A rendszer automatikusan hozzáadja a kulcs nevét a `B2C_1A_` előtaghoz.
1. A **Secret (titkos kulcs**) mezőben adja meg az előző szakaszban rögzített ügyfél-titkos kulcsot.
1. A **kulcshasználat**beállításnál válassza a `Signature`lehetőséget.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Annak engedélyezéséhez, hogy a felhasználók egy Microsoft-fiók használatával jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja a **ClaimsProvider** elemet a szabályzat fájlkiterjesztés-fájljához.

1. Nyissa meg a *TrustFrameworkExtensions. XML* házirend fájlját.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

1. Cserélje le **client_id** értékét a korábban rögzített Azure ad-alkalmazás *alkalmazás-azonosítójával (ügyfél)* .
1. Mentse a fájlt.

Ezzel konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a Microsoft-fiók alkalmazással az Azure AD-ben.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

A folytatás előtt töltse fel a módosított szabályzatot annak megerősítéséhez, hogy eddig nem rendelkezik problémákkal.

1. Navigáljon a Azure AD B2C-bérlőhöz a Azure Portal, és válassza az **identitási élmény keretrendszert**.
1. Az **Egyéni házirendek** lapon válassza az **egyéni házirend feltöltése**lehetőséget.
1. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
1. Kattintson a **Feltöltés** gombra.

Ha nem jelenik meg hibaüzenet a portálon, folytassa a következő szakasszal.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ekkor beállította az identitás-szolgáltatót, de még nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. Ha elérhetővé szeretné tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az a Microsoft-fiók identitás-szolgáltatóval is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagból.
1. A `Id="SignUpOrSignIn"`tartalmazó **UserJourney** elem teljes tartalmának megkeresése és másolása.
1. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
1. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInMSA`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha egy Microsoft-fiók **ClaimsProviderSelection** elemet ad hozzá, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. A *TrustFrameworkExtensions. XML* fájlban keresse meg a **OrchestrationStep** elemet, amely tartalmazza a létrehozott felhasználói útra `Order="1"`.
1. A **ClaimsProviderSelects**területen adja hozzá a következő elemet. A **TargetClaimsExchangeId** értékét állítsa be a megfelelő értékre, például `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy kommunikáljon egy Microsoft-fiók a jogkivonat fogadásához.

1. Keresse meg a felhasználói úton `Order="2"`t tartalmazó **OrchestrationStep** .
1. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét úgy, hogy az megfeleljen a korábban hozzáadott jogcím-szolgáltató **kivonatjogcím** elemének `Id` értékének. Például: `MSA-OIDC`.

1. Mentse a *TrustFrameworkExtensions. XML* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

A Azure AD B2Ckel folytatott kommunikáció egy, a B2C-bérlőben regisztrált alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignInMSA. XML fájlba*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInMSA`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például`http://contoso.com/B2C_1A_signup_signin_msa`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának (SignUpSignInMSA).
1. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
1. Győződjön meg arról, hogy az előző szakaszban létrehozott Azure AD B2C alkalmazás (vagy az előfeltételek (például *webapp1* vagy *testapp1*) kitöltése be van jelölve az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.
1. Válassza a **Microsoft-fiók** gombot, és jelentkezzen be.

    Ha a bejelentkezési művelet sikeres, a rendszer átirányítja `jwt.ms`, amely a dekódolású jogkivonatot jeleníti meg, a következőhöz hasonlóan:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
