---
title: Bejelentkezés beállítása Twitter-fiókkal egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Állítsa be a bejelentkezést egy Twitter-fiókkal az Azure Active Directory B2C egyéni szabályzataival.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5804ded875ef03d7ade4414eb8f08885634748dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051602"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Twitter-fiókkal egyéni szabályzatok használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a Twitter-fiók felhasználói számára az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatok](custom-policy-overview.md) használatával.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.
- Ha még nem rendelkezik Twitter-fiókkal, hozzon létre egyet a [Twitter regisztrációs oldalán.](https://twitter.com/signup)

## <a name="create-an-application"></a>Alkalmazás létrehozása

A Twitter identitásszolgáltatóként való használatához az Azure AD B2C-ben létre kell hoznia egy Twitter-alkalmazást.

1. Jelentkezzen be a [Twitter Developers](https://developer.twitter.com/en/apps) webhelyére a Twitter-fiókjának hitelesítő adataival.
2. Válassza **az Alkalmazás létrehozása**lehetőséget.
3. Adja meg **az alkalmazás nevét** és az alkalmazás **leírását.**
4. A **webhely URL-címében**adja meg a . `https://your-tenant.b2clogin.com` Cserélje `your-tenant` le a bérlő nevét. Például: `https://contosob2c.b2clogin.com`.
5. A **visszahívás url-címéhez**írja be a . `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp` Cserélje `your-tenant` le a bérlő nevét `your-policy-Id` és a szabályzat azonosítóját. Például: `b2c_1A_signup_signin_twitter`. Az összes kisbetűt kell használnia a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben.
6. A lap alján olvassa el és fogadja el a feltételeket, majd válassza a **Létrehozás gombot.**
7. Az **Alkalmazás részletei** lapon válassza a **Szerkesztés > A részletek szerkesztése**jelölőnégyzetet, jelölje be a Bejelentkezés engedélyezése a **Twitterrel**jelölőnégyzetet, majd kattintson a **Mentés gombra.**
8. Válassza ki **a kulcsok at és jogkivonatokat,** és rögzítse a consumer **API-kulcs** és a **consumer API titkos kulcs** értékeit később használni.

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben korábban rögzített titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
5. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
6. A **Beállítások** `Manual`területen válassza a lehetőséget.
7. Adja meg a házirendkulcs **nevét.** Például: `TwitterSecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
9. A Kulcs használata `Encryption` **esetén**válassza a lehetőséget.
10. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók egy Twitter-fiók használatával jelentkezzenek be, meg kell határoznia a fiókot olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

A Twitter-fiókot jogcímszolgáltatóként definiálhatja, ha hozzáadja azt a házirend bővítményfájljában lévő **ClaimsProviders** elemhez.

1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt.*
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
3. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

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
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
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

4. Cserélje le a **client_id** értékét a korábban rögzített fogyasztói kulccsal.
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

Mostanra úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon a Twitter-fiókjával. Próbálja meg feltölteni a házirend bővítményfájlját, csak hogy megerősítse, hogy eddig nincsenek-e problémái.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
2. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. A rendelkezésre álló vájkáláshoz hozzon létre egy másolatot egy meglévő sablon felhasználói útról, majd módosítsa azt úgy, hogy az is rendelkezik a Twitter identitásszolgáltatójával.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInTwitter`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs vagy bejelentkezési képernyőn. Ha Egy Twitter-fiókhoz hozzáad egy **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor egy felhasználó az oldalra ér.

1. Keresse meg a **OrchestrationStep** elemet, amely tartalmazza `Order="1"` a létrehozott felhasználói út.
2. A **ClaimsProviderSelects csoportban**adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `TwitterExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy Twitter-fiók egy jogkivonat fogadására.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange-elemet,** ügyelve arra, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil azonosítójával. Például: `Twitter-OAUTH1`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInTwitter.xml*névre.
2. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInTwitter`.
3. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignTwitter).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van jelölve az **Alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**gombra kattintva.
