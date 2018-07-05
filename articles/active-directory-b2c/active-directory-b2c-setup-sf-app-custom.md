---
title: A Salesforce-ban az SAML-szolgáltató hozzáadása az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: További tudnivalók az Azure Active Directory B2C-vel egyéni szabályzatok létrehozása és kezelése.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1307fc455cacde81cb25ad58c5e99df21f126568
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448254"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Az Azure Active Directory B2C: Jelentkezzen be Salesforce-fiókok SAML-n keresztül

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan használható [egyéni szabályzatok](active-directory-b2c-overview-custom.md) jelentkezzen be a felhasználók számára egy adott Salesforce-szervezet beállításához.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-ad-b2c-setup"></a>Az Azure AD B2C-vel beállítása

Győződjön meg arról, hogy végrehajtotta a lépéseket, amelyek bemutatják, hogyan való [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) az Azure Active Directory B2C (Azure AD B2C-vel).

Ezek a következők:

* Hozzon létre egy Azure AD B2C-bérlőben.
* Hozzon létre egy Azure AD B2C-alkalmazást.
* Két házirend motor alkalmazások regisztrálását.
* Kulcsok beállításához.
* Az alapszintű csomag beállításához.

### <a name="salesforce-setup"></a>A Salesforce beállítása

Ez a cikk feltételezzük, hogy már elvégezte a következőket:

