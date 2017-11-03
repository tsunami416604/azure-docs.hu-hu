---
title: "Az Azure Active Directory B2C: Hozzáadása Google + identitás-szolgáltatóként OAuth2 egyéni házirendekkel"
description: "Google + identitás-szolgáltatóként OAuth2 protokollt használó mintákkal"
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
ms.openlocfilehash: 0d84dde1f70023abcfd0c15f5425d3cbaeb8c765
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Hozzáadása Google + identitás-szolgáltatóként OAuth2 egyéni házirendekkel

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez az útmutató bemutatja, hogyan bejelentkezés Google + fiókból keresztül a felhasználók engedélyezése [egyéni házirendek](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

Ezek a lépések az alábbiak:

1.  A Google + fiók alkalmazás létrehozása.
2.  A Google + alkalmazás fiókkulcs felvétele az Azure AD B2C
3.  Egy házirend hozzáadása jogcím-szolgáltató
4.  A Google + fiók jogcímszolgáltató felhasználói út regisztrálása
5.  A házirend feltöltése az Azure AD B2C bérlői és tesztelik azt

## <a name="create-a-google-account-application"></a>Google + fiók-alkalmazás létrehozása
Használatához Google + az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként kell Google +-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. A Google + alkalmazás Itt regisztrálhatja: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Lépjen a [Google fejlesztői konzol](https://console.developers.google.com/) , és jelentkezzen be Google + fiók hitelesítő adataival.
2.  Kattintson a **Create project**, adjon meg egy **projekt neve**, és kattintson a **létrehozása**.

3.  Kattintson a **projektek menü**.

    ![Google + fiók – válassza ki a projekt](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Kattintson a  **+**  gombra.

    ![Google + fiók – új projekt létrehozása](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Adjon meg egy **projektnevet**, és kattintson a **létrehozása**.

    ![Google + fiók – új projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Várjon, amíg készen áll a projektet, majd kattintson a a **projektek menü**.

    ![Google + fiók - Várjon, amíg az új projekt használatra készen áll](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Kattintson a a projekt nevét.

    ![Google + fiók – válassza ki az új projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Kattintson a **API Manager** majd **hitelesítő adatok** a bal oldali navigációs.
9.  Kattintson a **OAuth-hozzájárulás képernyő** fülre az oldal tetején.

    ![Google + fiók - beállítása OAuth hozzájárulási képernyő](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Válassza ki vagy adjon meg érvényes **E-mail cím**, adjon meg egy **Terméknév**, és kattintson **mentése**.

    ![Google + - alkalmazás hitelesítő adatait](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Kattintson a **új hitelesítő adatok** majd **OAuth-Ügyfélazonosító**.

    ![Google + - hozható létre új alkalmazás hitelesítő adatait](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  A **alkalmazástípus**, jelölje be **webes alkalmazás**.

    ![Google + - alkalmazástípus kiválasztása](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Adjon meg egy **neve** adja meg az alkalmazás `https://login.microsoftonline.com` a a **JavaScript engedélyezett eredeteket** mező, és `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **jogosult átirányítási URI-azonosítók** a mező. Cserélje le **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com). A **{tenant}** érték kis-és nagybetűket. Kattintson a **Create** (Létrehozás) gombra.

    ![Google + - JavaScript engedélyezett eredetet adjon meg, és az átirányítási URI](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Értékeinek másolására **ügyfél-azonosító** és **ügyfélkulcs**. Mindkettő konfigurálásához Google +-bérlőben identitás-szolgáltatóként van szüksége. **Titkos ügyfélkulcsot** van egy fontos biztonsági hitelesítő adatok.

    ![Google + - azonosító és az ügyfél ügyfélkulcs értékeinek másolására](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>A Google + alkalmazás fiókkulcs hozzáadása az Azure AD B2C
A Google +-fiókokkal összevonási szükség van egy ügyfélkulcsot Google + fiók nevében az alkalmazás az Azure AD B2C megbízhatóságának. A Google + alkalmazás titkos kulcs tárolása az Azure AD B2C bérlő kell:  

1.  Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C beállítások** > **identitás élmény keretrendszer**
2.  Válassza ki **házirend kulcsok** érhető el a bérlői kulcsok megtekintéséhez.
3.  Kattintson a **+ Hozzáadás**.
4.  A **beállítások**, használjon **manuális**.
5.  A **neve**, használjon `GoogleSecret`.  
    Az előtag `B2C_1A_` előfordulhat, hogy automatikusan hozzáadja.
6.  Az a **titkos** mezőbe írja be a Google alkalmazáskulcsot a a [Google fejlesztői konzol](https://console.developers.google.com/) fent másolt.
7.  A **kulcshasználat**, használjon **aláírás**.
8.  Kattintson a **Create** (Létrehozás) gombra
9.  Győződjön meg arról, hogy létrehozta a kulcs `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A jogcím-szolgáltató hozzáadása a bővítmény házirend

Ha azt szeretné, hogy a felhasználók a Google + fiók használatával jelentkezzen be, meg kell Google + fiók megadása jogcímek szolgáltatóként. Ez azt jelenti meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont a jogcímek, az Azure AD B2C által használt győződjön meg arról, hogy egy adott felhasználó engedélyezést biztosít.

Google + fiók megadása egy jogcímszolgáltatótól, mint hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend-fájlban:

1.  Nyissa meg a házirend bővítményfájl (TrustFrameworkExtensions.xml) a munkakönyvtárat. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy egyszerű platformfüggetlen-szerkesztőben.
2.  Keresés a `<ClaimsProviders>` szakasz
3.  Adja hozzá a következő XML-részletet alatt a `ClaimsProviders` elem és a név felülírandó `client_id` érték és a Google + fiók alkalmazás ügyfél-azonosító a fájl mentése előtt.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>A Google + fiók jogcímszolgáltató való regisztráció vagy bejelentkezés felhasználói út regisztrálása

Az identitásszolgáltató beállítása.  Azonban nincs sem a sign-Close-Up/sign-in képernyők áll rendelkezésre. A Google + fiók identitásszolgáltató hozzáadása a felhasználói `SignUpOrSignIn` felhasználói út. Tegye elérhetővé, azt hozzon létre egy meglévő sablon felhasználói út duplikált.  Azt adja a Google + fiók identitásszolgáltató:

>[!NOTE]
>
>Ha a másolt a `<UserJourneys>` elem a bővítményfájl (TrustFrameworkExtensions.xml) a szabályzat alapszintű fájlból, kihagyhatja ezt a szakaszt.

1.  Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
2.  Keresés a `<UserJourneys>` elem és a teljes tartalmának másolása a `<UserJourneys>` csomópont.
3.  Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keresse a `<UserJourneys>` elemet. Ha az elem nem létezik, vegyen fel egyet.
4.  Illessze be a teljes tartalmát `<UserJournesy>` csomópont gyermekeként másolt a `<UserJourneys>` elemet.

### <a name="display-the-button"></a>A gomb megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját.  `<ClaimsProviderSelection>`a elem egy identity provider gombra a sign-Close-Up/sign-in oldalán hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` elem Google + fiókhoz, egy új gomb megjelenik, amikor egy felhasználó fájljai az oldalon. Ez az elem hozzáadása:

1.  Keresés a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a a felhasználók utazás másolt.
2.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont`Order="1"`
3.  Adja hozzá a következő XML-részletet a `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
Most, hogy a gomb helyen, hogy egy művelet kapcsolódnia kell. A műveleti fiók Google + fogadhatnak jogkivonatot kommunikálni az Azure AD B2C ebben az esetben szolgál.

1.  Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá a következő XML-részletet a `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Győződjön meg arról a `Id` ugyanazzal az értékkel, amely rendelkezik `TargetClaimsExchangeId` az előző szakaszban leírt
> * Győződjön meg arról `TechnicalProfileReferenceId` beállítása a műszaki profil létrehozott korábbi (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>A házirend a bérlő feltöltése
1.  A a [Azure-portálon](https://portal.azure.com), átváltani a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **az Azure AD B2C** panelen.
2.  Válassza ki **identitás élmény keretrendszer**.
3.  Nyissa meg a **házirend** panelen.
4.  Válassza ki **házirend feltöltése**.
5.  Ellenőrizze **felülírja a házirendet, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy az érvényesítése nem hiúsul meg az

## <a name="test-the-custom-policy-by-using-run-now"></a>Tesztelje az egyéni házirend segítségével futtatása most
1.  Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.

    >[!NOTE]
    >
    >    **Futtassa most** legalább egy alkalmazás a tenant preregistered lehet szükséges. 
    >    Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.


2.  Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.
3.  Meg kell tudni jelentkezzen be Google + fiók használatával.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Választható] A Google + fiók jogcímszolgáltató profil szerkesztheti a felhasználói út regisztrálása
Érdemes lehet a Google + fiók identitásszolgáltató is hozzáadása a felhasználói `ProfileEdit` felhasználói út. Tegye elérhetővé, hogy ismételje meg az utolsó két lépést:

### <a name="display-the-button"></a>A gomb megjelenítése
1.  Nyissa meg a bővítményfájl házirend (például TrustFrameworkExtensions.xml).
2.  Keresés a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"` a a felhasználók utazás másolt.
3.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont`Order="1"`
4.  Adja hozzá a következő XML-részletet a `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
1.  Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá a következő XML-részletet a `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Tesztelje az egyéni házirend profil-módosítása segítségével futtatása most

1.  Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.
3.  Meg kell tudni jelentkezzen be Google + fiók használatával.

## <a name="download-the-complete-policy-files"></a>A teljes házirend-fájlok letöltésére
Választható lehetőség: Javasoljuk hoz létre a minta-fájlok használata helyett a saját egyéni házirend fájlok az első lépések egyéni házirendekkel befejezése után végezze el a forgatókönyvet.  [Házirend mintafájlok referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
