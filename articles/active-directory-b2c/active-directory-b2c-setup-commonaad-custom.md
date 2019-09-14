---
title: Bejelentkezés beállítása több-bérlős Azure AD-identitás-szolgáltatóhoz egyéni szabályzatok használatával Azure Active Directory B2C
description: Több-bérlős Azure AD-identitás-szolgáltató hozzáadása egyéni házirendek használatával – Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21bd9d98bc59424d05ee20d91e52c78b5b0efc4a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964482"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Több-bérlős Azure Active Directory bejelentkezésének beállítása egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti a bejelentkezést a Azure Active Directory (Azure AD) több-bérlős végpontját használó felhasználók számára a Azure AD B2C [Egyéni házirendjeivel](active-directory-b2c-overview-custom.md) . Ez lehetővé teszi, hogy több Azure AD-bérlő felhasználói jelentkezzenek be Azure AD B2C használatával, anélkül, hogy az egyes bérlők számára kellene konfigurálnia az identitás-szolgáltatót. Ezeknek a bérlőknek a vendég tagjai azonban nem tudnak **majd** bejelentkezni. Ehhez [külön kell konfigurálnia az egyes bérlőket](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Alkalmazás regisztrálása

Ha egy adott Azure AD-szervezet felhasználói számára engedélyezni szeretné a bejelentkezést, regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: `Azure AD B2C App`.
1. Válassza a fiókok lehetőséget az alkalmazás **bármely szervezeti könyvtárában** .
1. Az **átirányítási URI**esetében fogadja el a **web**értékét, és írja be az alábbi URL-címet minden kisbetűvel, ahol `your-B2C-tenant-name` a helyére a Azure ad B2C bérlő neve kerül.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Kattintson a **Register** (Regisztrálás) elemre. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza a **tanúsítványok & titkok**lehetőséget, majd válassza az **új ügyfél titka**lehetőséget.
1. Adja meg a titok **leírását** , válasszon ki egy lejáratot, majd kattintson a **Hozzáadás**gombra. Jegyezze fel a titok **értékét** egy későbbi lépésben való használatra.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott alkalmazás-kulcsot kell tárolnia.

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok**területen válassza az **identitási élmény keretrendszere**elemet.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. A **Beállítások**területen válassza `Manual`a lehetőséget.
1. Adja meg a szabályzat kulcsának **nevét** . Például: `AADAppSecret`.  A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs neveként a létrehozáskor, ezért a következő szakaszban található XML-ben szereplő hivatkozás a *B2C_1A_AADAppSecret*.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban feljegyzett ügyfél-titkot.
1. A **kulcshasználat**beállításnál válassza `Signature`a elemet.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók az Azure AD használatával jelentkezzenek be, az Azure AD-t olyan jogcím-szolgáltatóként kell megadnia, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is megadhatja, ha hozzáadja az Azure AD-t az **ClaimsProvider** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions. XML* fájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/common/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. A **ClaimsProvider** elem alatt frissítse a **tartomány** értékét egy olyan egyedi értékre, amelyet a más identitás-szolgáltatóktól való megkülönböztetéshez használhat.
1. A **kivonatjogcím** elem alatt frissítse a **DisplayName**értéket, például `Contoso Employee`:. Ez az érték jelenik meg a bejelentkezési oldalon a bejelentkezés gombon.
1. Állítsa be a **client_id** a korábban regisztrált Azure ad több-bérlős alkalmazáshoz tartozó alkalmazás-azonosítóra.
1. A **CryptographicKeys**alatt frissítse a **StorageReferenceId** értékét a korábban létrehozott házirend-kulcs nevére. Például: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Hozzáférés korlátozása

> [!NOTE]
> A `https://sts.windows.net` **ValidTokenIssuerPrefixes** értékének használata lehetővé teszi, hogy az összes Azure ad-felhasználó bejelentkezzen az alkalmazásba.

Frissítenie kell az érvényes jogkivonat-kiállítók listáját, és korlátozni kell a hozzáférést az Azure AD-bérlő azon felhasználói számára, akik be tudnak jelentkezni.

Az értékek beszerzéséhez tekintse meg az OpenID Connect Discovery metaadatait minden olyan Azure AD-bérlőhöz, amelyhez be szeretné jelentkezni a felhasználóktól. A metaadatok URL-címének formátuma hasonló `https://login.windows.net/your-tenant/.well-known/openid-configuration`a következőhöz:, ahol `your-tenant` az az Azure ad-bérlő neve. Példa:

