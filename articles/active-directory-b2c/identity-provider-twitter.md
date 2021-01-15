---
title: Regisztráció és bejelentkezés beállítása Twitter-fiókkal
titleSuffix: Azure AD B2C
description: Az alkalmazások Twitter-fiókkal való regisztrációját és bejelentkezését Azure Active Directory B2C használatával biztosíthatja az ügyfeleknek.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f209e0ac17c62378d279a32f4a27f48a9f74bd
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232692"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Twitter-fiókkal való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Alkalmazás létrehozása

A Twitter-fiókkal rendelkező felhasználók bejelentkezésének engedélyezéséhez Azure Active Directory B2C (Azure AD B2C) alkalmazásban Twitter-alkalmazást kell létrehoznia. Ha még nem rendelkezik Twitter-fiókkal, regisztrálhat a következő címen: [https://twitter.com/signup](https://twitter.com/signup) . [Egy fejlesztői fiókra](https://developer.twitter.com/en/apply/user.html)is szüksége lesz. További információ: [Apply for Access](https://developer.twitter.com/en/apply-for-access).

1. Jelentkezzen be a [Twitter fejlesztői portálra](https://developer.twitter.com/portal/projects-and-apps) a Twitter-fiókja hitelesítő adataival.
1. Az **önálló alkalmazások** területen válassza az **+ alkalmazás létrehozása** lehetőséget.
1. Adja meg az **alkalmazás nevét**, majd kattintson a **Befejezés** gombra.
1. Másolja az **alkalmazás kulcsának** és az API- **kulcs titkának** értékét.  Mindkettőt a Twitter identitás-szolgáltatóként való konfigurálásához használja a bérlőben. 
1. **Az alkalmazás beállítása** területen válassza az **Alkalmazásbeállítások** lehetőséget.
1. A **hitelesítési beállítások** területen válassza a **Szerkesztés** lehetőséget.
    1. Jelölje be a **3 lábú OAuth engedélyezése** jelölőnégyzetet.
    1. Válassza **a kérelem e-mail címe a felhasználók számára** jelölőnégyzetet.
    1. A **visszahívási URL-címek** esetében adja meg a értéket `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Cserélje le a `your-tenant` nevet a bérlő nevére, a `your-user-flow-Id` felhasználói folyamat azonosítójával együtt. Például: `b2c_1A_signup_signin_twitter`. Az összes kisbetűs betűt kell használnia, ha a bérlő nevét és a felhasználói folyamat azonosítóját akkor is meg kell adnia, ha nagybetűvel vannak meghatározva a Azure AD B2Cban.
    1. A **webhely URL-címe** mezőbe írja be a következőt: `https://your-tenant.b2clogin.com` . Cserélje le a helyére a `your-tenant` bérlő nevét. Például: `https://contosob2c.b2clogin.com`.
    1. Adja meg a **szolgáltatási feltételek** URL-címét, például: `http://www.contoso.com/tos` . A szabályzat URL-címe egy olyan oldal, amelyet fenntart az alkalmazás használati feltételeinek megadásához.
    1. Adja meg az **adatvédelmi szabályzat** URL-címét, például: `http://www.contoso.com/privacy` . A szabályzat URL-címe olyan oldal, amelyet az alkalmazásra vonatkozó adatvédelmi információk biztosítására tart fenn.
    1. Válassza a **Mentés** lehetőséget.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A Twitter konfigurálása identitás-szolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **Twitter** lehetőséget.
1. Adjon meg egy **nevet**. Például: *Twitter*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Twitter-alkalmazás *API-kulcsát* .
1. Az **ügyfél titka** mezőben adja meg a rögzített *API-kulcs titkát* .
1. Válassza a **Mentés** lehetőséget.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Twitter-identitás szolgáltatójának hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelyhez hozzá szeretné adni a Twitter-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **Twitter** lehetőséget.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `TwitterSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Encryption` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Twitter-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A Twitter-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

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
            <Item Key="client_id">Your Twitter application API key</Item>
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

4. Cserélje le a **client_id** értékét a korábban rögzített *API-kulcs titkára* .
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a Twitter-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs vagy bejelentkezési képernyőkön nem érhető el. A szolgáltatás elérhetővé tételéhez hozzon létre egy meglévő sablon felhasználói útvonalát, majd módosítsa úgy, hogy a Twitter-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInTwitter`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha hozzáad egy **ClaimsProviderSelection** elemet egy Twitter-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `TwitterExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy egy Twitter-fiókkal kommunikáljon a jogkivonatok fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `Twitter-OAUTH1`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInTwitter.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInTwitter`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_twitter`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignTwitter).
1. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
1. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most** lehetőségre kattintva.

::: zone-end
