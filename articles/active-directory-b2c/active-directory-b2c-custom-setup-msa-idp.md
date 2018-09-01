---
title: Adja hozzá a Microsoft-fiók (MSA) identitás-szolgáltatóként az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: A minta használata a Microsoft identitásszolgáltatótól az OpenID Connect (OIDC) protokoll használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6a981f112c97ee35b476c92f6f698e68a12a1363
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336821"
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Egyéni szabályzat használata Identitásszolgáltatóként, adja hozzá a Microsoft-fiók (MSA)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a Microsoft-fiókból (MSA) használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek
A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

Ezeket a lépéseket tartalmazza:

1.  Egy Microsoft-fiók alkalmazás létrehozása.
2.  A Microsoft-fiók alkalmazáskulcsot hozzáadása az Azure AD B2C-vel
3.  Egy házirend hozzáadása jogcím-szolgáltatói
4.  A felhasználói út Microsoft Account jogcímszolgáltató regisztrálása
5.  A szabályzat feltöltése egy Azure AD B2C-bérlőben és a tesztelés közben

## <a name="create-a-microsoft-account-application"></a>Hozzon létre egy Microsoft-fiók alkalmazás
Microsoft-fiók használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell hozzon létre egy Microsoft-fiók alkalmazást, és adja meg azt a megfelelő paraméterekkel. Microsoft-fiók szükséges. Ha még nincs fiókja, keresse fel [ https://www.live.com/ ](https://www.live.com/).

1.  Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) , és jelentkezzen be a Microsoft-fiók hitelesítő adatait.
2.  Kattintson a **alkalmazás hozzáadása**.

    ![A Microsoft-fiók – alkalmazás hozzáadása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Adjon meg egy **neve** az alkalmazás **kapcsolattartási e-mail cím**, törölje a jelet **segítünk használatának megkezdése** kattintson **létrehozás**.

    ![A Microsoft-fiók – az alkalmazás regisztrálása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Másolja az értéket a **alkalmazásazonosító**. Szüksége lesz rá a Microsoft-fiók beállítása lehetőséget Identitásszolgáltatóként, a bérlőben.

    ![Microsoft-fiók - példányt az alkalmazás azonosítójának értéke](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Kattintson a **Hozzáadás platform**

    ![A Microsoft-fiók - platform hozzáadása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Válassza ki a platform listából **webes**.

    ![A Microsoft-fiók – a platform listából válassza ki webes](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Adja meg `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a a **átirányítási URI-k** mező. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c).

    ![A Microsoft-fiók – Set átirányítási URL-címek](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Kattintson a **új jelszó készítése** alatt a **titkos Alkalmazáskulcsok** szakaszban. Másolja a képernyőn megjelenő új jelszót. Szüksége lesz rá a Microsoft-fiók beállítása lehetőséget Identitásszolgáltatóként, a bérlőben. Ez a jelszó, egy fontos biztonsági hitelesítő adat.

    ![A Microsoft-fiók – új jelszó létrehozása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft-fiók – az új jelszó másolása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Jelölje be a jelölőnégyzetet, arról, hogy a **Live SDK támogatás** alatt a **speciális beállítások** szakaszban. Kattintson a **Save** (Mentés) gombra.

    ![Microsoft-fiók – a Live SDK-támogatás](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Adja hozzá a Microsoft-fiók alkalmazás kulcsát az Azure AD B2C-vel
Microsoft-fiókkal rendelkező összevonási ügyfélkódot nevében az alkalmazás Azure AD B2C-vel megbízható Microsoft-fiókra van szüksége. A Microsoft-fiók alkalmazás secert tárolása az Azure AD B2C-bérlő van szüksége:   

1.  Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**
2.  Válassza ki **Szabályzatbejegyzések** elérhető a bérlői kulcsok megtekintéséhez.
3.  Kattintson a **+ Hozzáadás**.
4.  A **beállítások**, használjon **manuális**.
5.  A **neve**, használjon `MSASecret`.  
    Az előtag `B2C_1A_` automatikusan hozzáadhatók.
6.  Az a **titkos** mezőbe írja be a Microsoft-alkalmazás titkos a https://apps.dev.microsoft.com
7.  A **kulcshasználat**, használjon **aláírás**.
8.  Kattintson a **Create** (Létrehozás) gombra
9.  Győződjön meg arról, hogy a kulcs létrehozott `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A bővítmény a házirend a jogcímeket szolgáltató hozzáadása
Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be Microsoft-Account használatával, definiálhatja a Microsoft Account egy jogcímszolgáltatótól szeretne. Más szóval meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít.

A Microsoft Account meghatározni egy jogcímszolgáltatótól, hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend fájlban:

1.  Nyissa meg a bővítmény a házirend fájlt (TrustFrameworkExtensions.xml) a munkakönyvtár. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy könnyen használható, többplatformos szerkesztő.
2.  Keresse meg a `<ClaimsProviders>` szakasz
3.  Adja hozzá az alábbi XML-kódrészlet alatt a `ClaimsProviders` elem:

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

4.  Cserélje le `client_id` érték és a Microsoft Account alkalmazás ügyfél azonosítója

5.  Mentse a fájlt.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Regisztráljon a Microsoft Account jogcím-szolgáltatót, hogy jelentkezzen be, vagy jelentkezzen be a felhasználói interakciósorozat

Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrálási-regisztrálási vagy bejelentkezési képernyőt. Most adja hozzá a Microsoft Account identitásszolgáltató a felhasználónak kell `SignUpOrSignIn` felhasználói interakciósorozat. Elérhető legyen, létrehozunk egy meglévő sablon felhasználói interakciósorozat másolatát.  Majd hozzáadjuk a Microsoft Account identitásszolgáltató:

> [!NOTE]
>
>Ha korábban kimásolt a `<UserJourneys>` elem a szabályzat alapszintű fájlból a bővítmény fájl `TrustFrameworkExtensions.xml`, ezt a szakaszt kihagyhatja.

1.  Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
2.  Keresse meg a `<UserJourneys>` elemet, és másolja a teljes tartalmát `<UserJourneys>` csomópont.
3.  Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adjon hozzá egyet.
4.  Illessze be a teljes tartalmát `<UserJourneys>` csomópont gyermekeként kimásolt a `<UserJourneys>` elemet.

### <a name="display-the-button"></a>A gomb megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját.  `<ClaimsProviderSelection>` a elem egy identity provider gombjára egy regisztrálási-regisztrálási vagy bejelentkezési oldal hasonló. Ha hozzáad egy `<ClaimsProviderSelection>` elem a következő Microsoft-fiók, egy új gomb jelenik meg, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadása:

1.  Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a másolt felhasználói interakciósorozat.
2.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`
3.  Adja hozzá az alábbi kódrészletet XML `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz Microsoft Account fogadhatnak jogkivonatot. A gomb összekapcsolása egy műveletet a Microsoft Account jogcím-szolgáltatói összekapcsolásának a technikai profil:

1.  Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá az alábbi kódrészletet XML `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Győződjön meg, hogy a `Id` , ugyanazzal az értékkel rendelkezik `TargetClaimsExchangeId` az előző szakaszban
>   * Győződjön meg, hogy `TechnicalProfileReferenceId` azonosító értéke a technikai profil létrehozott korábbi (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>A szabályzat feltöltése a bérlőhöz
1.  Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **Azure AD B2C-vel** panelen.
2.  Válassza ki **identitás-kezelőfelületi keretrendszer**.
3.  Nyissa meg a **összes szabályzat** panelen.
4.  Válassza ki **szabályzat feltöltése**.
5.  Ellenőrizze **szabályzat felülírása, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy azt érvényesítése nem hiúsul meg a

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a Futtatás most

1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.
> [!NOTE]
>
>**Futtatás most** kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.
2.  Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.
3.  Jelentkezhet be Microsoft-fiókkal kell lennie.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Opcionális] Regisztráljon a Microsoft Account jogcímszolgáltató Profilmódosítás felhasználói interakciósorozat
A Microsoft Account identitásszolgáltató hozzáadása a felhasználói is érdemes `ProfileEdit` felhasználói interakciósorozat. Elérhető legyen, hogy az utolsó két lépést ismételje meg:

### <a name="display-the-button"></a>A gomb megjelenítése
1.  Nyissa meg a szabályzat (például TrustFrameworkExtensions.xml) a kiterjesztésű fájlt.
2.  Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"` a másolt felhasználói interakciósorozat.
3.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`
4.  Adja hozzá az alábbi kódrészletet XML `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
1.  Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá az alábbi kódrészletet XML `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Az egyéni Profilmódosítás házirend tesztelése a Futtatás most
1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.
3.  Jelentkezhet be Microsoft-fiókkal kell lennie.

## <a name="download-the-complete-policy-files"></a>A teljes-fájlok letöltése
Választható lehetőség: Javasoljuk, hogy a forgatókönyv a minta-fájlok használata helyett a saját egyéni házirend végig az Ismerkedés az egyéni szabályzatok befejezése után fájlok használatával fejleszt.  [A házirend mintafájlok referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
