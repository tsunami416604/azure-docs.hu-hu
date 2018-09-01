---
title: Adja hozzá a Google + identitás-szolgáltatóként OAuth2 egyéni szabályzatok használatával az Azure Active Directory B2C |} A Microsoft Docs
description: A minta használata a Google + OAuth2 protokollt használó identitás-szolgáltatóként.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f076a906ba38e6c8e8c9530baba1607553b41ea6
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338328"
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Adja hozzá a Google + identitás-szolgáltatóként OAuth2 egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez az útmutató bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a Google +-fiókból használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

Ezeket a lépéseket tartalmazza:

1.  A Google + fiók alkalmazások létrehozásához.
2.  A Google + fiók kérelem kulcs hozzáadása az Azure AD B2C-vel
3.  Egy házirend hozzáadása jogcím-szolgáltatói
4.  A Google + fiók jogcímszolgáltató felhasználói út regisztrálása
5.  A szabályzat feltöltése egy Azure AD B2C-bérlőben és a tesztelés közben

## <a name="create-a-google-account-application"></a>A Google + fiók-alkalmazás létrehozása
A Google +-t használja Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C, kell a Google +-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. A Google + alkalmazás itt regisztrálhat: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Nyissa meg a [Google fejlesztői konzolon](https://console.developers.google.com/) , és jelentkezzen be a Google + fiók hitelesítő adatait.
2.  Kattintson a **Create project**, adjon meg egy **projektnév**, és kattintson a **létrehozása**.

3.  Kattintson a **projektek menü**.

    ![A Google +-fiók – válassza ki a projekthez](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Kattintson a **+** gombra.

    ![A Google +-fiók – új projekt létrehozása](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Adjon meg egy **projektnév**, és kattintson a **létrehozás**.

    ![A Google +-fiók – új projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Várjon, amíg készen áll a projektet, és kattintson a a **projektek menü**.

    ![A Google +-fiók – Várjon, amíg az új projekt használatra készen áll](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Kattintson a projekt nevére.

    ![A Google +-fiók – válassza ki az új projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Kattintson a **API Manager** majd **hitelesítő adatok** a bal oldali navigációs.
9.  Kattintson a **OAuth-hozzájárulási képernyő** a felső fülön.

    ![A Google +-fiók – OAuth beállítása beleegyezést kérő oldalon](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Válasszon vagy adjon meg egy érvényes **E-mail-cím**, adjon meg egy **Terméknév**, és kattintson a **mentése**.

    ![A Google + - alkalmazás hitelesítő adatok](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Kattintson a **új hitelesítő adatok** majd **OAuth-Ügyfélazonosító**.

    ![A Google + - új alkalmazások hitelesítő adatok létrehozása](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  A **alkalmazástípus**válassza **webes alkalmazás**.

    ![A Google + - alkalmazástípus kiválasztása](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Adjon meg egy **neve** adja meg az alkalmazás `https://{tenant}.b2clogin.com` a a **JavaScript engedélyezett eredetek** mező, és `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a a **jogosult átirányítási URI-k** a mező. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c). A **{tenant}** értéke a kis-és nagybetűket. Kattintson a **Create** (Létrehozás) gombra.

    ![A Google + - adja meg a JavaScript engedélyezett származási helyek és az átirányítási URI-k](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Mindkettő konfigurálásához a Google + identitás-szolgáltatóként a bérlő van szüksége. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.

    ![A Google + - ügyfél-azonosítót és a titkos kód az értékek másolásához](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Adja hozzá a Google + alkalmazás fiókkulcs Azure AD B2C-vel
Összevonás a Google + fiókok nevében az alkalmazás Azure AD B2C-vel megbízhatósági fiók Google + ügyfélkódot igényel. Az Azure AD B2C-bérlőben a Google + Alkalmazáskulcs tárolására van szüksége:  

1.  Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**
2.  Válassza ki **Szabályzatbejegyzések** elérhető a bérlői kulcsok megtekintéséhez.
3.  Kattintson a **+ Hozzáadás**.
4.  A **beállítások**, használjon **manuális**.
5.  A **neve**, használjon `GoogleSecret`.  
    Az előtag `B2C_1A_` automatikusan hozzáadhatók.
6.  Az a **titkos kulcs** adja meg a Google-alkalmazás titkos származó a [Google fejlesztői konzolon](https://console.developers.google.com/) fent kimásolt.
7.  A **kulcshasználat**, használjon **aláírás**.
8.  Kattintson a **Create** (Létrehozás) gombra
9.  Győződjön meg arról, hogy a kulcs létrehozott `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A bővítmény a házirend a jogcímeket szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be Google +-fiók használatával, definiálhatja a Google +-fiók egy jogcímszolgáltatótól szeretne. Más szóval meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít.

A Google + fiók meghatározni egy jogcímszolgáltatótól, hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend fájlban:

1.  Nyissa meg a bővítmény a házirend fájlt (TrustFrameworkExtensions.xml) a munkakönyvtár. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy könnyen használható, többplatformos szerkesztő.
2.  Keresse meg a `<ClaimsProviders>` szakasz
3.  Adja hozzá a következő XML-részletet a `ClaimsProviders` elemet, és cserélje le `client_id` érték és a Google + fiók alkalmazás ügyfél-azonosító a fájl mentése előtt.  

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

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>A Google + fiók jogcím-szolgáltató regisztrálásához való regisztráció vagy bejelentkezés a felhasználói interakciósorozat

Az identitásszolgáltató be lett állítva.  Azonban ez nem érhető összes regisztrálási-regisztrálási vagy bejelentkezési képernyőt. A Google + fiók identitásszolgáltató hozzáadása a felhasználói `SignUpOrSignIn` felhasználói interakciósorozat. Elérhető legyen, létrehozunk egy meglévő sablon felhasználói interakciósorozat másolatát.  Majd hozzáadjuk a Google + fiók identitásszolgáltató:

>[!NOTE]
>
>Ha másolja a `<UserJourneys>` elem a bővítmény fájlt (TrustFrameworkExtensions.xml) a szabályzat alapszintű fájlból, továbbléphet az ebben a szakaszban.

1.  Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
2.  Keresse meg a `<UserJourneys>` elemet, és másolja a teljes tartalmát `<UserJourneys>` csomópont.
3.  Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adjon hozzá egyet.
4.  Illessze be a teljes tartalmát `<UserJourney>` csomópont gyermekeként kimásolt a `<UserJourneys>` elemet.

### <a name="display-the-button"></a>A gomb megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját.  `<ClaimsProviderSelection>` a elem egy identity provider gombjára egy regisztrálási-regisztrálási vagy bejelentkezési oldal hasonló. Ha hozzáad egy `<ClaimsProviderSelection>` elemet a Google +-fiókhoz tartozó, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadása:

1.  Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a másolt felhasználói interakciósorozat.
2.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`
3.  Adja hozzá az alábbi kódrészletet XML `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C kommunikálni a Google + fiók kaphat jogkivonatot.

1.  Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá az alábbi kódrészletet XML `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Győződjön meg, hogy a `Id` , ugyanazzal az értékkel rendelkezik `TargetClaimsExchangeId` az előző szakaszban
> * Győződjön meg, hogy `TechnicalProfileReferenceId` azonosító értéke a technikai profil létrehozott korábbi (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>A szabályzat feltöltése a bérlőhöz
1.  Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **Azure AD B2C-vel** panelen.
2.  Válassza ki **identitás-kezelőfelületi keretrendszer**.
3.  Nyissa meg a **összes szabályzat** panelen.
4.  Válassza ki **szabályzat feltöltése**.
5.  Ellenőrizze **szabályzat felülírása, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy azt érvényesítése nem hiúsul meg a

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a Futtatás most
1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.

    >[!NOTE]
    >
    >    **Futtatás most** kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. 
    >    Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.


2.  Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.
3.  Bejelentkezhet a Google +-fiókkal kell lennie.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Opcionális] A Google + fiók jogcímszolgáltató Profilmódosítás felhasználói interakciósorozat regisztrálása
A Google + fiók identitásszolgáltató hozzáadása a felhasználói is érdemes `ProfileEdit` felhasználói interakciósorozat. Elérhető legyen, hogy az utolsó két lépést ismételje meg:

### <a name="display-the-button"></a>A gomb megjelenítése
1.  Nyissa meg a szabályzat (például TrustFrameworkExtensions.xml) a kiterjesztésű fájlt.
2.  Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"` a másolt felhasználói interakciósorozat.
3.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`
4.  Adja hozzá az alábbi kódrészletet XML `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
1.  Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá az alábbi kódrészletet XML `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>A szabályzat feltöltése a bérlőhöz
1.  Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **Azure AD B2C-vel** panelen.
2.  Válassza ki **identitás-kezelőfelületi keretrendszer**.
3.  Nyissa meg a **összes szabályzat** panelen.
4.  Válassza ki **szabályzat feltöltése**.
5.  Ellenőrizze a **szabályzat felülírása, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy azt a az érvényesítése nem hiúsul.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Az egyéni Profilmódosítás házirend tesztelése a Futtatás most

1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.
3.  Bejelentkezhet a Google +-fiókkal kell lennie.

## <a name="download-the-complete-policy-files"></a>A teljes-fájlok letöltése
Választható lehetőség: Javasoljuk, hogy a forgatókönyv a minta-fájlok használata helyett a saját egyéni házirend végig az Ismerkedés az egyéni szabályzatok befejezése után fájlok használatával fejleszt.  [A házirend mintafájlok referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
