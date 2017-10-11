---
title: "Az Azure Active Directory B2C: Egyéni házirendekkel identitás-szolgáltatóként hozzáadása a Microsoft-fiók (MSA)"
description: "A Microsoft identitás-szolgáltatóként OpenID Connect (OIDC) protokollt használó minta"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8c981046ff41d3927ff60d6dc4f40366ae25ba74
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Egyéni házirendekkel identitás-szolgáltatóként hozzáadása a Microsoft-fiók (MSA)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan használatával Bejelentkezés Microsoft-fiók (msa-t) a felhasználók engedélyezése [egyéni házirendek](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

Ezek a lépések az alábbiak:

1.  A Microsoft-fiók alkalmazás létrehozása.
2.  A Microsoft-fiók alkalmazás kulcs hozzáadása az Azure AD B2C
3.  Egy házirend hozzáadása jogcím-szolgáltató
4.  A Microsoft Account jogcímszolgáltató felhasználói út regisztrálása
5.  A házirend feltöltése az Azure AD B2C bérlői és tesztelik azt

## <a name="create-a-microsoft-account-application"></a>Microsoft-fiók alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként a Microsoft-fiók használatához szüksége hozzon létre egy Microsoft-fiók alkalmazást, és adja meg azt a megfelelő paraméterekkel. Microsoft-fiók szükséges. Ha még nincs fiókja, látogasson el [https://www.live.com/](https://www.live.com/).

1.  Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) , és jelentkezzen be Microsoft-fiók hitelesítő adataival.
2.  Kattintson a **hozzáadhat egy alkalmazást**.

    ![Microsoft fiók - alkalmazás hozzáadása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Adja meg egy **neve** az alkalmazás **lépjen kapcsolatba az e-mailek**, törölje a jelet **tudassa velünk megkezdésében súgó** kattintson **létrehozása**.

    ![Microsoft-fiók – az alkalmazás regisztrálása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Másolja a értékének **alkalmazásazonosító**. Esetleg szükség lenne rá, Microsoft-fiókok konfigurálása a bérlő az identitás-szolgáltatóként.

    ![Microsoft-fiók - példány az alkalmazás azonosítójának értéke](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Kattintson a **Hozzáadás platform**

    ![Microsoft fiók - platform hozzáadása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  A platform listából válassza ki a **webes**.

    ![Microsoft-fiók – a platform listából válassza a webes](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **átirányítási URI-azonosítók** mező. Cserélje le **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com).

    ![Microsoft-fiók - készlet átirányítási URL-címek](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Kattintson a **új jelszó létrehozása** alatt a **alkalmazás titkos kulcsok** szakasz. Másolja az új jelszót a képernyőn jelenik meg. Esetleg szükség lenne rá, Microsoft-fiókok konfigurálása a bérlő az identitás-szolgáltatóként. Ez a jelszó nem egy fontos biztonsági hitelesítő adatok.

    ![Microsoft fiók – új jelszó létrehozása](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft-fiók - példány az új jelszó](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Jelölje be a jelölőnégyzetet, amely szerint **Live SDK támogatási** alatt a **speciális beállítások** szakasz. Kattintson a **Save** (Mentés) gombra.

    ![Microsoft-fiók - Live SDK-támogatás](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>A Microsoft-fiók alkalmazás kulcs hozzáadása az Azure AD B2C
A Microsoft-fiókokkal az összevonáshoz szükség van egy ügyfélkulcsot Microsoft-fiók nevében az alkalmazás az Azure AD B2C megbízhatósági. A Microsoft-fiók alkalmazás secert tárolása az Azure AD B2C bérlő kell:   

1.  Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C beállítások** > **identitás élmény keretrendszer**
2.  Válassza ki **házirend kulcsok** érhető el a bérlői kulcsok megtekintéséhez.
3.  Kattintson a **+ Hozzáadás**.
4.  A **beállítások**, használjon **manuális**.
5.  A **neve**, használjon `MSASecret`.  
    Az előtag `B2C_1A_` előfordulhat, hogy automatikusan hozzáadja.
6.  Az a **titkos** mezőbe írja be a Microsoft alkalmazás titkos kulcs a https://apps.dev.microsoft.com
7.  A **kulcshasználat**, használjon **aláírás**.
8.  Kattintson a **Create** (Létrehozás) gombra
9.  Győződjön meg arról, hogy létrehozta a kulcs `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A jogcím-szolgáltató hozzáadása a bővítmény házirend
Ha azt szeretné, hogy a felhasználók Microsoft Account bejelentkezni, szüksége Microsoft Account megadása jogcímek-szolgáltatóként. Ez azt jelenti meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont a jogcímek, az Azure AD B2C által használt győződjön meg arról, hogy egy adott felhasználó engedélyezést biztosít.

Microsoft Account meghatározni, a Jogcímszolgáltatók hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend-fájlban:

1.  Nyissa meg a házirend bővítményfájl (TrustFrameworkExtensions.xml) a munkakönyvtárat. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy egyszerű platformfüggetlen-szerkesztőben.
2.  Keresés a `<ClaimsProviders>` szakasz
3.  Adja hozzá a következő XML-részletet alatt a `ClaimsProviders` elem:

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

4.  Cserélje le `client_id` értékének a Microsoft Account alkalmazás ügyfél-azonosítóját

5.  Mentse a fájlt.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>A Microsoft Account jogcímszolgáltató bejelentkezési mentése regisztrálásának vagy a felhasználói bejelentkezés használatában

Ezen a ponton az identitásszolgáltató be van állítva, de a sign-Close-Up/sign-in képernyők egyik nem érhető el. Most a Microsoft Account identitásszolgáltató hozzáadása a felhasználói `SignUpOrSignIn` felhasználói út. Tegye elérhetővé, azt hozzon létre egy meglévő sablon felhasználói út duplikált.  Majd azt a Microsoft Account identitásszolgáltató hozzáadása:

> [!NOTE]
>
>Ha korábban kimásolt a `<UserJourneys>` a bővítményfájl házirend alap fájlból elem `TrustFrameworkExtensions.xml`, ezt a szakaszt kihagyhatja.

1.  Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
2.  Keresés a `<UserJourneys>` elem és a teljes tartalmának másolása a `<UserJourneys>` csomópont.
3.  Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keresse a `<UserJourneys>` elemet. Ha az elem nem létezik, vegyen fel egyet.
4.  Illessze be a teljes tartalmát `<UserJournesy>` csomópont gyermekeként másolt a `<UserJourneys>` elemet.

### <a name="display-the-button"></a>A gomb megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját.  `<ClaimsProviderSelection>`a elem egy identity provider gombra a sign-Close-Up/sign-in oldalán hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` elem Microsoft-fiókot, egy új gomb megjelenik, amikor a felhasználó az oldalon fájljai. Ez az elem hozzáadása:

1.  Keresés a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a a felhasználók utazás másolt.
2.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont`Order="1"`
3.  Adja hozzá a következő XML-részletet a `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
Most, hogy a gomb helyen, hogy egy művelet kapcsolódnia kell. A művelet, ebben az esetben, akkor az Azure AD B2C kommunikálni a Microsoft Account fogadhatnak jogkivonatot. A gomb művelet mutató hivatkozás létrehozása a műszaki profil létrehozhatja, ha a Microsoft Account jogcímeket szolgáltató:

1.  Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá a következő XML-részletet a `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Győződjön meg arról a `Id` ugyanazzal az értékkel, amely rendelkezik `TargetClaimsExchangeId` az előző szakaszban leírt
>   * Győződjön meg arról `TechnicalProfileReferenceId` beállítása a műszaki profil létrehozott korábbi (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>A házirend a bérlő feltöltése
1.  A a [Azure-portálon](https://portal.azure.com), átváltani a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **az Azure AD B2C** panelen.
2.  Válassza ki **identitás élmény keretrendszer**.
3.  Nyissa meg a **házirend** panelen.
4.  Válassza ki **házirend feltöltése**.
5.  Ellenőrizze **felülírja a házirendet, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy az érvényesítése nem hiúsul meg az

## <a name="test-the-custom-policy-by-using-run-now"></a>Tesztelje az egyéni házirend segítségével futtatása most

1.  Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.
> [!NOTE]
>
>**Futtassa most** legalább egy alkalmazás a tenant preregistered lehet szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.
2.  Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.
3.  Jelentkezhet be Microsoft-fiók használatával kell lennie.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Választható] A Microsoft Account jogcímszolgáltató profil szerkesztheti a felhasználói út regisztrálása
Érdemes lehet a Microsoft Account identitásszolgáltató is hozzáadása a felhasználói `ProfileEdit` felhasználói út. Tegye elérhetővé, hogy ismételje meg az utolsó két lépést:

### <a name="display-the-button"></a>A gomb megjelenítése
1.  Nyissa meg a bővítményfájl házirend (például TrustFrameworkExtensions.xml).
2.  Keresés a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"` a a felhasználók utazás másolt.
3.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont`Order="1"`
4.  Adja hozzá a következő XML-részletet a `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
1.  Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá a következő XML-részletet a `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Tesztelje az egyéni házirend profil-módosítása segítségével futtatása most
1.  Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.
3.  Jelentkezhet be Microsoft-fiók használatával kell lennie.

## <a name="download-the-complete-policy-files"></a>A teljes házirend-fájlok letöltésére
Választható lehetőség: Javasoljuk hoz létre a minta-fájlok használata helyett a saját egyéni házirend fájlok az első lépések egyéni házirendekkel befejezése után végezze el a forgatókönyvet.  [Házirend mintafájlok referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
