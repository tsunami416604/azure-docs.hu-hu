---
title: Jelentkezzen be egy Salesforce-ban az SAML-szolgáltató által létrehozott egyéni szabályzatok használatával az Azure Active Directory B2C |} A Microsoft Docs
description: Jelentkezzen be egy Salesforce-ban az SAML-szolgáltató által létrehozott egyéni szabályzatok az Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 920d5924271d31848b455ebece9e4832548e1b06
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846404"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Jelentkezzen be egy Salesforce-ban az SAML-szolgáltató által létrehozott egyéni szabályzatok az Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára, a Salesforce szervezet használja [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben. Engedélyezi a bejelentkezést hozzáadásával egy [SAML-alapú technikai profilban](saml-technical-profile.md) egy egyéni házirend.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).
- Ha ezt még nem tette meg, regisztráljon egy [ingyenes fiókot Developer Edition](https://developer.salesforce.com/signup). Ez a cikk a [Salesforce villámgyors élmény](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Saját tartomány beállítása](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) Salesforce szervezete számára.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Identitás-szolgáltatóként a Salesforce beállítása

1. [Jelentkezzen be Salesforce](https://login.salesforce.com/). 
2. A bal oldali menüben a **beállítások**, bontsa ki a **identitás**, majd válassza ki **identitásszolgáltató**.
3. Válassza ki **identitásszolgáltató engedélyezése**.
4. A **válassza ki a tanúsítványt**, válassza ki a Salesforce-hoz való kommunikációra az Azure AD B2C-vel használni kívánt tanúsítványt. Az alapértelmezett tanúsítvány is használható. 
5. Kattintson a **Save** (Mentés) gombra. 

### <a name="create-a-connected-app-in-salesforce"></a>A csatlakoztatott alkalmazás létrehozása a Salesforce-ban

1. Az a **identitásszolgáltató** lapon jelölje be **szolgáltatók most jönnek létre keresztül kapcsolódó alkalmazásokhoz. Kattintson ide.**
2. A **alapinformációk**, adja meg a szükséges értékeket a csatlakoztatott alkalmazáshoz.
3. A **a webalkalmazás-beállítások**, ellenőrizze a **SAML engedélyezése** mezőbe.
4. Az a **Entitásazonosító** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy értékét lecserélte `your-tenant` az Azure AD B2C-bérlő nevével.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Az a **ACS URL** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy értékét lecserélte `your-tenant` az Azure AD B2C-bérlő nevével.
      
      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Görgessen a lista aljára, és kattintson **mentése**.

### <a name="get-the-metadata-url"></a>A metaadatok URL-Címének lekéréséhez

1. A csatlakoztatott alkalmazáshoz – Áttekintés lapon kattintson **kezelés**.
2. Másolja az értéket a **metaadatok Discovery Endpoint-ügynök**, majd mentse azt. Ez a cikk későbbi részében fogja használni.

### <a name="set-up-salesforce-users-to-federate"></a>Állítsa be a Salesforce-felhasználók összevonásához

1. A a **kezelés** lapon kattintson a csatlakoztatott alkalmazás **Spravovat Profily**.
2. Válassza ki a profilok (vagy felhasználói csoportokat), hogy az Azure AD B2C-vel összevonni kívánt. Rendszergazdaként, válassza ki a **rendszergazda** jelölőnégyzetet, így Ön a Salesforce-fiók használatával is összevonható.

## <a name="generate-a-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A Salesforce-hoz küldött kérések be kell jelentkeznie az Azure AD B2C által. Létrehoz egy aláíró tanúsítványt, nyissa meg az Azure Powershellt, és futtassa a következő parancsokat.

> [!NOTE]
> Győződjön meg arról, hogy a bérlő nevét és jelszavát, az első két sorainak frissítése.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Hozzon létre egy házirendjének kulcsa

Kell tárolnia a tanúsítványt, amelyet az Azure AD B2C-bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer – előzetes verzió**.
5. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
6. A **beállítások**, válassza a `Upload`.
7. Adja meg a szabályzat **nevét**. Ha például SAMLSigningCert. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
8. Keresse meg és válassza ki a létrehozott B2CSigningCert.pfx tanúsítványt. 
9. Adja meg a **jelszó** a tanúsítványt.
3. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>A jogcímeket szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be a Salesforce-fiók használatával, definiálhatja a fiók, amely az Azure AD B2C-végponton keresztül kommunikálhat egy jogcímszolgáltatótól szeretne. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít. 

Definiálhat egy Salesforce-fiókban, egy jogcímszolgáltatótól hozzáadásával, hogy a **ClaimsProviders** elem a bővítmény fájlban a szabályzat.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a legfelső szintű elem alatt.
3. Vegyen fel egy új **ClaimsProvider** módon:

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
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Frissítse az értéket a **PartnerEntity** a Salesforce metaadatok URL-címet korábban vágólapra másolt.
5. Frissítse az értéket, mindkét példányát **StorageReferenceId** neve lesz a kulcs az aláíró tanúsítványban. For example, B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

Már konfigurálta a szabályzatot, hogy az Azure AD B2C-vel tudja, hogyan kommunikál a Salesforce-fiókjához. A szabályzat csak, győződjön meg arról, hogy minden problémát, amennyiben nem rendelkezik a bővítmény fájlt töltsön fel.

1. Az a **egyéni szabályzatok** az Azure AD B2C-bérlő, válassza a lap **szabályzat feltöltése**.
2. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrációs vagy bejelentkezési képernyőt. Elérhető legyen, hozzon létre egy meglévő sablon felhasználói interakciósorozat másolatát, és ezután módosítsa, hogy a Salesforce-identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* az alapszintű csomag fájlt.
2. Keresse meg és másolja ki a teljes tartalmát a **UserJourney** , amely tartalmazza az elem `Id="SignUpOrSignIn"`.
3. Nyissa meg a *TrustFrameworkExtensions.xml* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a teljes tartalmát a **UserJourney** gyermekeként kimásolt elem a **UserJourneys** elemet.
5. Nevezze át a felhasználói út azonosítója. Például: `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **hiányzik a ClaimsProviderSelection** elem ehhez hasonló regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy **hiányzik a ClaimsProviderSelection** elem a LinkedIn-fiók, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon.

1. Keresse meg a **OrchestrationStep** , amely tartalmazza az elem `Order="1"` az imént létrehozott felhasználói interakciósorozat.
2. A **ClaimsProviderSelects**, adja hozzá a következő elemet. Állítsa az értékét **TargetClaimsExchangeId** egy megfelelő értéket, például a `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot a Salesforce-fiókban.

1. Keresse meg a **OrchestrationStep** tartalmazó `Order="2"` a felhasználói interakciósorozatban szereplő.
2. Adja hozzá a következő **ClaimsExchange** gondoskodik róla, hogy ugyanazt az értéket használt elem **azonosító** során használt **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```
    
    Frissítse az értéket a **TechnicalProfileReferenceId** , a **azonosító** a korábban létrehozott technikai profil. Például: `LinkedIn-OAUTH`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Az Azure AD B2C-alkalmazás létrehozása

Kommunikáció az Azure AD B2C-vel hoz létre a bérlő alkalmazás keresztül történik. Ez a szakasz felsorolja a nem kötelező lépések is elvégezheti egy test-alkalmazás létrehozása, ha ezt még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adja meg egy nevet az alkalmazásnak, például *testapp1*.
6. A **Web App / Web API**, jelölje be `Yes`, majd adja meg `https://jwt.ms` a a **válasz URL-cím**.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="update-and-test-the-relying-party-file"></a>Frissítse és a függő entitás fájl tesztelése

Frissítse a függő entitásonkénti (RP) fájl, amely kezdeményezi az imént létrehozott felhasználói interakciósorozatban:

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárban, és nevezze át. Például nevezze át, hogy *SignUpSignInSalesforce.xml*.
2. Nyissa meg az új fájlt, és frissítse az értéket, a **PolicyId** az attribútum **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInSalesforce`.
3. Frissítse az értéket a **PublicPolicyUri** URI-a szabályzat. Ha például`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Frissítse az értéket, a **hivatkozásazonosító** attribútum **DefaultUserJourney** megfelelően (SignUpSignInSalesforce) létrehozott új felhasználói interakciósorozat azonosítója.
5. A módosítások mentéséhez, feltöltheti a fájlt, és válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C-alkalmazást az van kiválasztva, a **válassza ki az alkalmazás** mezőben, majd tesztelje kattintva **Futtatás most**.
