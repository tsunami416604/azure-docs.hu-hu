---
title: Bejelentkezés beállítása Amazon-fiókkal egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Állítsa be a bejelentkezést egy Amazon-fiókkal az Azure Active Directory B2C-ben egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2de891ee109677f92ff603759701f7732f5951ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188511"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Amazon-fiókkal egyéni szabályzatok használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a felhasználók számára egy Amazon-fiók ból [egyéni szabályzatok](custom-policy-overview.md) használatával az Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md)
- Ha még nem rendelkezik Amazon-fiókkal, [https://www.amazon.com/](https://www.amazon.com/)hozzon létre egyet a .

## <a name="register-the-application"></a>A kérelem regisztrálása

Ahhoz, hogy engedélyezze a bejelentkezést a felhasználók számára egy Amazon-fiókból, létre kell hoznia egy Amazon-alkalmazást.

1. Jelentkezzen be az [Amazon Developer Centerbe](https://login.amazon.com/) amazonos fiókjának hitelesítő adataival.
2. Ha még nem tette meg, kattintson a **Regisztráció**gombra, kövesse a fejlesztői regisztrációs lépéseket, és fogadja el a szabályzatot.
3. Válassza **az Új alkalmazás regisztrálása**lehetőséget.
4. Adja meg **a név,** **a leírás**és **az adatvédelmi nyilatkozat URL-címét,** majd kattintson a **Mentés gombra.** Az adatvédelmi nyilatkozat egy ön által kezelt oldal, amely adatvédelmi információkat biztosít a felhasználók számára.
5. A **Webbeállítások** csoportban másolja az **Ügyfélazonosító**értékét. Válassza **a Titkos titok megjelenítése** lehetőséget az ügyféltitok leéséhez, majd másolja azt. Mindkettőre szüksége van egy Amazon-fiók konfigurálásához a bérlőidentitás-szolgáltatóként. **Az ügyféltitkos adat** fontos biztonsági hitelesítő adat.
6. A **Webes beállítások** csoportban válassza a `https://your-tenant-name.b2clogin.com` **Szerkesztés**lehetőséget, majd `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` írja be az **Engedélyezett JavaScript-eredetek** és az Engedélyezett **visszatérési URL-címek mezőbe.** Cserélje `your-tenant-name` le a bérlő nevét. Használja az összes kisbetűt a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben.
7. Kattintson a **Mentés** gombra.

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben korábban rögzített ügyféltitok tárolásához kell.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
5. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
6. A **Beállítások** `Manual`területen válassza a lehetőséget.
7. Adja meg a házirendkulcs **nevét.** Például: `AmazonSecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
9. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
10. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók egy Amazon-fiók használatával jelentkezzenek be, meg kell határoznia a fiókot olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

Az Amazon-fiókot jogcímszolgáltatóként definiálhatja, ha hozzáadja azt a házirend bővítményfájljában található **ClaimsProviders** elemhez.


1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt.*
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
3. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
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

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs/bejelentkezési képernyők egyikén sem. Ahhoz, hogy elérhetővé tegye, hozzon létre egy másolatot egy meglévő sablon felhasználói útról, majd módosítsa azt úgy, hogy az Amazon identitásszolgáltatóval is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInAmazon`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs/bejelentkezési képernyőn. Ha egy Amazon-fiókhoz ad hozzá **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor egy felhasználó az oldalra ér.

1. Keresse meg a **OrchestrationStep** elemet, amely tartalmazza `Order="1"` a létrehozott felhasználói út.
2. A **ClaimsProviderSelects csoportban**adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `AmazonExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy Amazon-fiók egy jogkivonat fogadására.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange-elemet,** ügyelve arra, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil azonosítójával. Például: `Amazon-OAuth`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInAmazon.xml*névre.
2. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInAmazon`.
3. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a létrehozott új felhasználói út (SignUpSignAmazon) azonosítójának.
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van jelölve az **Alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**gombra kattintva.