* A Salesforce-fiókjába regisztrált. Regisztrálhat egy [ingyenes fiókot Developer Edition](https://developer.salesforce.com/signup).
* [Saját tartomány beállítása](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) Salesforce szervezete számára.

## <a name="set-up-salesforce-so-users-can-federate"></a>Állítsa be a Salesforce-hoz, így a felhasználók is összevonható

Annak érdekében, az Azure AD B2C-vel kommunikál a Salesforce-ban, szüksége a Salesforce-metaadatok URL-Címének lekéréséhez.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Identitás-szolgáltatóként a Salesforce beállítása

> [!NOTE]
> Ez a cikk feltételezzük használ [Salesforce villámgyors élmény](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Jelentkezzen be Salesforce](https://login.salesforce.com/). 
2. A bal oldali menü alatt **beállítások**, bontsa ki a **identitás**, és kattintson a **identitásszolgáltató**.
3. Kattintson a **identitásszolgáltató engedélyezése**.
4. A **válassza ki a tanúsítványt**, válassza ki a Salesforce-hoz való kommunikációra az Azure AD B2C-vel használni kívánt tanúsítványt. (Az alapértelmezett tanúsítvány is használható.) Kattintson a **Save** (Mentés) gombra. 

### <a name="create-a-connected-app-in-salesforce"></a>A csatlakoztatott alkalmazás létrehozása a Salesforce-ban

1. Az a **identitásszolgáltató** lap megnyitásához válassza a **szolgáltatók**.
2. Kattintson a **szolgáltatók most jönnek létre keresztül kapcsolódó alkalmazásokhoz. Kattintson ide.**
3. A **alapinformációk**, adja meg a szükséges értékeket a csatlakoztatott alkalmazáshoz.
4. A **a webalkalmazás-beállítások**, jelölje be a **SAML engedélyezése** jelölőnégyzetet.
5. Az a **Entitásazonosító** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy értékét lecserélte `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. Az a **ACS URL** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy értékét lecserélte `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Hagyja a többi beállítás alapértelmezett értékeit.
8. Görgessen a lista aljára, és kattintson **mentése**.

### <a name="get-the-metadata-url"></a>A metaadatok URL-Címének lekéréséhez

1. A csatlakoztatott alkalmazáshoz – Áttekintés lapon kattintson **kezelés**.
2. Másolja az értéket a **metaadatok Discovery Endpoint-ügynök**, majd mentse azt. Ez a cikk későbbi részében fogja használni.

### <a name="set-up-salesforce-users-to-federate"></a>Állítsa be a Salesforce-felhasználók összevonásához

1. Az a **kezelés** lap a csatlakoztatott alkalmazás, lépjen a **profilok**.
2. Kattintson a **profilok kezeléséhez**.
3. Válassza ki a profilok (vagy felhasználói csoportokat), hogy az Azure AD B2C-vel összevonni kívánt. Rendszergazdaként, válassza ki a **rendszergazda** jelölőnégyzetet, így Ön a Salesforce-fiók használatával is összevonható.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Aláíró tanúsítvány létrehozása az Azure AD B2C-vel

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

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Az SAML aláírási tanúsítvány hozzáadása az Azure AD B2C-vel

Az aláíró tanúsítvány feltöltése az Azure AD B2C-bérlő: 

1. Nyissa meg az Azure AD B2C-bérlőben. Kattintson a **beállítások** > **identitás-kezelőfelületi keretrendszer** > **Szabályzatbejegyzések**.
2. Kattintson a **+ Hozzáadás**, majd:
    1. Kattintson a **beállítások** > **feltöltése**.
    2. Adjon meg egy **neve** (például SAMLSigningCert). Az előtag *B2C_1A_* automatikusan hozzáadódik a kulcs neve.
    3. Válassza ki a tanúsítványt, jelölje be **töltse fel a fájlt vezérlő**. 
    4. Adja meg a tanúsítvány jelszavát a PowerShell-parancsfájl a megadott.
3. Kattintson a **Create** (Létrehozás) gombra.
4. Győződjön meg arról, hogy létrehozott egy kulcsot (például B2C_1A_SAMLSigningCert). Jegyezze fel a teljes fájlvisszaállítási név (beleértve a *B2C_1A_*). Ezt a kulcsot később a házirend az fog hivatkozni.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>A Salesforce SAML jogcímszolgáltató az alap szabályzat létrehozása

Adja meg a Salesforce egy jogcímszolgáltatótól, így a felhasználók bejelentkezhetnek a Salesforce kell. Más szóval meg kell adnia a végpontot, amely az Azure AD B2C-vel kommunikál. A végpont lesz *biztosítanak* készletét *jogcímek* , amely az Azure AD B2C-t használ, győződjön meg arról, hogy egy adott felhasználó hitelesítette. Ehhez adja hozzá a `<ClaimsProvider>` a Salesforce-hoz, a szabályzat a bővítmény fájlban:

1. A munkakönyvtárban nyissa meg a bővítményfájl (TrustFrameworkExtensions.xml).
2. Keresse meg a `<ClaimsProviders>` szakaszban. Ha még nem létezik, hozza létre a legfelső szintű csomópontja alatt.
3. Vegyen fel egy új `<ClaimsProvider>`:

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
            <Item Key="RequestsSigned">false</Item>
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
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
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

Alatt a `<ClaimsProvider>` csomópont:

1. Módosítsa az értéket a `<Domain>` egyedi érték, amely megkülönbözteti az `<ClaimsProvider>` egyéb identitás-szolgáltatóktól származó.
2. Frissítse az értéket a `<DisplayName>` , a jogcímeket szolgáltató megjelenített nevét. Jelenleg ez az érték nem használatos.

### <a name="update-the-technical-profile"></a>A technikai profil frissítése

SAML-token beszerzése a Salesforce-ból, adjon meg, hogy Azure AD B2C-vel való kommunikációhoz, az Azure Active Directoryval (Azure AD) fogja használni a protokollokat. Ehhez a `<TechnicalProfile>` eleme `<ClaimsProvider>`:

1. Frissítés azonosítója a `<TechnicalProfile>` csomópont. Ez az azonosító segítségével tekintse meg a házirend egyéb részei a technikai profil.
2. Frissítse az értéket a `<DisplayName>`. Ez az érték a bejelentkezés gombot a bejelentkezési oldal jelenik meg.
3. Frissítse az értéket a `<Description>`.
4. Salesforce-ban az SAML 2.0 protokollt használja. Ellenőrizze, hogy az érték `<Protocol>` van **egy SAML2**.

Frissítés a `<Metadata>` szakasz előző XML-beállításoknak az adott Salesforce-fiókjához. Az XML-frissítse a metaadatok értékeket:

1. Frissítse az értéket a `<Item Key="PartnerEntity">` a Salesforce metaadatok URL-címet korábban vágólapra másolt. A következő formátumban van: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. Az a `<CryptographicKeys>` területen módosítsa a mindkét példányát `StorageReferenceId` neve lesz a kulcs az aláíró tanúsítványban (például B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

A szabályzat van konfigurálva, hogy az Azure AD B2C-vel tudja, hogyan kommunikál a Salesforce-ban. Próbálja ki a kiterjesztésű fájl, ellenőrizze, hogy nincs minden problémát, amennyiben a szabályzat feltöltése. A bővítményfájl a szabályzat feltöltése:

1. Az Azure AD B2C-bérlőben, nyissa meg a **összes szabályzat** panelen.
2. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.
3. A bővítményfájl (TrustFrameworkExtensions.xml) feltöltése. Győződjön meg arról, hogy azt nem érvényesítése sikertelen.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>A felhasználói út a Salesforce SAML jogcím-szolgáltató regisztrálása

Ezután adja hozzá a Salesforce-ban az SAML-identitásszolgáltató a felhasználói utak egyikének. Ezen a ponton az identitásszolgáltató be lett állítva, de nem áll rendelkezésre a felhasználói regisztrálási vagy bejelentkezési oldalakon. Az identitásszolgáltató hozzáadni a bejelentkezési oldal, először hozzon létre egy meglévő sablon felhasználói interakciósorozat másolatát. Ezután módosítsa a sablont, hogy az Azure ad-ben identitásszolgáltató is rendelkezik.

1. Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
2. Keresse meg a `<UserJourneys>` elemet, és másolja a teljes `<UserJourney>` érték, beleértve a azonosító = "SignUpOrSignIn".
3. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml). Keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, hozzon létre egyet.
4. Illessze be a másolt teljes `<UserJourney>` gyermekeként a `<UserJourneys>` elemet.
5. Nevezze át az új azonosítója `<UserJourney>` (például SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Az identity provider gomb megjelenítése

A `<ClaimsProviderSelection>` elem hasonlatos egy regisztrálási vagy bejelentkezési oldal identity provider gombjára. Adja hozzá egy `<ClaimsProviderSelection>` elem a Salesforce-hoz, egy új gomb jelenik meg, amikor egy felhasználó megnyitja a ezen a lapon. Az identity provider gomb megjelenítése:

1. Az a `<UserJourney>` létrehozott, keresse meg a `<OrchestrationStep>` a `Order="1"`.
2. Adja hozzá a következő XML-kódot:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Állítsa be `TargetClaimsExchangeId` logikai értéket. Javasoljuk, hogy kövesse a, a többi felületéével azonos konvenciókat követnek (például  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Az identity provider gomb összekapcsolása egy műveletet

Most, hogy egy identitás-szolgáltató gomb helyen, csatolása egy műveletet. Ebben az esetben a művelet pedig az Azure AD B2C-vel való kommunikációhoz a Salesforce-szal való fogadásához egy SAML-jogkivonat. Ezt megteheti is létrehozhatja a technikai profil, a Salesforce-ban az SAML az jogcím-szolgáltatói:

1. Az a `<UserJourney>` csomópontot, keresse meg a `<OrchestrationStep>` a `Order="2"`.
2. Adja hozzá a következő XML-kódot:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Frissítés `Id` ugyanazt az értéket, amelyet a korábban használt `TargetClaimsExchangeId`.
4. Frissítés `TechnicalProfileReferenceId` , a `Id` műszaki profil, korábban létrehozott (például ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>A frissített kiterjesztésű fájl feltöltése

Elkészült a kiterjesztésű fájl módosítása. Mentse, és töltse fel ezt a fájlt. Győződjön meg arról, hogy az összes ellenőrzés sikeres.

### <a name="update-the-relying-party-file"></a>A függő entitás fájl frissítése

Ezután frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi:

1. A munkakönyvtárban SignUpOrSignIn.xml másolatának elkészítéséhez. Nevezze (például SignUpOrSignInWithAAD.xml).
2. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel. Ez az a a szabályzat nevét (például SignUpOrSignInWithAAD).
3. Módosítsa a `ReferenceId` attribútum `<DefaultUserJourney>` megfelelően a `Id` az új felhasználói út (például SignUpOrSignUsingContoso) létrehozott.
4. Mentse a módosításokat, majd feltölti a fájlt.

## <a name="test-and-troubleshoot"></a>Tesztelése és hibakeresése

Ha tesztelni szeretné az egyéni házirend, amely az imént feltöltött, az Azure Portalon, nyissa meg a szabályzat paneljén, és kattintson **Futtatás most**. Ha ez nem sikerül, tekintse meg [egyéni szabályzatokkal kapcsolatos problémák elhárítása](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>További lépések

Visszajelzést küldhet [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
