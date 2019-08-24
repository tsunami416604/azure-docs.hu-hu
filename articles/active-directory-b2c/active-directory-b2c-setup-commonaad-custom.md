---
title: Bejelentkezés beállítása több-bérlős Azure AD-identitáshoz a Azure Active Directory B2C egyéni szabályzatok használatával | Microsoft Docs
description: Több-bérlős Azure AD-identitás-szolgáltató hozzáadása egyéni házirendek használatával – Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8524eb8f9a8d220964e5dd1f6f8dc6d1aaf94a6d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980715"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Több-bérlős Azure Active Directory bejelentkezésének beállítása egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti a bejelentkezést a Azure Active Directory (Azure AD) több-bérlős végpontját használó felhasználók számára a Azure AD B2C [Egyéni házirendjeivel](active-directory-b2c-overview-custom.md) . Ez lehetővé teszi, hogy több Azure AD-bérlő felhasználói jelentkezzenek be Azure AD B2Cra anélkül, hogy az egyes bérlők számára műszaki szolgáltatót kellene konfigurálni. Ezeknek a bérlőknek a vendég tagjai azonban nem tudnak **majd** bejelentkezni. Ehhez külön kell konfigurálnia az [egyes bérlőket](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com`a szervezeti Azure ad-bérlőhöz használatos `fabrikamb2c.onmicrosoft.com` , és az alábbi utasításokban Azure ad B2C bérlőként szolgál.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Alkalmazás regisztrálása

Ha egy adott Azure AD-szervezet felhasználói számára engedélyezni szeretné a bejelentkezést, regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja a szervezeti Azure AD-bérlőt (contoso.com) tartalmazó könyvtárat a felső menüben található **címtár és előfizetés szűrőre** kattintva, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **Új alkalmazás regisztrálása** elemet.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Az **alkalmazás típusa**beállításnál válassza `Web app / API`a elemet.
7. A **bejelentkezési URL-cím**mezőbe írja be az alábbi URL-címet minden kisbetűvel `your-tenant` , ahol a helyére a Azure ad B2C bérlő (fabrikamb2c.onmicrosoft.com) neve kerül:

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Kattintson a **Create** (Létrehozás) gombra. Másolja a későbbiekben használni kívánt **alkalmazás-azonosítót** .
9. Válassza ki az alkalmazást, majd válassza a **Beállítások**lehetőséget.
10. Válassza a **kulcsok**lehetőséget, adja meg a kulcs leírását, válasszon ki egy időtartamot, majd kattintson a **Mentés**gombra. Másolja a megjelenő kulcs értékét későbbi használatra.
11. A **Beállítások**területen válassza a **Tulajdonságok**lehetőséget, majd `Yes`a **több-bérlős** beállítást, majd kattintson a **Mentés** gombra.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott alkalmazás-kulcsot kell tárolnia.

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
3. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
4. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
5. A **Beállítások**területen válassza `Manual`a lehetőséget.
6. Adja meg a szabályzat kulcsának **nevét** . Például: `ContosoAppSecret`.  A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs nevéhez.
7. A **Secret (titkos**kód) mezőben adja meg a korábban rögzített alkalmazás kulcsát.
8. A **kulcshasználat**beállításnál válassza `Signature`a elemet.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók az Azure AD használatával jelentkezzenek be, az Azure AD-t olyan jogcím-szolgáltatóként kell megadnia, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is megadhatja, ha hozzáadja az Azure AD-t az **ClaimsProvider** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions. xml fájlt*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="unique_name" />
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

4. A **ClaimsProvider** elem alatt frissítse a **tartomány** értékét egy olyan egyedi értékre, amelyet a más identitás-szolgáltatóktól való megkülönböztetéshez használhat.
5. A **kivonatjogcím** elem alatt frissítse a **DisplayName**értékét. Ez az érték a bejelentkezési képernyőjén a bejelentkezés gombján jelenik meg.
6. Állítsa be az **client_id** az alkalmazás-azonosítóra az Azure ad mulity – bérlői alkalmazás regisztrációja alapján.

### <a name="restrict-access"></a>Hozzáférés korlátozása

> [!NOTE]
> A `https://sts.windows.net` **ValidTokenIssuerPrefixes** értékének használata lehetővé teszi, hogy az összes Azure ad-felhasználó bejelentkezzen az alkalmazásba.

Frissítenie kell az érvényes jogkivonat-kiállítók listáját, és korlátozni kell a hozzáférést az Azure AD-bérlő azon felhasználói számára, akik be tudnak jelentkezni. Az értékek beszerzéséhez meg kell vizsgálnia az egyes Azure AD-bérlők metaadatait, amelyekhez be szeretné jelentkezni a felhasználótól. Az adatformátum a következőhöz hasonlóan néz ki: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, ahol `your-tenant` az az Azure ad-bérlő neve (contoso.com vagy bármely más Azure ad-bérlő).

1. Nyissa meg a böngészőt, és lépjen a **metaadatok** URL-címére, és keresse meg a **kiállító** objektumot, és másolja az értékét. A következőhöz hasonlóan kell kinéznie: `https://sts.windows.net/tenant-id/`.
2. Másolja és illessze be a **ValidTokenIssuerPrefixes** kulcs értékét. A vesszővel elválasztva többet is hozzáadhat. Erre példa a fenti XML-minta.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a házirendet, hogy Azure AD B2C tudja, hogyan kommunikálhat az Azure AD-címtárral. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói útvonalról, majd módosítsa úgy, hogy az Azure AD-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagból.
2. A **UserJourney** elem `Id="SignUpOrSignIn"`teljes tartalmának megkeresése és másolása.
3. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInContoso`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha hozzáad egy **ClaimsProviderSelection** elemet az Azure ad-hoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg az Ön által létrehozott `Order="1"` felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects**területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `AzureADExchange`:

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

A Azure AD B2C folytatott kommunikáció egy, a bérlőben létrehozott alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adja meg az alkalmazás nevét, például *testapp1*.
6. A **Web App/web API**esetében válassza `Yes`a elemet, majd `https://jwt.ms` adja meg a **Válasz URL-címét**.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignContoso. XML fájlba*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInContoso`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignContoso).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.
