---
title: Regisztráció és bejelentkezés beállítása WeChat-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával WeChat-fiókkal rendelkező ügyfelek számára biztosítson regisztrációt és bejelentkezést az alkalmazásokban.
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
ms.openlocfilehash: 1dd9af3abd723b3a7adad82f3bbb4a09fc4464f7
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538015"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>WeChat-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C


[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-wechat-application"></a>WeChat-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) WeChat-fiókkal rendelkező felhasználók számára szeretné engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [WeChat felügyeleti központban](https://open.weixin.qq.com/). Ha még nem rendelkezik WeChat-fiókkal, a következő címen kérhet információt: [https://kf.qq.com](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>WeChat-alkalmazás regisztrálása

1. Jelentkezzen be a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) WeChat hitelesítő adataival.
1. Válassza a **管理中心** (felügyeleti központ) lehetőséget.
1. Új alkalmazás regisztrálásához kövesse az alábbi lépéseket.
1. Adja meg `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **授权回调域** (visszahívási URL-cím). Ha például a bérlő neve contoso, állítsa be az URL-címet a következőre: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Másolja az **alkalmazás azonosítóját** és az **alkalmazás kulcsát**. Ezekre szüksége lesz az identitás-szolgáltatónak a bérlőhöz való hozzáadásához.

::: zone pivot="b2c-user-flow"

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>A WeChat konfigurálása identitás-szolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **WeChat (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *WeChat*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott WeChat-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titkos** kulcsa mezőben adja meg a rögzített alkalmazás kulcsát.
1. Válassza a **Mentés** lehetőséget.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `WeChatSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók WeChat-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A WeChat-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat fájlkiterjesztés fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>wechat.com</Domain>
      <DisplayName>WeChat (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="WeChat-OAUTH">
          <DisplayName>WeChat</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">wechat</Item>
            <Item Key="authorization_endpoint">https://open.weixin.qq.com/connect/qrconnect</Item>
            <Item Key="AccessTokenEndpoint">https://api.weixin.qq.com/sns/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weixin.qq.com/sns/userinfo</Item>
            <Item Key="scope">snsapi_login</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="AccessTokenResponseFormat">json</Item>
            <Item Key="ClientIdParamName">appid</Item>
            <Item Key="ClientSecretParamName">secret</Item>
            <Item Key="ExtraParamsInAccessTokenEndpointResponse">openid</Item>
            <Item Key="ExtraParamsInClaimsEndpointRequest">openid</Item>
            <Item Key="ResponseErrorCodeParamName">errcode</Item>
            <Item Key="external_user_identity_claim_id">unionid</Item>
          <Item Key="client_id">Your WeChat application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeChatSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="unionid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="wechat.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a WeChat-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az a WeChat-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInWeChat`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha hozzáad egy **ClaimsProviderSelection** elemet egy WeChat-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `WeChatExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="WeChatExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. Ebben az esetben a művelet arra vonatkozik, hogy a Azure AD B2C kommunikáljon egy WeChat-fiókkal a jogkivonat fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="WeChatExchange" TechnicalProfileReferenceId="WeChat-OAuth" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `WeChat-OAuth`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-wechat-identity-provider-to-a-user-flow"></a>WeChat-identitás szolgáltatójának hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a WeChat-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **WeChat** lehetőséget.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInWeChat.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInWeChat`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_WeChat`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignWeChat).
1. Mentse a módosításokat, töltse fel a fájlt.
1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Az **alkalmazás kiválasztása** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** lehetőségre, és válassza a WeChat lehetőséget a WeChat való bejelentkezéshez és az egyéni szabályzat teszteléséhez.

::: zone-end
