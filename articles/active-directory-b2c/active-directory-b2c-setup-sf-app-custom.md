---
title: Salesforce SAML-szolgáltatóval történő bejelentkezés beállítása egyéni szabályzatok használatával Azure Active Directory B2C
description: Salesforce SAML-szolgáltatóval történő bejelentkezés beállítása egyéni szabályzatok használatával Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b38764f7a615cce410ab3cf3c4977f558d5c5d38
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315020"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Salesforce SAML-szolgáltatóval történő bejelentkezés beállítása egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy Salesforce-szervezet felhasználói számára a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](active-directory-b2c-overview-custom.md) . A bejelentkezéshez [SAML technikai profilt](saml-technical-profile.md) kell hozzáadnia egy egyéni szabályzathoz.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md).
- Ha még nem tette meg, regisztráljon egy [ingyenes Developer Edition-fiókra](https://developer.salesforce.com/signup). Ez a cikk a [Salesforce villám-élményt](https://developer.salesforce.com/page/Lightning_Experience_FAQ)használja.
- [Állítsa be a saját tartományát](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) a Salesforce-szervezet számára.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce beállítása identitás-szolgáltatóként

1. [Jelentkezzen be a Salesforce](https://login.salesforce.com/).
2. A bal oldali menüben a **Beállítások**alatt bontsa ki az **identitás**csomópontot, majd válassza az **identitás-szolgáltató**elemet.
3. Válassza az **Identitáskezelő engedélyezése**lehetőséget.
4. A **tanúsítvány kiválasztása**területen válassza ki azt a tanúsítványt, amelyet a Salesforce használni szeretne a Azure ad B2Csal való kommunikációhoz. Használhatja az alapértelmezett tanúsítványt.
5. Kattintson a **Save** (Mentés) gombra.

### <a name="create-a-connected-app-in-salesforce"></a>Csatlakoztatott alkalmazás létrehozása a Salesforce-ben

1. Az **identitás-szolgáltató** lapon válassza a **szolgáltatók most a csatlakoztatott alkalmazások használatával létrejöttek lehetőséget. Kattintson ide.**
2. Az **alapszintű információ**területen adja meg a csatlakoztatott alkalmazás szükséges értékeit.
3. A **webalkalmazás beállításai**területen jelölje be az **SAML engedélyezése** jelölőnégyzetet.
4. Az **entitás azonosítója** mezőben adja meg a következő URL-címet. Győződjön meg arról, hogy az értékét `your-tenant` a Azure ad B2C bérlő nevére cseréli.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Az **ACS URL-címe** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy az értékét `your-tenant` a Azure ad B2C bérlő nevére cseréli.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Görgessen a lista aljára, majd kattintson a **Mentés**gombra.

### <a name="get-the-metadata-url"></a>A metaadatok URL-címének beolvasása

1. A csatlakoztatott alkalmazás áttekintés lapján kattintson a **kezelés**elemre.
2. Másolja a metaadat- **felderítési végpont**értékét, majd mentse. Ezt a cikk későbbi részében fogja használni.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce-felhasználók beállítása a összevonása

1. A csatlakoztatott alkalmazás **kezelés** lapján kattintson a **profilok kezelése**lehetőségre.
2. Válassza ki a összevonása használni kívánt profilokat (vagy felhasználói csoportokat) Azure AD B2C. Rendszergazdaként jelölje be a rendszergazda jelölőnégyzetet, hogy összevonása a Salesforce-fiók használatával.

## <a name="generate-a-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A Salesforce küldött kérelmeket Azure AD B2C alá kell írni. Aláíró tanúsítvány létrehozásához nyissa meg Azure PowerShell, majd futtassa a következő parancsokat.

> [!NOTE]
> Győződjön meg arról, hogy a felső két sorban frissíti a bérlő nevét és jelszavát.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott tanúsítványt kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
6. A **Beállítások**területen válassza `Upload`a lehetőséget.
7. Adja meg a szabályzat **nevét**. Például: SAMLSigningCert. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. Keresse meg és válassza ki a létrehozott B2CSigningCert. pfx-tanúsítványt.
9. Adja meg a tanúsítványhoz tartozó **jelszót** .
3. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Salesforce-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A Salesforce-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat fájlkiterjesztés fájljában.

1. Nyissa meg a *TrustFrameworkExtensions. xml fájlt*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Frissítse a **PartnerEntity** értékét a korábban átmásolt Salesforce metaadat URL-címével.
5. Frissítse a **StorageReferenceId** mindkét példányának értékét az aláíró tanúsítvány kulcsának nevére. Például: B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a Salesforce-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs vagy bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az a Salesforce-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagból.
2. A **UserJourney** elem `Id="SignUpOrSignIn"`teljes tartalmának megkeresése és másolása.
3. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha **ClaimsProviderSelection** elemet ad hozzá egy LinkedIn-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg az imént létrehozott felhasználói `Order="1"` útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects**területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. Ebben az esetben a művelet arra vonatkozik, hogy a Azure AD B2C kommunikáljon egy Salesforce-fiókkal a jogkivonat fogadásához.

1. Keresse meg a felhasználói útra `Order="2"` kiterjedő OrchestrationStep.
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt **azonosítóhoz** :

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil **azonosítójával** . Például: `LinkedIn-OAUTH`.

3. Mentse a *TrustFrameworkExtensions. XML* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely elindítja az imént létrehozott felhasználói utat:

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignInSalesforce. XML fájlba*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInSalesforce`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignInSalesforce).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.
