---
title: Bejelentkezés beállítása Google-fiókkal egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: A bejelentkezést egyéni házirendek használatával Azure Active Directory B2C Google-fiókkal állíthatja be.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 91a55782492c1b2612652b147e0aca37941bf4db
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85388204"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Google-fiókkal egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a Google-fiókkal rendelkező felhasználók számára a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) .

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [első lépések az egyéni házirendek Active Directory B2Cban](custom-policy-get-started.md)című témakör lépéseit.
- Ha még nem rendelkezik Google-fiókkal, hozzon létre egyet a [Google-fiók létrehozása](https://accounts.google.com/SignUp)területen.

## <a name="register-the-application"></a>Az alkalmazás regisztrálása

Google-fiók felhasználói számára való bejelentkezés engedélyezéséhez létre kell hoznia egy Google-alkalmazás projektjét.

1. Jelentkezzen be a [Google fejlesztői konzolra](https://console.developers.google.com/) a fiókja hitelesítő adataival.
2. Adja meg a **projekt nevét**, kattintson a **Létrehozás**elemre, majd győződjön meg arról, hogy az új projektet használja.
3. Válassza a bal oldali menüben a **hitelesítő adatok** lehetőséget, majd válassza a **hitelesítő adatok létrehozása > OAUTH ügyfél-azonosító**lehetőséget.
4. Válassza a **beleegyezés beállítása képernyőt**.
5. Válasszon ki vagy adjon meg egy érvényes **e-mail-címet**, adja meg a felhasználók számára megjelenített **terméknév nevet** , írja be `b2clogin.com` a **jogosultsági tartományokat**, majd kattintson a **Mentés**gombra.
6. Az **alkalmazás típusa**területen válassza a **webalkalmazás**lehetőséget.
7. Adja meg az alkalmazás **nevét** .
8. A **hitelesítő JavaScript**-forrásokban írja be a `https://your-tenant-name.b2clogin.com` és a **jogosultságokat az átirányítási URI**-k mezőbe `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a-bérlő nevét a bérlő nevére. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
8. Kattintson a **Létrehozás** lehetőségre.
9. Másolja ki az **ügyfél-azonosító** és az **ügyfél titkos kulcsának**értékeit. Mindkettőre szüksége lesz a Google identitás-szolgáltatóként való konfigurálásához a bérlőben. Az ügyfél titkos kulcsa fontos biztonsági hitelesítő adat.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
6. A **Beállítások**területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `GoogleSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat**beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Google-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A Google-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

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

4. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a házirendet, hogy Azure AD B2C tudja, hogyan kommunikálhat az Azure AD-címtárral. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói útvonalról, majd módosítsa úgy, hogy az Azure AD-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInGoogle`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha hozzáad egy **ClaimsProviderSelection** elemet egy Google-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg **OrchestrationStep** az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects**területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `GoogleExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy egy Google-fiókkal kommunikáljon a jogkivonatok fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `Google-OAuth`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

A Azure AD B2Ckel folytatott kommunikáció egy, a B2C-bérlőben regisztrált alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInGoogle.xmlra *.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInGoogle`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_google`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignGoogle).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.
