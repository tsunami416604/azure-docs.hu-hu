---
title: Bejelentkezés beállítása a több-bérlős Azure AD-hez egyéni szabályzatok szerint
titleSuffix: Azure AD B2C
description: Adjon hozzá egy több-bérlős Azure AD-identitásszolgáltató t az Azure Active Directory B2C egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678109"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>A több-bérlős Azure Active Directorybejelentkezés beállítása egyéni szabályzatok használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést az Azure Active Directory (Azure AD) több-bérlős végpontját használó felhasználók számára az Azure AD B2C [egyéni szabályzatok](custom-policy-overview.md) használatával. Ez lehetővé teszi, hogy a felhasználók több Azure AD-bérlők bejelentkezhet az Azure AD B2C használatával, anélkül, hogy konfigurálnia kell egy identitásszolgáltató minden bérlő. Azonban a vendég tagok a bérlők egyikében **sem tud** nak bejelentkezni. Ahhoz, hogy [minden egyes bérlőkülön-külön konfigurálja.](identity-provider-azure-ad-single-tenant-custom.md)

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Ahhoz, hogy engedélyezze a bejelentkezést egy adott Azure AD-szervezet felhasználói számára, regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés szűrőt** a felső menüben, majd válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás **nevét.** Például: `Azure AD B2C App`.
1. Válassza a Fiókok lehetőséget az alkalmazás **bármely szervezeti címtárában.**
1. Az **átirányításURI,** fogadja el a **web**értékét, és adja meg `your-B2C-tenant-name` a következő URL-t az összes kisbetű, ahol lecseréli az Azure AD B2C bérlő nevét.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Kattintson a **Register** (Regisztrálás) elemre. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza **a Tanúsítványok & titkos kulcsok**at, majd az Új **ügyféltitok**lehetőséget.
1. Adja meg a titkos **kulcsot,** válasszon egy lejárati lehetőséget, majd válassza a **Hozzáadás**lehetőséget. Rögzítse a titkos **adatik értékét** egy későbbi lépésben való használatra.

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha azt szeretné, `family_name` `given_name` hogy a és a jogcímek az Azure AD-ből, konfigurálhatja az alkalmazás választható jogcímeket az Azure Portal felhasználói felületén vagy az alkalmazásjegyzékben. További információ: [Választható jogcímek biztosítása az Azure AD-alkalmazáshoz.](../active-directory/develop/active-directory-optional-claims.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** szakaszban válassza **az Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez a nem kötelező jogcímeket konfigurálni szeretné a listában.
1. A **Kezelés** szakaszban válassza a **Token konfigurációja**lehetőséget.
1. Válassza **a Választható jogcím hozzáadása**lehetőséget.
1. A **Token típushoz**válassza az **Azonosító**lehetőséget.
1. Válassza ki a hozzáadni `family_name` `given_name`kívánt választható jogcímeket, és válassza ki a lehetőséget.
1. Kattintson a **Hozzáadás** parancsra.

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben létrehozott alkalmazáskulcsot kell tárolnia.

1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés szűrőa** felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. A **Házirendek**csoportban válassza **az Identitáskezelési keretrendszert**.
1. Válassza **a Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Manual`területen válassza a lehetőséget.
1. Adja meg a házirendkulcs **nevét.** Például: `AADAppSecret`.  Az előtag `B2C_1A_` létrehozásakor automatikusan hozzákerül a kulcs nevéhez, így a következő szakaszban az XML-ben való hivatkozása *a B2C_1A_AADAppSecret*.
1. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
1. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók az Azure AD használatával jelentkezzenek be, meg kell határoznia az Azure AD-t olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

Az Azure AD-t jogcímszolgáltatóként definiálhatja, ha hozzáadja az Azure AD-t a házirend bővítményfájljában lévő **ClaimsProvider** elemhez.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
1. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

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
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
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

1. A **ClaimsProvider** elem alatt frissítse a **Tartomány** értékét egy olyan egyedi értékre, amely megkülönböztetheti azt a többi identitásszolgáltatótól.
1. A **TechnicalProfile** elem alatt frissítse például a `Contoso Employee` **DisplayName**értékét. Ez az érték jelenik meg a bejelentkezési gomb a bejelentkezési oldalon.
1. Állítsa be **a client_id** az Azure AD több-bérlős alkalmazás, amely korábban regisztrált alkalmazásazonosítóját.
1. A **Kriptográfiai kulcsok**csoportban frissítse a **StorageReferenceId** értékét a korábban létrehozott házirendkulcs nevére. Például: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Hozzáférés korlátozása

> [!NOTE]
> A `https://login.microsoftonline.com/` **ValidTokenIssuerPrefixes** értékként való használata lehetővé teszi, hogy az összes Azure AD-felhasználó jelentkezzen be az alkalmazásba.

Frissítenie kell az érvényes jogkivonat-kibocsátók listáját, és korlátoznia kell a hozzáférést az Azure AD-bérlői felhasználók egy adott listájához, akik bejelentkezhetnek.

Az értékek beszerzéséhez tekintse meg az OpenID Connect felderítési metaadatait az egyes Azure AD-bérlők, amelyek szeretné, hogy a felhasználók jelentkezzen be. A metaadat-URL formátuma `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`hasonló `your-tenant` a, ahol az Azure AD-bérlő neve. Például:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Hajtsa végre ezeket a lépéseket minden egyes Azure AD-bérlőhöz, amelyet a bejelentkezéshez kell használni:

1. Nyissa meg a böngészőt, és lépjen a bérlő OpenID Connect metaadat-URL-címére. Keresse meg a **kibocsátó** objektumot, és jegyezze fel annak értékét. Meg kell kinéznie hasonló `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`.
1. Másolja és illessze be az értéket a **ValidTokenIssuerPrefixes** kulcsba. Különítsd el a több kibocsátót vesszővel. Az előző `ClaimsProvider` XML-mintában megjelenik egy példa két kibocsátóval.

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

Mostanra úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon az Azure AD-könyvtárakkal. Próbálja meg feltölteni a házirend bővítményfájlját, csak hogy megerősítse, hogy eddig nincsenek-e problémái.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
2. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
3. Válassza a **Feltöltés** lehetőséget.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs/bejelentkezési képernyők egyikén sem. Elérhetővé tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói út, majd módosítsa úgy, hogy az Azure AD-identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInContoso`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs/bejelentkezési képernyőn. Ha hozzáad egy **ClaimsProviderSelection** elemet az Azure AD-hez, egy új gomb jelenik meg, amikor egy felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely a `Order="1"` *TrustFrameworkExtensions.xml fájlban*létrehozott felhasználói út része.
1. A **ClaimsProviderSelects csoportban**adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `AzureADExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni az Azure AD egy jogkivonat fogadására. Kapcsolja össze a gombot egy műveletösszekapcsolásával a technikai profil az Azure AD-jogcímszolgáltató.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange** elemet, ügyelve arra, **hogy** ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil **azonosítójával.** Például: `Common-AAD`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi:

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignContoso.xml*fájlra.
1. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInContoso`.
1. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának. Például *SignUpSignInContoso*.
1. Mentse a módosításokat, és töltse fel a fájlt.
1. Az **Egyéni házirendek**csoportban válassza ki az új házirendet a listában.
1. Az **alkalmazás kiválasztása** legördülő menüben válassza ki a korábban létrehozott Azure AD B2C alkalmazást. *Például, testapp1*.
1. Másolja a **Futtatás most végpontot,** és nyissa meg egy privát böngészőablakban, például inkognitómódban a Google Chrome-ban vagy egy InPrivate-ablakban a Microsoft Edge-ben. A privát böngészőablakban való megnyitás lehetővé teszi a teljes felhasználói út tesztelését az aktuálisan gyorsítótárazott Azure AD-hitelesítő adatok használatával.
1. Válassza ki az Azure AD bejelentkezési gombot, például *a Contoso Employee*, majd adja meg a hitelesítő adatokat a felhasználó az Egyik Azure AD szervezeti bérlők. A rendszer kéri, hogy engedélyezze az alkalmazást, majd adja meg a profiljához szükséges adatokat.

Ha a bejelentkezési folyamat sikeres, a böngésző `https://jwt.ms`átirányítása a rendszerre, amely megjeleníti az Azure AD B2C által visszaadott jogkivonat tartalmát.

A több-bérlős bejelentkezési képesség teszteléséhez hajtsa végre az utolsó két lépést egy másik Azure AD-bérlőt létező felhasználó hitelesítő adataival.

## <a name="next-steps"></a>További lépések

Ha egyéni házirendekkel dolgozik, előfordulhat, hogy a fejlesztés során további információkra van szüksége a házirendhiba elhárításakor.

A problémák diagnosztizálása érdekében ideiglenesen "fejlesztői módba" helyezheti a szabályzatot, és az Azure Application Insights segítségével naplót gyűjthet. Megtudhatja, hogy az [Azure Active Directory B2C: Naplók gyűjtése.](troubleshoot-with-application-insights.md)
