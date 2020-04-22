---
title: Bejelentkezés beállítása Azure AD-fiókkal egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Állítsa be a bejelentkezést egy Azure Active Directory-fiókkal az Azure Active Directory B2C-ben egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: be3a7a3ce4ce3a06398436058ea5d4d935ef5a5c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678095"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Azure Active Directory-fiókkal az Azure Active Directory B2C egyéni szabályzataival

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést az Azure Active Directory (Azure AD) szervezetből származó felhasználók számára az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatainak](custom-policy-overview.md) használatával.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben létrehozott alkalmazáskulcsot kell tárolnia.

1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés szűrőa** felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. A **Házirendek**csoportban válassza **az Identitáskezelési keretrendszert**.
1. Válassza **a Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Manual`területen válassza a lehetőséget.
1. Adja meg a házirendkulcs **nevét.** Például: `ContosoAppSecret`.  Az előtag `B2C_1A_` létrehozásakor automatikusan hozzákerül a kulcs nevéhez, így a következő szakaszban az XML-ben való hivatkozása *a B2C_1A_ContosoAppSecret*.
1. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
1. A Kulcs használata `Signature` **esetén**válassza a lehetőséget.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók az Azure AD használatával jelentkezzenek be, meg kell határoznia az Azure AD-t olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

Az Azure AD-t jogcímszolgáltatóként definiálhatja, ha hozzáadja az Azure AD-t a házirend bővítményfájljában lévő **ClaimsProvider** elemhez.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
3. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
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

4. A **ClaimsProvider** elem alatt frissítse a **Tartomány** értékét egy olyan egyedi értékre, amely megkülönböztetheti azt a többi identitásszolgáltatótól. Például: `Contoso`. A tartománybeállítás végén `.com` nem helyezhet el beállítást.
5. A **ClaimsProvider** elem alatt frissítse a **DisplayName** értékét a jogcímszolgáltató rövid nevére. Ez az érték jelenleg nincs használatban.

### <a name="update-the-technical-profile"></a>A technikai profil frissítése

Az Azure AD-végpont token beszerezéséhez meg kell határoznia azokat a protokollokat, amelyeket az Azure AD B2C-nek az Azure AD-vel való kommunikációhoz kell használnia. Ez a **ClaimsProvider** **TechnicalProfile** elemén belül történik.

1. Frissítse a **TechnicalProfile** elem azonosítóját. Ez az azonosító a házirend más részeiből származó technikai profilra vonatkozik, például `OIDC-Contoso`.
1. Frissítse a **DisplayName**értékét. Ez az érték jelenik meg a bejelentkezési gombon a bejelentkezési képernyőn.
1. A **Leírás**értékének frissítése.
1. Az Azure AD az OpenID Connect protokollt **Protocol** használja, `OpenIdConnect`ezért győződjön meg arról, hogy a Protokoll értéke .
1. Állítsa be a **METADATA** `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` `tenant-name` értékét, hogy hol van az Azure AD-bérlő neve. Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Állítsa **be az** alkalmazásazonosító client_id az alkalmazásregisztrációból.
1. A **Kriptográfiai kulcsok**csoportban frissítse a **StorageReferenceId** értékét a korábban létrehozott házirendkulcs nevére. Például: `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

Mostanra úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon az Azure AD-címtárral. Próbálja meg feltölteni a házirend bővítményfájlját, csak hogy megerősítse, hogy eddig nincsenek-e problémái.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
1. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de ez még nem érhető el a regisztrációs/bejelentkezési oldalakon. Elérhetővé tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói út, majd módosítsa úgy, hogy az Is rendelkezik az Azure AD-identitásszolgáltató:

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
1. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
1. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInContoso`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs/bejelentkezési oldalon. Ha hozzáad egy **ClaimsProviderSelection** elemet az Azure AD-hez, egy új gomb jelenik meg, amikor egy felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely a `Order="1"` *TrustFrameworkExtensions.xml fájlban*létrehozott felhasználói út része.
1. A **ClaimsProviderSelections**csoportban adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `ContosoExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni az Azure AD egy jogkivonat fogadására. Kapcsolja össze a gombot egy művelethez az Azure AD-jogcímszolgáltató technikai profiljának összekapcsolásával:

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
1. Adja hozzá a következő **ClaimsExchange** elemet, ügyelve arra, **hogy** ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil **azonosítójával.** Például: `OIDC-Contoso`.

1. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInContoso.xml*fájlra.
1. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInContoso`.
1. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának. Például *SignUpSignInContoso*.
1. Mentse a módosításokat, és töltse fel a fájlt.
1. Az **Egyéni házirendek**csoportban válassza ki az új házirendet a listában.
1. Az **alkalmazás kiválasztása** legördülő menüben válassza ki a korábban létrehozott Azure AD B2C alkalmazást. *Például, testapp1*.
1. Másolja a **Futtatás most végpontot,** és nyissa meg egy privát böngészőablakban, például inkognitómódban a Google Chrome-ban vagy egy InPrivate-ablakban a Microsoft Edge-ben. A privát böngészőablakban való megnyitás lehetővé teszi a teljes felhasználói út tesztelését az aktuálisan gyorsítótárazott Azure AD-hitelesítő adatok használatával.
1. Válassza ki az Azure AD bejelentkezési gombot, például *a Contoso Employee*, majd adja meg a hitelesítő adatokat egy felhasználó az Azure AD szervezeti bérlő. A rendszer kéri, hogy engedélyezze az alkalmazást, majd adja meg a profiljához szükséges adatokat.

Ha a bejelentkezési folyamat sikeres, a böngésző `https://jwt.ms`átirányítása a rendszerre, amely megjeleníti az Azure AD B2C által visszaadott jogkivonat tartalmát.

## <a name="next-steps"></a>További lépések

Ha egyéni házirendekkel dolgozik, előfordulhat, hogy a fejlesztés során további információkra van szüksége a házirendhiba elhárításakor.

A problémák diagnosztizálása érdekében ideiglenesen "fejlesztői módba" helyezheti a szabályzatot, és az Azure Application Insights segítségével naplót gyűjthet. Megtudhatja, hogy az [Azure Active Directory B2C: Naplók gyűjtése.](troubleshoot-with-application-insights.md)
