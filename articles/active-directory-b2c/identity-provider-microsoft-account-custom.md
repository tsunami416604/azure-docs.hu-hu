---
title: Bejelentkezés beállítása Microsoft-fiókkal egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Egyéni házirendek használata a Microsoft-fiók (MSA) identitásszolgáltatóként való engedélyezéséhez az OpenID Connect (OIDC) protokoll használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188117"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Microsoft-fiókkal egyéni szabályzatok használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a microsoftos fiókból származó felhasználók bejelentkezését az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatok](custom-policy-overview.md) használatával.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.
- Ha még nem rendelkezik Microsoft-fiókkal, [https://www.live.com/](https://www.live.com/)hozzon létre egyet a alkalmazásban.

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

A Microsoft-fiókkal rendelkező felhasználók bejelentkezésének engedélyezéséhez regisztrálnia kell egy alkalmazást az Azure AD-bérlőn belül. Az Azure AD-bérlő nem ugyanaz, mint az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás **nevét.** Például *MSAapp1*.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox, Outlook.com).**
1. Az **Uri átirányítása (nem kötelező)** csoportban válassza a **Web** lehetőséget, és írja be `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a szövegmezőbe. Cserélje `your-tenant-name` le az Azure AD B2C bérlői nevét.
1. **Regisztráció** kiválasztása
1. Rögzítse az alkalmazás áttekintése lapon megjelenő **alkalmazásazonosítót.** Erre akkor van szüksége, ha egy későbbi szakaszban konfigurálja a jogcímszolgáltatót.
1. Jelölje be **a tanúsítványok & titkos kulcsok at**
1. Kattintson **az Új ügyféltitok gombra.**
1. Adja meg a titkos **kulcsot,** például *az MSA alkalmazásügyfél-titkos kulcsot,* majd kattintson a **Hozzáadás gombra.**
1. Rögzítse az Alkalmazás jelszavát az **Érték** oszlopban. Ezt az értéket a következő szakaszban használhatja.

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha azt szeretné, `family_name` `given_name` hogy a és a jogcímek az Azure AD-ből, konfigurálhatja az alkalmazás választható jogcímeket az Azure Portal felhasználói felületén vagy az alkalmazásjegyzékben. További információ: [Választható jogcímek biztosítása az Azure AD-alkalmazáshoz.](../active-directory/develop/active-directory-optional-claims.md)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** szakaszban válassza **az Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez a nem kötelező jogcímeket konfigurálni szeretné a listában.
1. A **Kezelés** szakaszban válassza **a Token konfigurációja (előzetes verzió)** lehetőséget.
1. Válassza **a Választható jogcím hozzáadása**lehetőséget.
1. Válassza ki a konfigurálni kívánt tokentípust.
1. Válassza ki a hozzáadni kívánt választható jogcímeket.
1. Kattintson a **Hozzáadás** gombra.

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Most, hogy létrehozta az alkalmazást az Azure AD-bérlőben, az alkalmazás ügyféltitkát az Azure AD B2C-bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
1. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Manual`területen válassza a lehetőséget.
1. Adja meg a házirendkulcs **nevét.** Például: `MSASecret`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
1. A **Titkos**mezőbe írja be az előző szakaszban rögzített ügyféltitkos kulcsot.
1. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
1. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ahhoz, hogy a felhasználók bejelentkezhessenek egy Microsoft-fiók használatával, meg kell határoznia a fiókot jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

Az Azure AD-t jogcímszolgáltatóként definiálhatja a **Jogcímszolgáltató** elem hozzáadásával a szabályzat bővítményfájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml* házirendfájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
1. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

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
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Cserélje le a **client_id** értékét az Azure AD-alkalmazás *alkalmazás (ügyfél) azonosítójára,* amelyet korábban rögzített.
1. Mentse a fájlt.

Most úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon a Microsoft-fiókalkalmazással az Azure AD-ben.

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

A folytatás előtt töltse fel a módosított szabályzatot annak megerősítéséhez, hogy eddig nincsenek-e problémái.

1. Keresse meg az Azure AD B2C-bérlőt az Azure Portalon, és válassza **az Identity Experience Framework lehetőséget.**
1. Az **Egyéni házirendek** lapon válassza az **Egyéni házirend feltöltése**lehetőséget.
1. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.

Ha nem jelennek meg hibák a portálon, folytassa a következő szakasszal.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton beállította az identitásszolgáltatót, de az még nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. Elérhetővé tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói út, majd módosítsa azt úgy, hogy az is a Microsoft-fiók identitás-szolgáltató.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
1. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
1. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInMSA`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs vagy bejelentkezési képernyőn. Ha Egy **Microsoft-fiókhoz hozzáad egy ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. A *TrustFrameworkExtensions.xml* fájlban keresse meg a `Order="1"` **OrchestrationStep** elemet, amely tartalmazza a létrehozott felhasználói út.
1. A **ClaimsProviderSelects csoportban**adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `MicrosoftAccountExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy Microsoft-fiók egy jogkivonat fogadására.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
1. Adja hozzá a következő **ClaimsExchange-elemet,** ügyelve arra, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét, hogy `Id` megfeleljen a korábban hozzáadott jogcímszolgáltató **TechnicalProfile** elemében szereplő értékértéknek. Például: `MSA-OIDC`.

1. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInMSA.xml*fájlra.
1. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInMSA`.
1. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_msa`
1. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy az megfeleljen a korábban létrehozott felhasználói út (SignUpSignInMSA) azonosítójának.
1. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
1. Győződjön meg arról, hogy az előző szakaszban létrehozott Azure AD B2C alkalmazás (vagy az előfeltételek, például *webapp1* vagy *testapp1)* beállítás sal van kiválasztva az **Alkalmazás kiválasztása** mezőben, majd tesztelje azt a **Futtatás gombra**kattintva.
1. Válassza a **Microsoft-fiók gombot,** és jelentkezzen be.

    Ha a bejelentkezési művelet sikeres, a parancs `jwt.ms` átirányítja, amely a dekódolt jogkivonatot jeleníti meg, hasonlóan a következőkhöz:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
