---
title: 'Az Azure Active Directory B2C: Egyéni házirendek segítségével hozzáadása az OAuth2 identitás-szolgáltatóként LinkedIn'
description: Egy útmutató cikk LinkedIn-alkalmazás beállítása az OAuth2 protokollt és egyéni házirendek
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.openlocfilehash: 9e5235739abcfbee1e530685890098a45a1b093d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Az Azure Active Directory B2C: Egyéni házirendek segítségével hozzáadni az identitás-szolgáltatóként LinkedIn
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezze a bejelentkezés egy LinkedIn-fiók felhasználói [egyéni házirendek](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

## <a name="step-1-create-a-linkedin-account-application"></a>1. lépés: A LinkedIn-fiók alkalmazás létrehozása
LinkedIn használata az Azure Active Directory B2C identitás-szolgáltatóként (az Azure AD B2C), létre kell hoznia egy LinkedIn-alkalmazást és adja meg azt a megfelelő paraméterekkel. Egy LinkedIn-alkalmazást a regisztrálhatja a [LinkedIn regisztrációs oldalon](https://LinkedIn.com/signup).

1. Lépjen a [LinkedIn Alkalmazáskezelés](https://www.linkedin.com/secure/developer?newapp=) webhelyét, jelentkezzen be a LinkedIn-fiók hitelesítő adataival, majd válassza ki **alkalmazás létrehozása**.

    ![LinkedIn fiók - alkalmazás létrehozása](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Az a **hozzon létre egy új alkalmazást** lapján tegye a következőket:

    a. Típus a **vállalatnév**, egy leíró **neve** a vállalat és a **leírás** az új alkalmazás.

    b. Töltse fel a **alkalmazás embléma**.

    c. Válasszon egy **alkalmazás használata**.

    d. Az a **webhely URL-címe** mezőbe illessze be **https://login.microsoftonline.com**.

    e. Típus a **üzleti E-mail** cím és **munkahelyi telefon** számát.

    f. A lap alján, olvassa el és fogadja el a használati feltételeket, majd válassza ki **Submit**.

    ![LinkedIn fiók - alkalmazás tulajdonságainak konfigurálása](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Válassza ki **hitelesítési**, és jegyezze fel a **ügyfél-azonosító** és **Ügyfélkulcs** értékeket.

4. Az a **átirányítási URL-t engedélyezett** mezőbe illessze be **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Cserélje le {*bérlői*}, a bérlő neve (például contosob2c.onmicrosoft.com). Győződjön meg arról, hogy a HTTPS protokollt használ. 

    ![Fiók LinkedIn - készlet engedélyezett átirányítási URL-címek](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >A titkos ügyfélkulcs egy fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel ezt a titkos kulcsot, és eloszthatják azt az alkalmazással.

5. Válassza a **Hozzáadás** lehetőséget.

6. Válassza ki **beállítások**, módosítása a **alkalmazás állapotának** való **Live**, majd válassza ki **frissítés**.

    ![LinkedIn fiók - alkalmazás állapotának beállítása](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>2. lépés: A LinkedIn alkalmazás kulcs hozzáadása az Azure AD B2C
A LinkedIn-fiókokkal az összevonáshoz szükség van egy ügyfélkulcsot a LinkedIn-fiók nevében az alkalmazás az Azure AD B2C megbízhatóságának. Az Azure AD B2C-bérlő a LinkedIn alkalmazás titkos kulcs tárolása, tegye a következőket:  

1. Válassza ki az Azure AD B2C-bérlő **B2C beállítások** > **identitás élmény keretrendszer**.

2. Válassza ki, ha elérhetők a bérlői kulcsok **házirend kulcsok**.

3. Válassza a **Hozzáadás** lehetőséget.

4. Az a **beállítások** mezőben válassza **feltöltése**.

5. Az a **neve** mezőbe írja be **B2cRestClientCertificate**.  
    Az előtag *B2C_1A_* előfordulhat, hogy automatikusan hozzáadja.

6. Az a **titkos** mezőbe írja be a LinkedIn alkalmazáskulcsot a a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com).

7. A **kulcshasználat**, jelölje be **titkosítási**.

8. Kattintson a **Létrehozás** gombra. 

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_LinkedInSecret`kulcs.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>3. lépés: A jogcím-szolgáltató hozzáadása a bővítmény házirend
Ha azt szeretné, hogy a felhasználók által a LinkedIn-fiókkal bejelentkezni, meg kell adnia LinkedIn jogcím-szolgáltatóként. Ez azt jelenti meg kell adnia a végpontok, amelyek az Azure AD B2C-vel kommunikál. A végpontok Azure AD B2C használt győződjön meg arról, hogy egy adott felhasználó hitelesítette jogcímek egy készletét adja meg.

LinkedIn meghatározni egy jogcímszolgáltatótól, adja hozzá a `<ClaimsProvider>` csomópont a bővítmény a házirend-fájlban:

1. A munkakönyvtár, nyissa meg a *TrustFrameworkExtensions.xml* bővítményfájl házirend. 

2. Keresse meg a `<ClaimsProviders>` elemet.

3. Az a `<ClaimsProviders>` elemet, adja hozzá a következő XML-részletet: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Cserélje le a *client_id* érték és a LinkedIn alkalmazás ügyfél-azonosítót.

5. Mentse a fájlt.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>4. lépés: A LinkedIn fiók jogcím-szolgáltató regisztrálása
Az identitásszolgáltató beállítása. Azonban ez még nem érhető el a regisztráció vagy bejelentkezés windows valamelyikében. Most hozzá kell adnia a felhasználói fiók LinkedIn identitásszolgáltató `SignUpOrSignIn` felhasználói út.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1. lépés: Másolat készítése a felhasználó út
A felhasználók utazás elérhetővé, hozzon létre egy meglévő felhasználó út sablon duplikált, és adja hozzá a LinkedIn identitásszolgáltató:

>[!NOTE]
>Ha a másolt a `<UserJourneys>` elem a következő alap fájl a házirend a *TrustFrameworkExtensions.xml* bővítményfájl, kihagyhatja ezt a részt.

1. Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.

2. Keresse meg a `<UserJourneys>` elem, jelölje be a teljes tartalmát a `<UserJourney>` csomópont, és válassza **Kivágás** helyezhető át a kijelölt szöveg a vágólapra.

3. Nyissa meg a bővítményfájl (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be a teljes tartalmát a `<UserJourney>` csomópont, amely történő áthelyezése a 2. lépésben a vágólapra a `<UserJourneys>` elemet.

### <a name="step-42-display-the-button"></a>4.2. lépés: A "gomb" megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját. A `<ClaimsProviderSelection>` csomópont egy identitás szolgáltató gomb regisztráció vagy bejelentkezés lapon hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` csomópont LinkedIn-fiók esetén egy új gomb akkor látható, ha a felhasználó az oldalon fájljai. Ez az elem hozzáadásához tegye a következőket:

1. Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a a felhasználók utazás másolt.

2. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`.

3. Az a `<ClaimsProviderSelections>` elemet, adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>4.3. lépés: Hivatkozás a gombra kattintva egy műveletet
Most, hogy a gomb helyen, akkor egy műveletet kell kapcsolni. A művelet, ebben az esetben, akkor az Azure AD B2C fogadhatnak jogkivonatot a LinkedIn fiók kommunikálni. A gomb művelet mutató hivatkozás létrehozása a műszaki profil létrehozhatja a LinkedIn fiók jogcímeket szolgáltató:

1. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="2"` a a `<UserJourney>` csomópont.

2. Az a `<ClaimsExchanges>` elemet, adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Győződjön meg arról, hogy `Id` ugyanazzal az értékkel, amely rendelkezik `TargetClaimsExchangeId` az előző szakaszban leírt.
    >* Győződjön meg arról, hogy a `TechnicalProfileReferenceId` ID értéke a műszaki profil létrehozott korábbi (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5. lépés: Töltse fel a házirend a bérlő
1. A a [Azure-portálon](https://portal.azure.com), váltson a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **az Azure AD B2C**.

2. Válassza ki **identitás élmény keretrendszer**.

3. Válassza ki **házirend**.

4. Válassza ki **házirend feltöltése**.

5. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkBase.xml* és *TrustFrameworkExtensions.xml* fájlokat, és győződjön meg arról, hogy ezek a csomópontok visszaküldik érvényesítése.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6. lépés: Az egyéni házirend tesztelése Futtatás most használatával
1. Válassza ki **az Azure AD B2C beállítások**, majd válassza ki **identitás élmény keretrendszer**.

    >[!NOTE]
    >Futtatás most a tenant preregistered kell legalább egy alkalmazás szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött, és válassza **futtatása most**.  
    Most kell a LinkedIn fiókkal jelentkezhet be.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>7. lépés: A LinkedIn-fiók (nem kötelező) regisztrálása jogcím-szolgáltatót, amelyet a felhasználói profil-Szerkesztés út
Előfordulhat, hogy szeretné hozzáadni a LinkedIn fiók identitásszolgáltató a `ProfileEdit` felhasználói út. Ahhoz, hogy a felhasználó szállítás rendelkezésre álló, ismétlődő "4. lépés." Most, jelölje be a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"`. Mentse, töltse fel, és tesztelje a házirendet.

## <a name="optional-download-the-complete-policy-files"></a>(Választható) A teljes házirend-fájlok letöltésére
Miután elvégezte a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
