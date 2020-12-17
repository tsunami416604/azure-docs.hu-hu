---
title: Regisztráció és bejelentkezés beállítása Microsoft-fiókkal
titleSuffix: Azure AD B2C
description: Az alkalmazásokban Microsoft-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával regisztrálhat és regisztrálhat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 123b36ba854bec8b363d59bbed5e70f18da1e578
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653707"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Microsoft-fiók használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Microsoft-fiók-alkalmazás létrehozása

Ha Microsoft-fiók [identitás-szolgáltatóként](openid-connect.md) szeretné használni Azure Active Directory B2C (Azure ad B2C), létre kell hoznia egy alkalmazást az Azure ad-bérlőben. Az Azure AD-bérlő nem azonos a Azure AD B2C Bérlővel. Ha még nem rendelkezik Microsoft-fiókval, beszerezhet egyet [https://www.live.com/](https://www.live.com/) .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *MSAapp1*.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár-több-bérlős) és a személyes Microsoft-fiókokat (például Skype, Xbox)**.

   A különböző fióktípus-beállításokkal kapcsolatos további információkért tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md)című témakört.
1. Az **átirányítási URI (nem kötelező)** területen válassza a **web** lehetőséget, és írja be `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` a szövegmezőbe. Cserélje le a helyére a `<tenant-name>` Azure ad B2C bérlő nevét.
1. **Regisztráció** kiválasztása
1. Jegyezze fel az alkalmazás – Áttekintés lapon megjelenő **alkalmazást (ügyfél-azonosítót)** . Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.
1. **Tanúsítványok kiválasztása & Secrets**
1. Kattintson az **Új titkos ügyfélkód** elemre.
1. Adja meg a titok **leírását** , például az *1. alkalmazás jelszava*, majd kattintson a **Hozzáadás** gombra.
1. Jegyezze fel az **érték** oszlopban látható alkalmazás jelszavát. Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a Microsoft- **fiók** lehetőséget.
1. Adjon meg egy **nevet**. Például: *MSA*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Azure ad-alkalmazás alkalmazás-(ügyfél-) azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített ügyfél-titkot.
1. Válassza a **Mentés** lehetőséget.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha a `family_name` és a `given_name` jogcímeket az Azure ad-től szeretné beszerezni, az alkalmazáshoz a Azure Portal felhasználói felületén vagy az alkalmazás-jegyzékfájlban konfigurálhat választható jogcímeket is. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció (előzetes verzió)** lehetőséget.
1. Válassza a **választható jogcím hozzáadása** lehetőséget.
1. Válassza ki a konfigurálni kívánt jogkivonat-típust.
1. Válassza ki a hozzáadandó választható jogcímeket.
1. Kattintson a **Hozzáadás** parancsra.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

Most, hogy létrehozta az alkalmazást az Azure AD-bérlőben, az alkalmazás ügyfél-titkos kulcsát az Azure AD B2C-bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `MSASecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. A **Secret (titkos kulcs**) mezőben adja meg az előző szakaszban rögzített ügyfél-titkos kulcsot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Annak engedélyezéséhez, hogy a felhasználók egy Microsoft-fiók használatával jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja a **ClaimsProvider** elemet a szabályzat fájlkiterjesztés-fájljához.

1. Nyissa meg a *TrustFrameworkExtensions.xml* házirend-fájlt.
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
            <Item Key="UsePolicyInRedirectUri">false</Item>
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
1. Az **Egyéni házirendek** lapon válassza az **egyéni házirend feltöltése** lehetőséget.
1. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.

Ha nem jelenik meg hibaüzenet a portálon, folytassa a következő szakasszal.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ekkor beállította az identitás-szolgáltatót, de még nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. Ha elérhetővé szeretné tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az a Microsoft-fiók identitás-szolgáltatóval is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
1. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
1. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
1. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInMSA`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha egy Microsoft-fiók **ClaimsProviderSelection** elemet ad hozzá, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. A *TrustFrameworkExtensions.xml* fájlban keresse meg a **OrchestrationStep** elemet, amely a `Order="1"` létrehozott felhasználói útra vonatkozik.
1. A **ClaimsProviderSelects** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `MicrosoftAccountExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy kommunikáljon egy Microsoft-fiók a jogkivonat fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
1. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét, hogy megegyezzen a `Id` korábban hozzáadott jogcím-szolgáltató **kivonatjogcím** elemében szereplő értékkel. Például: `MSA-OIDC`.

1. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Microsoft Identity Provider hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyre a Microsoft Identity providert szeretné használni.
1. A **közösségi identitás-szolgáltatók** területen válassza a **Microsoft-fiók** lehetőséget.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInMSA.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInMSA`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_msa`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának (SignUpSignInMSA).
1. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
1. Győződjön meg arról, hogy az előző szakaszban létrehozott Azure AD B2C alkalmazás (vagy az előfeltételek (például *webapp1* vagy *testapp1*) kitöltése be van jelölve az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most** lehetőségre kattintva.
1. Válassza a **Microsoft-fiók** gombot, és jelentkezzen be.

::: zone-end