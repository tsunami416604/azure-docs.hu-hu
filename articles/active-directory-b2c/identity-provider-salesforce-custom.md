---
title: Bejelentkezés beállítása Salesforce SAML-szolgáltatóval egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Állítsa be a regisztrációt egy Salesforce SAML-szolgáltatóval az Azure Active Directory B2C egyéni szabályzataival.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67fe9ef4ad2b025d11f88976973658c9cd8ae693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187950"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása Salesforce SAML-szolgáltatóval az Azure Active Directory B2C egyéni szabályzataival

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a Salesforce-szervezet felhasználói számára az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatok](custom-policy-overview.md) használatával. A bejelentkezésengedélyezéséhez [saml technikai profilt](saml-technical-profile.md) adjon hozzá egy egyéni házirendhez.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.
- Ha még nem tette meg, regisztráljon egy [ingyenes Developer Edition-fiókot.](https://developer.salesforce.com/signup) Ez a cikk a [Salesforce Lightning Experience -t](https://developer.salesforce.com/page/Lightning_Experience_FAQ)használja.
- [Saját tartomány beállítása](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) a Salesforce-szervezet számára.

### <a name="set-up-salesforce-as-an-identity-provider"></a>A Salesforce beállítása identitásszolgáltatóként

1. [Jelentkezzen be a Salesforce-ba.](https://login.salesforce.com/)
2. A bal oldali menü **Beállítások területén**kattintson az **Identitás**elemre, és válassza **az Identitásszolgáltató lehetőséget.**
3. Válassza **az Identitásszolgáltató engedélyezése**lehetőséget.
4. A **Tanúsítvány kiválasztása csoportban**válassza ki azt a tanúsítványt, amelyet a Salesforce az Azure AD B2C-vel való kommunikációhoz használ. Használhatja az alapértelmezett tanúsítványt.
5. Kattintson a **Mentés** gombra.

### <a name="create-a-connected-app-in-salesforce"></a>Csatlakoztatott alkalmazás létrehozása a Salesforce-ban

1. Az **Identitásszolgáltató** lapon válassza ki a **Szolgáltatók jönnek létre a Csatlakoztatott alkalmazások on keresztül. Kattintson ide.**
2. Az **Alapszintű információk**csoportban adja meg a csatlakoztatott alkalmazáshoz szükséges értékeket.
3. A **Webalkalmazás beállításai csoportban**jelölje be az **SAML engedélyezése** jelölőnégyzetet.
4. Az **Entitásazonosító** mezőbe írja be a következő URL-címet. Győződjön meg arról, `your-tenant` hogy lecserélte az értéket az Azure AD B2C-bérlő nevére.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Az **ACS URL-cím** mezőjébe írja be a következő URL-címet. Győződjön meg arról, `your-tenant` hogy lecserélte az értéket az Azure AD B2C-bérlő nevére.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Görgessen a lista aljára, majd kattintson a **Mentés gombra.**

### <a name="get-the-metadata-url"></a>A metaadatok URL-címének beszereznie

1. A csatlakoztatott alkalmazás áttekintő lapján kattintson a **Kezelés gombra.**
2. Másolja másolni a **Metaadat-felderítési végpont**értékét, majd mentse azt. Ezt a cikk későbbi részében fogja használni.

### <a name="set-up-salesforce-users-to-federate"></a>A Salesforce-felhasználók beállítása az összeegyezéshez

1. A csatlakoztatott alkalmazás **Kezelés** lapján kattintson a **Profilok kezelése**gombra.
2. Válassza ki azokat a profilokat (vagy felhasználói csoportokat), amelyeket össze szeretne egyeesíten az Azure AD B2C-vel. Rendszergazdaként jelölje be a **Rendszergazda** jelölőnégyzetet, hogy a Salesforce-fiók használatával egyesíthesse az ellenőrzést.

## <a name="generate-a-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A Salesforce-nak küldött kérelmeket az Azure AD B2C-nek kell aláírnia. Aláíró tanúsítvány létrehozásához nyissa meg az Azure PowerShellt, majd futtassa a következő parancsokat.

> [!NOTE]
> Győződjön meg arról, hogy frissíti a bérlő nevét és jelszavát a felső két sorban.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

Az Azure AD B2C-bérlőben létrehozott tanúsítványt kell tárolnia.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
5. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
6. A **Beállítások** `Upload`területen válassza a lehetőséget.
7. Adja meg a szabályzat **nevét**. Például SAMLSigningCert. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. Tallózással keresse meg és jelölje ki a létrehozott B2CSigningCert.pfx tanúsítványt.
9. Adja meg a tanúsítvány **jelszavát.**
3. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók egy Salesforce-fiók használatával jelentkezzenek be, meg kell határoznia a fiókot olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

A Salesforce-fiókot jogcímszolgáltatóként definiálhatja, ha hozzáadja azt a házirend bővítményfájljában lévő **ClaimsProviders** elemhez. További információt az [SAML technikai profiljának definiálása című](saml-technical-profile.md)témakörben talál.

1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt.*
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
1. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Frissítse a **PartnerEntity** értékét a korábban másolt Salesforce metaadat-URL-címmel.
1. Frissítse a **StorageReferenceId** mindkét példányának értékét az aláíró tanúsítvány kulcsának nevére. Például B2C_1A_SAMLSigningCert.
1. Keresse `<ClaimsProviders>` meg a szakaszt, és adja hozzá a következő XML-kódrészletet. Ha a szabályzat `SM-Saml-idp` már tartalmazza a technikai profilt, ugorjon a következő lépésre. További információt az [egyszeri bejelentkezési munkamenet-kezelés című](custom-policy-reference-sso.md)témakörben talál.

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítményfájl feltöltése ellenőrzésre

Mostanra úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon a Salesforce-fiókkal. Próbálja meg feltölteni a házirend bővítményfájlját, csak hogy megerősítse, hogy eddig nincsenek-e problémái.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
2. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. A rendelkezésre való elérhetővé tenni egy meglévő sablon felhasználói útjának másolatát, majd módosítsa azt úgy, hogy az is rendelkezik a Salesforce identitásszolgáltatóval.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs vagy bejelentkezési képernyőn. Ha egy LinkedIn-fiókhoz hozzáad egy **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor egy felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely az imént létrehozott felhasználói úthoz tartozik. `Order="1"`
2. A **ClaimsProviderSelects csoportban**adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `SalesforceExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy Salesforce-fiók egy jogkivonat fogadására.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange** elemet, ügyelve arra, **hogy** ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil **azonosítójával.** Például: `LinkedIn-OAUTH`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse az imént létrehozott felhasználói utat kezdeményező függő entitás (RP) fájlját:

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInSalesforce.xml fájlra.*
2. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInSalesforce`.
3. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a létrehozott új felhasználói út (SignUpSignInSalesforce) azonosítójának.
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van jelölve az **Alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**gombra kattintva.
