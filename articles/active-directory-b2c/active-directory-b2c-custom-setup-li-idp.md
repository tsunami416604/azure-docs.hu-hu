---
title: Adja hozzá a LinkedIn OAuth2 identitásszolgáltatójaként az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Útmutató cikk beállításáról a LinkedIn-alkalmazás az OAuth2 protokoll és az egyéni szabályzatok használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 58a595c697b6e1a70089a6683493835e0d3a9780
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344318"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Az Azure Active Directory B2C: Egyéni szabályzatok használatával hozzáadása az identitás-szolgáltatóként LinkedIn
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a LinkedIn-fiók használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek
A lépések elvégzéséhez a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) cikk.

## <a name="step-1-create-a-linkedin-account-application"></a>1. lépés: A LinkedIn-fiók alkalmazás létrehozása
LinkedIn használatára az identitásszolgáltatójaként az Azure Active Directory B2C (Azure AD B2C-vel), létre kell hoznia egy LinkedIn-alkalmazás és adja meg azt a megfelelő paraméterekkel. A LinkedIn-alkalmazások a regisztrálhatja a [LinkedIn a regisztrációs oldalra](https://www.linkedin.com/start/join).

1. Nyissa meg a [LinkedIn Alkalmazáskezelés](https://www.linkedin.com/secure/developer?newapp=) webhelyét, jelentkezzen be a LinkedIn-fiók hitelesítő adatait, és válassza ki **alkalmazás létrehozása**.

    ![LinkedIn-fiók – alkalmazás létrehozása](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Az a **hozzon létre egy új alkalmazást** lapon, tegye a következőket:

    a. Írja be a **cégnév**, egy leíró **neve** a vállalat és a egy **leírás** az új alkalmazás.

    b. Töltse fel a **alkalmazás emblémája**.

    c. Válasszon egy **alkalmazások által használható**.

    d. Az a **webhely URL-címe** mezőbe illessze be **https://{tenant}.b2clogin.com**.  Ahol {*bérlői*} a bérlő neve (például contoso.b2clogin.com).

    e. Írja be a **üzleti E-mail** cím és **munkahelyi telefon** számát.

    f. A lap alján, olvassa el és fogadja el a használati feltételeket, és válassza ki **küldés**.

    ![LinkedIn-fiók – az alkalmazás tulajdonságainak konfigurálása](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Válassza ki **hitelesítési**, és jegyezze fel a **ügyfél-azonosító** és **titkos Ügyfélkód** értékeket.

4. Az a **átirányítási URL-címek engedélyezett** mezőbe illessze be **https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Cserélje le a(z)*bérlői*} a bérlő neve (például: contosob2c.onmicrosoft.com). Győződjön meg arról, hogy használja a HTTPS-sémát. 

    ![LinkedIn-fiók – engedélyezett Set átirányítási URL-címek](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Az ügyfél titkos kulcsát egy fontos biztonsági hitelesítő adat. Ne a titkos kulcs bárkivel megoszthatja vagy osztja el az alkalmazást.

5. Válassza a **Hozzáadás** lehetőséget.

6. Válassza ki **beállítások**, módosítsa a **az alkalmazás állapota** való **élő**, majd válassza ki **frissítés**.

    ![LinkedIn-fiók - alkalmazás állapotának beállítása](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>2. lépés: A LinkedIn alkalmazás kulcs hozzáadása az Azure AD B2C-vel
A LinkedIn-fiókokkal összevonási megbízhatósági Azure AD B2C-t az alkalmazás nevében a LinkedIn-fiókjából ügyfélkódot igényel. A LinkedIn-alkalmazás titkos kulcs tárolása az Azure AD B2C-bérlő, tegye a következőket:  

1. Válassza ki az Azure AD B2C-bérlőben **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**.

2. Elérhető a bérlőben a kulcsok megtekintéséhez jelölje ki **Szabályzatbejegyzések**.

3. Válassza a **Hozzáadás** lehetőséget.

4. Az a **beállítások** jelölje ki **feltöltése**.

5. Az a **neve** mezőbe írja be **B2cRestClientCertificate**.  
    Az előtag *B2C_1A_* automatikusan hozzáadhatók.

6. Az a **titkos** mezőbe írja be az a LinkedIn-alkalmazás titkos a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com).

7. A **kulcshasználat**válassza **titkosítási**.

8. Kattintson a **Létrehozás** gombra. 

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_LinkedInSecret`kulcsot.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>3. lépés: A bővítmény a házirend egy jogcímszolgáltatótól hozzáadása
Ha azt szeretné, hogy a felhasználók a saját LinkedIn-fiók használatával jelentkezzen be, definiálni kell LinkedIn jogcím-szolgáltatóként. Más szóval meg kell adnia a végpontok, amelyek az Azure AD B2C-vel kommunikál. A végpontok adja meg a jogcímeket, győződjön meg arról, hogy egy adott felhasználó hitelesítette az Azure AD B2C által használt.

LinkedIn meghatározni egy jogcímszolgáltatótól, adja hozzá a `<ClaimsProvider>` csomópont a bővítmény a házirend fájlban:

1. A munkakönyvtárban, nyissa meg a *TrustFrameworkExtensions.xml* bővítmény a házirend-fájl. 

2. Keresse meg a `<ClaimsProviders>` elemet.

3. Az a `<ClaimsProviders>` elemben adja hozzá a következő XML-részletet: 

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

4. Cserélje le a *client_id* érték és a LinkedIn alkalmazás ügyfél-azonosítóval.

5. Mentse a fájlt.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>4. lépés: A LinkedIn-fiók jogcímszolgáltató regisztrálása
Az identitásszolgáltató beállítása. Azonban, még nem érhető el, a regisztrálási vagy bejelentkezési windows egyikében. Most hozzá kell adnia a LinkedIn-fiók identitásszolgáltató a felhasználót `SignUpOrSignIn` felhasználói interakciósorozat.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1. lépés: Másolat készítése a felhasználói interakciósorozat
Ahhoz, hogy a felhasználói út elérhető, hozzon létre egy meglévő felhasználói interakciósorozat sablon többször, és hozzáadhatja a LinkedIn-identitásszolgáltató:

>[!NOTE]
>Ha másolja a `<UserJourneys>` elem a alap, a házirend-fájlból a *TrustFrameworkExtensions.xml* kiterjesztésű fájl, ezt a szakaszt kihagyhatja.

1. Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.

2. Keresse meg a `<UserJourneys>` elem, jelölje ki a teljes tartalmát a `<UserJourney>` csomópontot, és válassza ki **Kivágás** áthelyezése a kijelölt szöveg a vágólapra.

3. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be a teljes tartalmát a `<UserJourney>` csomópont, 2. lépésben a vágólapra történő áthelyezése a `<UserJourneys>` elemet.

### <a name="step-42-display-the-button"></a>4.2. lépés: A "gombot" megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját. A `<ClaimsProviderSelection>` csomópont hasonlatos egy regisztrálási vagy bejelentkezési oldal identity provider gombjára. Ha hozzáad egy `<ClaimsProviderSelection>` csomópont a LinkedIn-fiók, egy új gomb jelenik meg, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadásához tegye a következőket:

1. Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a másolt felhasználói interakciósorozat.

2. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`.

3. Az a `<ClaimsProviderSelections>` elemben adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>4.3. lépés: Hivatkozás egy műveletet a gomb
Most, hogy egyetlen helyen, a művelet kell kapcsolni. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz a LinkedIn-fiók kaphat jogkivonatot. A gomb összekapcsolása egy műveletet a LinkedIn-fiók jogcímszolgáltatótól összekapcsolásának a technikai profil:

1. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="2"` a a `<UserJourney>` csomópont.

2. Az a `<ClaimsExchanges>` elemben adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Ügyeljen arra, hogy `Id` , ugyanazzal az értékkel rendelkezik `TargetClaimsExchangeId` az előző szakaszban.
    >* Ügyeljen arra, hogy a `TechnicalProfileReferenceId` azonosító értékre van állítva a technikai profil korábbi (LinkedIn-OAuth) létrehozott.

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5. lépés: A szabályzat feltöltése a bérlőhöz
1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **Azure AD B2C-vel**.

2. Válassza ki **identitás-kezelőfelületi keretrendszer**.

3. Válassza ki **összes szabályzat**.

4. Válassza ki **szabályzat feltöltése**.

5. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkBase.xml* és *TrustFrameworkExtensions.xml* fájlokat, és győződjön meg arról, hogy azok megfelelnek az ellenőrzés.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6. lépés: Az egyéni házirend tesztelése a Futtatás most
1. Válassza ki **Azure AD B2C-beállítások**, majd válassza ki **identitás-kezelőfelületi keretrendszer**.

    >[!NOTE]
    >Futtatás most kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, feltöltött, és válassza ki **Futtatás most**.  
    Most már lehet a LinkedIn-fiók használatával jelentkezhet be.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>7. lépés: A LinkedIn-fiók (nem kötelező) regisztrálása jogcím-szolgáltató Profilmódosítás felhasználói interakciósorozat
A LinkedIn fiók identitásszolgáltató hozzáadása is érdemes a `ProfileEdit` felhasználói interakciósorozat. A felhasználók szállítás érhető el, ismételje meg a műveletet "4. lépés." Ezúttal válassza a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"`. Mentse, töltse fel, és tesztelje a házirendet.

## <a name="optional-download-the-complete-policy-files"></a>(Nem kötelező) A teljes-fájlok letöltése
Miután elvégezte a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
