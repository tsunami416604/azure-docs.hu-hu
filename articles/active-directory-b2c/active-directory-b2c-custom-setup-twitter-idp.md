---
title: Adja hozzá a Twitter OAuth1 identitásszolgáltatójaként az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Twitter használata Identitásszolgáltatóként OAuth1 protokoll használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 28679ef07c2625908f7b08f808ff49c48ddb625b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339868"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Az Azure Active Directory B2C: Adjon hozzá Twitter OAuth1 Identitásszolgáltatóként egyéni szabályzatok használatával
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára egy Twitter-fiók használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek
A lépések elvégzéséhez a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) cikk.

## <a name="step-1-create-a-twitter-account-application"></a>1. lépés: Twitter-fiók alkalmazás létrehozása
Twitter használata Identitásszolgáltatóként az Azure Active Directory B2C (Azure AD B2C-vel), létre kell hoznia egy Twitter-alkalmazás és adja meg azt a megfelelő paraméterekkel. A Twitter-alkalmazás regisztrálhatja a [Twitter a regisztrációs oldalra](https://twitter.com/signup).

1. Nyissa meg a [Twitter-fejlesztők](https://apps.twitter.com/) webhelyét, jelentkezzen be a Twitter-fiókja hitelesítő adatait, és válassza ki **új alkalmazás létrehozása**.

    ![Twitter-fiók – új alkalmazás létrehozása](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. Az a **hozzon létre egy alkalmazást** ablakban tegye a következőket:
 
    a. Írja be a **neve** és a egy **leírás** az új alkalmazás. 

    b. Az a **webhely** mezőbe illessze be **https://{tenant}.b2clogin.com**. Ahol **{tenant}** a bérlő neve (például https://contosob2c.b2clogin.com).

    c. 4. Az a **visszahívási URL-Címének**, adja meg `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp`. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c) és **{policyId}** a házirend-azonosítójú (például b2c_1_policy).  **A visszahívási URL-címet kell lennie az összes kisbetűt.** Minden szabályzat, amely a Twitter-bejelentkezés használatára egy visszahívási URL-címet hozzá kell adnia. Győződjön meg arról, hogy használja `b2clogin.com` helyett ` login.microsoftonline.com` Ha az alkalmazás használ.

    d. A lap alján, olvassa el és fogadja el a feltételeket, és válassza ki **Twitter-alkalmazás létrehozása**.

    ![Twitter-fiók – új alkalmazás hozzáadása](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. Az a **B2C bemutató** ablakban válassza **beállítások**, jelölje be a **telepítésének engedélyezése az alkalmazás segítségével jelentkezzen be Twitter** jelölőnégyzetet, majd válassza ki **Update Beállítások**.

4. Válassza ki **kulcsok és hozzáférési tokenek**, és jegyezze fel a **fogyasztói kulcs (API-kulcs)** és **fogyasztói titkos kulcs (API titkos kódot)** értékeket.

    ![Twitter-fiókunkat - alkalmazás tulajdonságainak megadása](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >A fogyasztói titkos kulcs egy fontos biztonsági hitelesítő adat. Ne a titkos kulcs bárkivel megoszthatja vagy osztja el az alkalmazást.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>2. lépés: A Twitter-fiók alkalmazás kulcsának hozzáadása az Azure AD B2C-vel
Twitter-fiókkal rendelkező összevonási a Twitter-fiók nevében az alkalmazás Azure AD B2C-vel megbízhatóságának fogyasztói titkos kulcs szükséges. A Twitter-alkalmazás fogyasztói titkos kulcs tárolása az Azure AD B2C-bérlő, tegye a következőket: 

1. Válassza ki az Azure AD B2C-bérlőben **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**.

2. Elérhető a bérlőben a kulcsok megtekintéséhez jelölje ki **Szabályzatbejegyzések**.

3. Válassza a **Hozzáadás** lehetőséget.

4. Az a **beállítások** jelölje ki **manuális**.

5. Az a **neve** jelölje ki **TwitterSecret**.  
    Az előtag *B2C_1A_* automatikusan hozzáadhatók.

6. Az a **titkos** mezőbe írja be a Microsoft-alkalmazás titkos származó a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com).

7. A **kulcshasználat**, használjon **titkosítási**.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_TwitterSecret` kulcsot.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>3. lépés: A bővítmény a házirend egy jogcímszolgáltatótól hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be a Twitter-fiókkal, definiálni kell Twitter jogcím-szolgáltatóként. Más szóval meg kell adnia a végpontok, amelyek az Azure AD B2C-vel kommunikál. A végpontok adja meg a jogcímeket, győződjön meg arról, hogy egy adott felhasználó hitelesítette az Azure AD B2C által használt.

Twitter meghatározni egy jogcímszolgáltatótól, hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend fájlban:

1. A munkakönyvtárban, nyissa meg a *TrustFrameworkExtensions.xml* bővítmény a házirend-fájl. 

2. Keresse meg a `<ClaimsProviders>` szakaszban.

3. Az a `<ClaimsProviders>` csomópont, adja hozzá a következő XML-részletet:  

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

4. Cserélje le a *client_id*"értéket a Twitter alkalmazást fogyasztói kulcsára.

5. Mentse a fájlt.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>4. lépés: Regisztráljon a Twitter fiók jogcímszolgáltatótól, a regisztrálási vagy bejelentkezési felhasználói interakciósorozat
Az identitásszolgáltató beállítása. Azonban, még nem érhető el, a regisztrálási vagy bejelentkezési windows egyikében. Most hozzá kell adnia a Twitter-fiók identitásszolgáltató a felhasználót `SignUpOrSignIn` felhasználói interakciósorozat.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1. lépés: Másolat készítése a felhasználói interakciósorozat
Ahhoz, hogy a felhasználói út érhető el, hozzon létre egy meglévő felhasználói interakciósorozat sablon többször, és hozzáadhatja a Twitter-identitásszolgáltató:

>[!NOTE]
>Ha másolja a `<UserJourneys>` elem a alap, a házirend-fájlból a *TrustFrameworkExtensions.xml* kiterjesztésű fájl, továbbléphet a következő szakaszra.

1. Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.

2. Keresse meg a `<UserJourneys>` elem, jelölje ki a teljes tartalmát a `<UserJourney>` csomópontot, és válassza ki **Kivágás** áthelyezése a kijelölt szöveg a vágólapra.

3. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keressen rá a `<UserJourneys>` elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be a teljes tartalmát a `<UserJourney>` csomópont, 2. lépésben a vágólapra történő áthelyezése a `<UserJourneys>` elemet.

### <a name="step-42-display-the-button"></a>4.2. lépés: A "gombot" megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját. A `<ClaimsProviderSelection>` csomópont hasonlatos egy regisztrálási vagy bejelentkezési oldal identity provider gombjára. Ha hozzáad egy `<ClaimsProviderSelection>` csomópont egy Twitter-fiók egy új gomb jelenik meg, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadásához tegye a következőket:

1. Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a másolt felhasználói interakciósorozat.

2. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`.

3. Az a `<ClaimsProviderSelections>` elemben adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>4.3. lépés: Hivatkozás egy műveletet a gomb
Most, hogy egyetlen helyen, a művelet kell kapcsolni. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot a Twitter-fiókjában. A gomb összekapcsolása egy műveletet a Twitter-fiók jogcímszolgáltatótól összekapcsolásának a technikai profil:

1. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="2"` a a `<UserJourney>` csomópont.
2. Az a `<ClaimsExchanges>` elemben adja hozzá a következő XML-részletet:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Ügyeljen arra, hogy `Id` , ugyanazzal az értékkel rendelkezik `TargetClaimsExchangeId` az előző szakaszban.
    >* Ügyeljen arra, hogy a `TechnicalProfileReferenceId` azonosító értékre van állítva a technikai profil korábbi (Twitter-OAUTH1) létrehozott.

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
    Most már lehet a Twitter-fiókkal jelentkezhet be.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>7. lépés: A Twitter-fiók (nem kötelező) regisztrálása jogcím-szolgáltató profilmódosítás felhasználói interakciósorozat
Előfordulhat, hogy szeretné is, a Twitter fiók identitásszolgáltató hozzáadása a `ProfileEdit` felhasználói interakciósorozat. A felhasználók szállítás érhető el, ismételje meg a műveletet "4. lépés." Ezúttal válassza a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"`. Mentse, töltse fel, és tesztelje a házirendet.


## <a name="optional-download-the-complete-policy-files"></a>(Nem kötelező) A teljes-fájlok letöltése
Miután elvégezte a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
