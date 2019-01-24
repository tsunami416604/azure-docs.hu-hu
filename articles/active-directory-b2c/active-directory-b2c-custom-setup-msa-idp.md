---
title: Adja hozzá a Microsoft-fiók (MSA) identitás-szolgáltatóként az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: A minta használata a Microsoft identitásszolgáltatótól az OpenID Connect (OIDC) protokoll használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d377b3f6dda864e9ab18dd716127ef6a6bed27c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845594"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Állítsa be a bejelentkezési Microsoft-fiókot az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a Microsoft-fiók használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).
- Ha még nincs Microsoft-fiókkal, hozzon létre egyet, [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Alkalmazás hozzáadása

Identitás-szolgáltatóként az Azure AD B2C egy Microsoft-fiók használatához hozzá kell egy Microsoft-fiók alkalmazást.

1. Jelentkezzen be a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) Microsoft-fiókja hitelesítő adataival.
2. A jobb felső sarokban válassza **alkalmazás hozzáadása**.
3. Adjon meg egy **alkalmazásnév**, és kattintson a **létrehozása** 
4. Válassza ki **új jelszó készítése** , és győződjön meg arról, hogy másolja az identitásszolgáltató konfigurálásakor használt jelszót. Emellett másolja a **alkalmazásazonosító**. 
5. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **átirányítási URL-ek**. Cserélje le `your-tenant-name` a bérlő nevével.
6. Kattintson a **Mentés** gombra.

## <a name="create-a-policy-key"></a>Hozzon létre egy házirendjének kulcsa

A jelszó jön létre és az Azure AD B2C-bérlő a korábban feljegyzett tárolásához szüksége.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer – előzetes verzió**.
5. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
6. A **beállítások**, válassza a `Manual`.
7. Adjon meg egy **neve** a házirend-kulcs. Például: `MSASecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
8. A **titkos**, adja meg a korábban feljegyzett jelszót.
9. A **kulcshasználat**válassza `Signature`.
10. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>A jogcímeket szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Microsoft-fiókkal bejelentkezni, határozza meg a fiók, amely az Azure AD B2C-végponton keresztül kommunikálhat egy jogcímszolgáltatótól szeretne. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít. 

Meghatározhatja az Azure AD egy jogcímszolgáltatótól, adja hozzá a **ClaimsProvider** elem a bővítmény fájlban a szabályzat.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a legfelső szintű elem alatt.
3. Vegyen fel egy új **ClaimsProvider** módon:

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
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
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

4.  Cserélje le a értékét **client_id** korábban feljegyzett Alkalmazásazonosítóval rendelkező.
5.  Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

Már konfigurálta a szabályzatot, hogy az Azure AD B2C-vel képes kommunikálni a Microsoft-fiókjával. A szabályzat csak, győződjön meg arról, hogy minden problémát, amennyiben nem rendelkezik a bővítmény fájlt töltsön fel.

1. Az a **egyéni szabályzatok** az Azure AD B2C-bérlő, válassza a lap **szabályzat feltöltése**.
2. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrációs vagy bejelentkezési képernyőt. Elérhető legyen, hozzon létre egy meglévő sablon felhasználói interakciósorozat másolatát, és ezután módosítsa, hogy a Microsoft-fiók identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* az alapszintű csomag fájlt.
2. Keresse meg és másolja ki a teljes tartalmát a **UserJourney** , amely tartalmazza az elem `Id="SignUpOrSignIn"`.
3. Nyissa meg a *TrustFrameworkExtensions.xml* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a teljes tartalmát a **UserJourney** gyermekeként kimásolt elem a **UserJourneys** elemet.
5. Nevezze át a felhasználói út azonosítója. Például: `SignUpSignInMSA`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **hiányzik a ClaimsProviderSelection** elem ehhez hasonló regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy **hiányzik a ClaimsProviderSelection** elem Microsoft-fiókot, egy új gomb jelenik meg, amikor egy felhasználó hajtanak végre az oldalon.

1. Az a *TrustFrameworkExtensions.xml* fájlt és keresse meg a **OrchestrationStep** , amely tartalmazza az elem `Order="1"` az Ön által létrehozott felhasználói interakciósorozat.
2. A **ClaimsProviderSelects**, adja hozzá a következő elemet. Állítsa az értékét **TargetClaimsExchangeId** egy megfelelő értéket, például a `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A műveletet, ebben az esetben az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot Twitter-fiókja van.

1. Keresse meg a **OrchestrationStep** tartalmazó `Order="2"` a felhasználói interakciósorozatban szereplő.
2. Adja hozzá a következő **ClaimsExchange** gondoskodik róla, hogy ugyanazt az értéket használt elem **azonosító** során használt **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    Frissítse az értéket a **TechnicalProfileReferenceId** , a **azonosító** a korábban létrehozott technikai profil. Például: `MSA-OIDC`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Az Azure AD B2C-alkalmazás létrehozása

Kommunikáció az Azure AD B2C-vel hoz létre a bérlő alkalmazás keresztül történik. Ez a szakasz felsorolja a nem kötelező lépések is elvégezheti egy test-alkalmazás létrehozása, ha ezt még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adja meg egy nevet az alkalmazásnak, például *testapp1*.
6. A **Web App / Web API**, jelölje be `Yes`, majd adja meg `https://jwt.ms` a a **válasz URL-cím**.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="update-and-test-the-relying-party-file"></a>Frissítse és a függő entitás fájl tesztelése

Frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárban, és nevezze át. Például nevezze át, hogy *SignUpSignInMSA.xml*.
2. Nyissa meg az új fájlt, és frissítse az értéket, a **PolicyId** az attribútum **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInMSA`.
3. Frissítse az értéket a **PublicPolicyUri** URI-a szabályzat. Ha például`http://contoso.com/B2C_1A_signup_signin_msa`
4. Frissítse az értéket, a **hivatkozásazonosító** attribútum **DefaultUserJourney** megfelelően (SignUpSignInMSA) létrehozott új felhasználói interakciósorozat azonosítója.
5. A módosítások mentéséhez, feltöltheti a fájlt, és válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C-alkalmazást az van kiválasztva, a **válassza ki az alkalmazás** mezőben, majd tesztelje kattintva **Futtatás most**.