`https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`

Hajtsa végre ezeket a lépéseket minden olyan Azure AD-bérlő esetében, amelyet a bejelentkezéshez használnia kell:

1. Nyissa meg a böngészőt, és lépjen a bérlő OpenID Connect metadata URL-címére. Keresse meg a **kiállító** objektumot, és jegyezze fel az értékét. A `https://sts.windows.net/00000000-0000-0000-0000-000000000000/`következőhöz hasonlóan kell kinéznie:.
1. Másolja és illessze be az értéket a **ValidTokenIssuerPrefixes** kulcsba. Több kiállítót vesszővel kell elválasztani. Az előző `ClaimsProvider` XML-mintában két kiállító látható.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat az Azure AD-címtárakkal. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
3. Válassza a **Feltöltés** lehetőséget.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói útvonalról, majd módosítsa úgy, hogy az Azure AD-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagból.
2. A **UserJourney** elem `Id="SignUpOrSignIn"`teljes tartalmának megkeresése és másolása.
3. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInContoso`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha hozzáad egy **ClaimsProviderSelection** elemet az Azure ad-hoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, `Order="1"` amely a *TrustFrameworkExtensions. xml fájlban*létrehozott felhasználói útra vonatkozik.
1. A **ClaimsProviderSelects**területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. Ebben az esetben a művelet Azure AD B2C az Azure AD-vel való kommunikációhoz a jogkivonat fogadása céljából. Csatolja a gombot egy művelethez az Azure AD-jogcímek szolgáltatójának technikai profiljának összekapcsolásával.

1. Keresse meg a felhasználói útra `Order="2"` kiterjedő OrchestrationStep.
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt **azonosítóhoz** :

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil **azonosítójával** . Például: `Common-AAD`.

3. Mentse a *TrustFrameworkExtensions. XML* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

A Azure AD B2Ckel folytatott kommunikáció egy, a B2C-bérlőben regisztrált alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adja meg az alkalmazás nevét, például *testapp1*.
1. A **Web App/web API**esetében válassza `Yes`a elemet, majd `https://jwt.ms` adja meg a **Válasz URL-címét**.
1. Kattintson a **Létrehozás** gombra.

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a létrehozott felhasználói utat kezdeményező függő entitás (RP) fájlját:

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignContoso. XML fájlba*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInContoso`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának. Például: *SignUpSignInContoso*.
1. Mentse a módosításokat, és töltse fel a fájlt.
1. Az **Egyéni házirendek**területen válassza ki az új szabályzatot a listában.
1. Az **alkalmazás kiválasztása** legördülő menüben válassza ki a korábban létrehozott Azure ad B2C alkalmazást. Például: *testapp1*.
1. Másolja a **Futtatás most végpontot** , és nyissa meg egy privát böngészőablakban, például: Incognito mód a Google Chrome-ban vagy egy InPrivate-ablak a Microsoft Edge-ben. A privát böngészőablak megnyitása lehetővé teszi a teljes felhasználói út tesztelését, ha nem használja a jelenleg gyorsítótárazott Azure AD-beli hitelesítő adatokat.
1. Válassza ki az Azure AD-bejelentkezés gombot, például a *contoso Employee*lehetőséget, majd adja meg egy felhasználó hitelesítő adatait az egyik Azure ad szervezeti bérlőben. A rendszer megkéri, hogy engedélyezze az alkalmazást, majd adja meg a profil adatait.

Ha a bejelentkezési folyamat sikeres, a böngésző átirányítja `https://jwt.ms`a-ra, amely megjeleníti a Azure ad B2C által visszaadott jogkivonat tartalmát.

A több-bérlős bejelentkezési képesség teszteléséhez hajtsa végre az utolsó két lépést egy másik Azure AD-bérlőt használó felhasználó hitelesítő adataival.

## <a name="next-steps"></a>További lépések

Egyéni szabályzatok használatakor előfordulhat, hogy további információra van szüksége, amikor a fejlesztés során hibaelhárítást végez a házirendben.

A problémák diagnosztizálásához ideiglenesen a "fejlesztői módba" helyezheti a szabályzatot, és összegyűjtheti a naplókat az Azure Application Insights használatával. Ismerje meg, [hogyan Azure Active Directory B2C: Naplók](active-directory-b2c-troubleshoot-custom.md)gyűjtése.
