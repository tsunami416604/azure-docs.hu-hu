---
title: Adja hozzá a Microsoft-fiók (MSA) identitás-szolgáltatóként az Azure Active Directory B2C-vel egyéni szabályzatok használatával
description: A minta használata a Microsoft identitásszolgáltatótól az OpenID Connect (OIDC) protokoll használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654154"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Állítsa be a bejelentkezési Microsoft-fiókot az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a Microsoft-fiók használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).
- Ha még nincs Microsoft-fiókkal, hozzon létre egyet, [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Alkalmazás hozzáadása

Jelentkezzen be Microsoft-fiókkal rendelkező felhasználók engedélyezéséhez szeretne regisztrálni egy alkalmazást az Azure AD-bérlő. Az Azure AD-bérlő nem ugyanaz, mint az Azure AD B2C-bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy használja az Azure AD-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD-bérlő tartalmazó könyvtárra.
1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
1. Válassza ki **új regisztrációs**.
1. Adjon meg egy **neve** az alkalmazáshoz. Ha például *MSAapp1*.
1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok (például a Skype, Xbox, Outlook.com)** .
1. Alatt **átirányítási URI-t (nem kötelező)** válassza **webes** , és adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a szövegmezőben. Cserélje le `your-tenant-name` együtt az Azure AD B2C bérlő neve.
1. Válassza ki **regisztrálása**
1. Rekord a **Alkalmazásazonosítót (ügyfél)** látható az alkalmazás Áttekintés oldalon. Ez szükséges egy későbbi szakaszban olvashat a jogcímszolgáltató konfigurálásakor.
1. Válassza ki **tanúsítványok és titkos kulcsok**
1. Kattintson a **új titkos ügyfélkulcsot**
1. Adjon meg egy **leírás** a titkos kulcsot, például a *MSA alkalmazás titkos Ügyfélkulcsát*, és kattintson a **Hozzáadás**.
1. Jegyezze fel az alkalmazás a jelszót látható a **érték** oszlop. A következő szakaszban ezt az értéket fogja használni.

## <a name="create-a-policy-key"></a>Hozzon létre egy házirendjének kulcsa

Most, hogy létrehozta az alkalmazást az Azure AD-bérlőben, meg kell, amelyeket az alkalmazás titkos tárolása az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő tartalmazó könyvtárba. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben, és válassza ki a bérlő tartalmazó könyvtárra.
1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
1. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer**.
1. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
1. A **beállítások**, válassza a `Manual`.
1. Adjon meg egy **neve** a házirend-kulcs. Például: `MSASecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
1. A **titkos**, adja meg az ügyfél titkos kulcsát, az előző szakaszban feljegyzett.
1. A **kulcshasználat**válassza `Signature`.
1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>A jogcímeket szolgáltató hozzáadása

Ahhoz, hogy a felhasználókat, hogy jelentkezzen be Microsoft-fiókkal, egy jogcímszolgáltatótól, az Azure AD B2C-végponton keresztül kommunikálhat a fiókot kell definiálni kell. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít.

Meghatározhatja az Azure AD egy jogcímszolgáltatótól, adja hozzá a **ClaimsProvider** elem a bővítmény fájlban a szabályzat.

1. Nyissa meg a *TrustFrameworkExtensions.xml* szabályzatot tartalmazó fájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a legfelső szintű elem alatt.
1. Vegyen fel egy új **ClaimsProvider** módon:

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
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
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

1. Értékét cserélje **client_id** az Azure AD-alkalmazás az *Alkalmazásazonosítót (ügyfél)* korábban feljegyzett.
1. Mentse a fájlt.

Most már konfigurálta a szabályzatot, hogy az Azure AD B2C-vel képes kommunikálni a Microsoft-fiók alkalmazás Azure AD-ben.

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

A folytatás előtt töltse fel a módosított szabályzatot annak ellenőrzéséhez, hogy minden problémát, amennyiben nem rendelkezik.

1. Keresse meg az Azure Portalon, és válassza az Azure AD B2C-bérlőben **identitás-kezelőfelületi keretrendszer**.
1. Az a **egyéni szabályzatok** lapon jelölje be **egyéni szabályzat feltöltése**.
1. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.

Ha nem jelenik a portálon, folytassa a következő szakaszban.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató beállítása, de ez még nem áll rendelkezésre az összes regisztrációs vagy bejelentkezési képernyőt. Elérhető legyen, hozzon létre egy meglévő sablon felhasználói interakciósorozat másolatát, majd módosítsa, hogy a Microsoft-fiók identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* az alapszintű csomag fájlt.
1. Keresse meg és másolja ki a teljes tartalmát a **UserJourney** , amely tartalmazza az elem `Id="SignUpOrSignIn"`.
1. Nyissa meg a *TrustFrameworkExtensions.xml* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
1. Illessze be a teljes tartalmát a **UserJourney** gyermekeként kimásolt elem a **UserJourneys** elemet.
1. Nevezze át a felhasználói út azonosítója. Például: `SignUpSignInMSA`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **hiányzik a ClaimsProviderSelection** elem ehhez hasonló regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy **hiányzik a ClaimsProviderSelection** elem Microsoft-fiókot, egy új gomb jelenik meg, amikor egy felhasználó hajtanak végre az oldalon.

1. Az a *TrustFrameworkExtensions.xml* fájlt és keresse meg a **OrchestrationStep** , amely tartalmazza az elem `Order="1"` az Ön által létrehozott felhasználói interakciósorozat.
1. A **ClaimsProviderSelects**, adja hozzá a következő elemet. Állítsa az értékét **TargetClaimsExchangeId** egy megfelelő értéket, például a `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot Microsoft-fiókkal.

1. Keresse meg a **OrchestrationStep** tartalmazó `Order="2"` a felhasználói interakciósorozatban szereplő.
1. Adja hozzá a következő **ClaimsExchange** tétele, hogy a használt azonosító ugyanazt az értéket használja-e elem **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Frissítse az értéket a **TechnicalProfileReferenceId** úgy, hogy az a `Id` értékét a **TechnicalProfile** elem a jogcímeket szolgáltató korábban hozzáadott. Például: `MSA-OIDC`.

1. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Az Azure AD B2C-alkalmazás létrehozása

Kommunikáció az Azure AD B2C-vel hoz létre az Azure AD B2C-bérlőben található alkalmazás keresztül történik. Ez a szakasz felsorolja a nem kötelező lépések is elvégezheti egy test-alkalmazás létrehozása, ha ezt még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő tartalmazó könyvtárba. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben, és válassza ki a bérlő tartalmazó könyvtárra.
1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
1. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
1. Adja meg egy nevet az alkalmazásnak, például *testapp1*.
1. A **Web App / Web API**, jelölje be `Yes`, majd adja meg `https://jwt.ms` a a **válasz URL-cím**.
1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="update-and-test-the-relying-party-file"></a>Frissítse és a függő entitás fájl tesztelése

Frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárban, és nevezze át. Például nevezze át, hogy *SignUpSignInMSA.xml*.
1. Nyissa meg az új fájlt, és frissítse az értéket, a **PolicyId** az attribútum **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInMSA`.
1. Frissítse az értéket a **PublicPolicyUri** URI-a szabályzat. Ha például`http://contoso.com/B2C_1A_signup_signin_msa`
1. Frissítse az értéket, a **hivatkozásazonosító** attribútum **DefaultUserJourney** megfelelően korábbi (SignUpSignInMSA) létrehozott felhasználói interakciósorozat azonosítója.
1. A módosítások mentéséhez, feltöltheti a fájlt, és válassza ki az új házirendet a listában.
1. Győződjön meg arról, hogy az előző szakaszban létrehozott Azure AD B2C-alkalmazás (vagy; Ehhez hajtsa végre az előfeltételeket, például *webapp1* vagy *testapp1*) van kiválasztva a **kiválasztása alkalmazás** mezőben, majd tesztelje kattintva **Futtatás most**.
1. Válassza ki a **Microsoft Account** gombra, és jelentkezzen be.

    Ha a bejelentkezési művelet sikeres, a program átirányítja `jwt.ms` megjeleníti a dekódolt Token, hasonló:

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
