---
title: "Az Azure Active Directory B2C: Hozzáadása Twitter OAuth1 identitás-szolgáltatóként használatával egyéni házirendek"
description: "Használja a Twitter identitás-szolgáltatóként a OAuth1 protokoll használatával"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: 629e0bbaa7c62ef5d381085588c6a99c203c41cb
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Az Azure Active Directory B2C: Hozzáadása Twitter OAuth1 identitás-szolgáltatóként használatával egyéni házirendek
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezze a bejelentkezhet Twitter-fiók felhasználói [egyéni házirendek](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

## <a name="step-1-create-a-twitter-account-application"></a>1. lépés: Egy Twitter-fiók alkalmazás létrehozása
Twitter használata az Azure Active Directory B2C identitás-szolgáltatóként (az Azure AD B2C), létre kell hoznia egy Twitter-alkalmazást és adja meg azt a megfelelő paraméterekkel. Egy Twitter-alkalmazást a regisztrálhatja a [Twitter-előfizetés lapján](https://twitter.com/signup).

1. Lépjen a [Twitter fejlesztők](https://apps.twitter.com/) webhelyét, jelentkezzen be Twitter-fiók hitelesítő adataival, majd válassza ki **új alkalmazás létrehozása**.

    ![Twitter-fiók – új alkalmazás létrehozása](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. Az a **hozzon létre egy alkalmazást** ablakban tegye a következőket:
 
    a. Típus a **neve** és egy **leírás** az új alkalmazás. 

    b. Az a **webhely** mezőbe illessze be  **https://login.microsoftonline.com** . 

    c. Az a **visszahívási URL-cím** mezőbe illessze be  **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp** . Cserélje le {*bérlői*}, a bérlő neve (például contosob2c.onmicrosoft.com). Győződjön meg arról, hogy a HTTPS protokollt használ. 

    d. A lap alján, olvassa el és fogadja el a feltételeket, majd válassza ki **az Twitter-alkalmazás létrehozása**.

    ![Twitter-fiók – egy új alkalmazás felvétele](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. Az a **B2C bemutató** ablakban válassza ki **beállítások**, jelölje be a **teszik lehetővé az alkalmazás aláírásához twitterrel** jelölje be a jelölőnégyzetet, majd válassza ki **frissítés Beállítások**.

4. Válassza ki **kulcsok és a hozzáférési jogkivonatok**, és jegyezze fel a **(API-kulcs) kulcsa** és **felhasználói titok (API titkos)** értékeket.

    ![Twitter-fiók - alkalmazás tulajdonságainak beállítása](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >A felhasználó titkos kulcsot egy fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel ezt a titkos kulcsot, és eloszthatják azt az alkalmazással.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>2. lépés: A Twitter-fiók alkalmazás kulcs hozzáadása az Azure AD B2C
Az összevonáshoz Twitter-fiókkal rendelkező felhasználó titkos kulcs a Twitter-fiók nevében az alkalmazás az Azure AD B2C megbízhatóságának szükség van. A Twitter-alkalmazásának felhasználói titkos kulcs tárolása az Azure AD B2C-bérlő, tegye a következőket: 

1. Válassza ki az Azure AD B2C-bérlő **B2C beállítások** > **identitás élmény keretrendszer**.

2. Válassza ki, ha elérhetők a bérlői kulcsok **házirend kulcsok**.

3. Válassza a **Hozzáadás** lehetőséget.

4. Az a **beállítások** mezőben válassza **manuális**.

5. Az a **neve** mezőben válassza **TwitterSecret**.  
    Az előtag *B2C_1A_* előfordulhat, hogy automatikusan hozzáadja.

6. Az a **titkos** mezőbe írja be a Microsoft alkalmazás titkos kulcs a a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com).

7. A **kulcshasználat**, használjon **titkosítási**.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_TwitterSecret` kulcs.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>3. lépés: A jogcím-szolgáltató hozzáadása a bővítmény házirend

Ha azt szeretné, hogy a felhasználók Twitter-fiókkal bejelentkezni, meg kell adnia Twitter jogcím-szolgáltatóként. Ez azt jelenti meg kell adnia a végpontok, amelyek az Azure AD B2C-vel kommunikál. A végpontok Azure AD B2C használt győződjön meg arról, hogy egy adott felhasználó hitelesítette jogcímek egy készletét adja meg.

Twitter meghatározni, a Jogcímszolgáltatók hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend-fájlban:

1. A munkakönyvtár, nyissa meg a *TrustFrameworkExtensions.xml* bővítményfájl házirend. 

2. Keresse meg a `<ClaimsProviders>` szakasz.

3. Az a `<ClaimsProviders>` csomópont, a következő XML-részletet hozzáadása:  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4. Cserélje le a *client_id*"azonosító, a Twitter alkalmazás fogyasztói kulcsára.

5. Mentse a fájlt.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>4. lépés: Regisztrálja a Twitter fiók jogcímszolgáltató a felhasználói regisztráció vagy bejelentkezés használatában
Az identitásszolgáltató beállítása. Azonban ez még nem érhető el a regisztráció vagy bejelentkezés windows valamelyikében. Most a Twitter-fiók identitásszolgáltató hozzá kell adnia a felhasználói `SignUpOrSignIn` felhasználói út.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1. lépés: Másolat készítése a felhasználó út
A felhasználó út elérhetővé tegye, hozzon létre egy meglévő felhasználó út sablon duplikált, és adja hozzá a Twitter identitásszolgáltató:

>[!NOTE]
>Ha a másolt a `<UserJourneys>` elem a következő alap fájl a házirend a *TrustFrameworkExtensions.xml* bővítményfájl, ugorjon a következő szakaszban.

1. Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.

2. Keresse meg a `<UserJourneys>` elem, jelölje be a teljes tartalmát a `<UserJourney>` csomópont, és válassza **Kivágás** helyezhető át a kijelölt szöveg a vágólapra.

3. Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keressen a `<UserJourneys>` elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be a teljes tartalmát a `<UserJourney>` csomópont, amely történő áthelyezése a 2. lépésben a vágólapra a `<UserJourneys>` elemet.

### <a name="step-42-display-the-button"></a>4.2. lépés: A "gomb" megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját. A `<ClaimsProviderSelection>` csomópont egy identitás szolgáltató gomb regisztráció vagy bejelentkezés lapon hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` Twitter-fiók, egy új gomb-csomópontja látható, amikor a felhasználó az oldalon fájljai. Ez az elem hozzáadásához tegye a következőket:

1. Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a a felhasználók utazás másolt.

2. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`.

3. Az a `<ClaimsProviderSelections>` elemet, adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>4.3. lépés: Hivatkozás a gombra kattintva egy műveletet
Most, hogy a gomb helyen, akkor egy műveletet kell kapcsolni. A művelet, ebben az esetben, akkor az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot a Twitter-fiók. A gomb művelet mutató hivatkozás létrehozása a műszaki profil létrehozhatja, a Twitter-fiók jogcímeket szolgáltató:

1. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="2"` a a `<UserJourney>` csomópont.
2. Az a `<ClaimsExchanges>` elemet, adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Győződjön meg arról, hogy `Id` ugyanazzal az értékkel, amely rendelkezik `TargetClaimsExchangeId` az előző szakaszban leírt.
    >* Győződjön meg arról, hogy a `TechnicalProfileReferenceId` ID értéke a műszaki profil létrehozott korábbi (Twitter-OAUTH1).

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
    Most kell használva jelentkezhet be a Twitter-fiók használatával.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>7. lépés: A Twitter-fiók (nem kötelező) regisztrálása jogcím-szolgáltatót, amelyet a felhasználói profil-Szerkesztés út
Előfordulhat, hogy szeretné hozzáadni a Twitter fiók identitásszolgáltató a `ProfileEdit` felhasználói út. Ahhoz, hogy a felhasználó szállítás rendelkezésre álló, ismétlődő "4. lépés." Most, jelölje be a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"`. Mentse, töltse fel, és tesztelje a házirendet.


## <a name="optional-download-the-complete-policy-files"></a>(Választható) A teljes házirend-fájlok letöltésére
Miután elvégezte a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
