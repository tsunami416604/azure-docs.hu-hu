---
title: Bejelentkezés beállítása Google-fiókkal egyéni irányelvek használatával
titleSuffix: Azure AD B2C
description: Állítsa be a bejelentkezést egy Google-fiókkal az Azure Active Directory B2C-ben egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d02c86a1ff330aa4003299e1494a164089d8470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188222"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Google-fiókkal egyéni szabályzatok használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a Google-fiókkal rendelkező felhasználók számára az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatok](custom-policy-overview.md) használatával.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az Egyéni házirendek első lépései című, [az Active Directory B2C szolgáltatásban](custom-policy-get-started.md)leírt lépéseket.
- Ha még nem rendelkezik Google-fiókkal, hozzon létre egyet [a Google-fiók létrehozása oldalon.](https://accounts.google.com/SignUp)

## <a name="register-the-application"></a>A kérelem regisztrálása

Ahhoz, hogy google-fiókból származó felhasználók számára engedélyezze a bejelentkezést, létre kell hoznia egy Google-alkalmazásprojektet.

1. Jelentkezzen be a [Google Fejlesztői konzolba](https://console.developers.google.com/) a fiók hitelesítő adataival.
2. Adja meg a **Projekt nevét**, kattintson a **Létrehozás**gombra, majd győződjön meg arról, hogy az új projektet használja.
3. Válassza a bal oldali menü **Hitelesítő adatok parancsát,** majd a **Hitelesítő adatok létrehozása > Oauth ügyfélazonosítója lehetőséget.**
4. Válassza **a Hozzájárulás konfigurálása képernyő lehetőséget.**
5. Jelöljön ki vagy adjon meg egy érvényes **e-mail címet,** adja meg a felhasználóknak megjelenített **terméknevet,** írja be `b2clogin.com` az Engedélyezett **tartományok**mezőbe, majd kattintson a Mentés **gombra.**
6. Az **Alkalmazástípusa csoportban**válassza a **Webalkalmazás**lehetőséget.
7. Adja meg az alkalmazás **nevét.**
8. Az **Engedélyezett JavaScript-eredete** `https://your-tenant-name.b2clogin.com` mezőbe írja be az Engedélyezett `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **átirányítási URI-k at,** és írja be a mezőbe a . Cserélje le a bérlő nevét a bérlő nevére. Az összes kisbetűt kell használnia a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben.
8. Kattintson **a Létrehozás gombra.**
9. Másolja az **ügyfélazonosító** és az **ügyféltitkos csoport értékeit.** Mindkettőre szüksége lesz ahhoz, hogy a Google-t identitásszolgáltatóként konfigurálja a bérlőben. Az ügyféltitkos adat fontos biztonsági hitelesítő adat.

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben korábban rögzített ügyféltitok tárolásához kell.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
5. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
6. A **Beállítások** `Manual`területen válassza a lehetőséget.
7. Adja meg a házirendkulcs **nevét.** Például: `GoogleSecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
9. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
10. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók egy Google-fiók használatával jelentkezzenek be, meg kell határoznia a fiókot olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

A Google-fiókot jogcímszolgáltatóként definiálhatja, ha hozzáadja azt a **reklamproviders** elemhez a szabályzat bővítményfájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt.*
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
3. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

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
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
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
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Állítsa **be az** alkalmazásazonosító client_id az alkalmazásregisztrációból.
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

Mostanra úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon az Azure AD-címtárral. Próbálja meg feltölteni a házirend bővítményfájlját, csak hogy megerősítse, hogy eddig nincsenek-e problémái.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
2. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs/bejelentkezési képernyők egyikén sem. Elérhetővé tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói út, majd módosítsa úgy, hogy az Azure AD-identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInGoogle`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs/bejelentkezési képernyőn. Ha egy **Google-fiókhoz hozzáad egy ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor egy felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely tartalmazza `Order="1"` a létrehozott felhasználói út.
2. A **ClaimsProviderSelects csoportban**adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `GoogleExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy Google-fiók kal egy jogkivonatot.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange-elemet,** ügyelve arra, hogy ugyanazt az azonosítót használja a **TargetClaimsExchangeId**azonosítóhoz:

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil azonosítójával. Például: `Google-OAuth`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInGoogle.xml névre.*
2. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInGoogle`.
3. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_google`
4. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a létrehozott új felhasználói út (SignUpSignGoogle) azonosítójának.
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van jelölve az **Alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**gombra kattintva.
