---
title: "Az Azure Active Directory B2C: Salesforce SAML-szolgáltató hozzáadása a egyéni házirendek használatával |} Microsoft Docs"
description: "További tudnivalók az Azure Active Directory B2C egyéni szabályzatok létrehozása és kezelése."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.openlocfilehash: 16f7c5708b479f18de17a612a733a2be6e97ad01
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Az Azure Active Directory B2C: Salesforce-fiókok keresztül SAML használatával írja alá

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan használható [egyéni házirendek](active-directory-b2c-overview-custom.md) bejelentkezhet a felhasználók egy adott Salesforce szervezet beállítása.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-ad-b2c-setup"></a>Az Azure AD B2C beállítása

Győződjön meg arról, hogy végrehajtotta a lépéseket, amelyek bemutatják, hogyan való [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) az Azure Active Directory B2C (Azure AD B2C-vel).

Ezek a következők:

* Az Azure AD B2C bérlő létrehozása.
* Az Azure AD B2C alkalmazás létrehozása.
* Két házirend motor alkalmazás regisztrálásához.
* Kulcsok beállítása.
* Az alapszintű csomag beállításához.

### <a name="salesforce-setup"></a>Salesforce-telepítő

Ez a cikk azt feltételezzük, hogy már elvégezte a következő:

* A Salesforce-fiókhoz való regisztráció. Regisztrálhatnak az egy [ingyenes Developer Edition fiókot](https://developer.salesforce.com/signup).
* [Saját tartomány beállítása](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) Salesforce szervezete számára.

## <a name="set-up-salesforce-so-users-can-federate"></a>Így a felhasználók is összevonni Salesforce beállítása

Salesforce kommunikálni az Azure AD B2C segítségével szeretné beolvasni a Salesforce metaadatainak URL-CÍMÉT.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Identitás-szolgáltatóként Salesforce beállítása

> [!NOTE]
> Ebben a cikkben azt feltételezzük, hogy azok be [Salesforce Lightning élmény](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Bejelentkezés Salesforce](https://login.salesforce.com/). 
2. A bal oldali menüben alatt **beállítások**, bontsa ki a **identitás**, és kattintson a **identitásszolgáltató**.
3. Kattintson a **identitásszolgáltató engedélyezése**.
4. A **válassza ki azt a tanúsítványt**, válassza ki a kívánt Azure AD B2C folytatott kommunikációhoz használandó Salesforce tanúsítványt. (Az alapértelmezett tanúsítvány is használható.) Kattintson a **Save** (Mentés) gombra. 

### <a name="create-a-connected-app-in-salesforce"></a>Hozzon létre egy csatlakoztatott alkalmazáshoz a Salesforce-ban

1. Az a **identitásszolgáltató** lap megnyitásához válassza **szolgáltatók**.
2. Kattintson a **szolgáltatók létrejön keresztül csatlakozó alkalmazásokat. Kattintson ide.**
3. A **alapvető információkat**, adja meg a szükséges értékeket a csatlakoztatott alkalmazáshoz.
4. A **a webalkalmazás-beállítások**, jelölje be a **SAML engedélyezése** jelölőnégyzetet.
5. Az a **Entitásazonosító** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy cserélje le a következő `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. Az a **ACS URL-cím** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy cserélje le a következő `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Hagyja a további beállításoknál az alapértelmezett értékeket.
8. Görgessen a lista aljára, és kattintson **mentése**.

### <a name="get-the-metadata-url"></a>A metaadatok URL-cím beszerzése

1. A csatlakoztatott alkalmazáshoz – Áttekintés lapon kattintson **kezelése**.
2. Másolja a következő **metaadatok Discovery Endpoint**, majd mentse. Ez a cikk későbbi részében fogja használni.

### <a name="set-up-salesforce-users-to-federate"></a>Összevonásához Salesforce felhasználók beállítása

1. Az a **kezelése** oldalán a csatlakoztatott alkalmazást, és navigáljon **profilok**.
2. Kattintson a **profilok kezeléséhez**.
3. Válassza ki a profilok (vagy felhasználói csoportok) az Azure AD B2C összevonni kívánt. Rendszergazdaként, válassza ki a **rendszergazda** jelölőnégyzetet, így akkor is összevonva a Salesforce-fiókjával.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Aláíró tanúsítvány jön létre az Azure AD B2C-vel

Az Azure AD B2C írja alá kell Salesforce küldött kérelmeket. Aláírási tanúsítvány létrehozásához nyissa meg az Azure PowerShell, és futtassa a következő parancsokat.

> [!NOTE]
> Győződjön meg arról, hogy a bérlő nevét és jelszavát, a felső két sorok frissítéséhez.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Az SAML-aláíró tanúsítvány hozzáadása az Azure AD B2C

Az aláíró tanúsítvány feltöltése az Azure AD B2C-bérlő: 

1. Nyissa meg az Azure AD B2C-bérlő. Kattintson a **beállítások** > **identitás élmény keretrendszer** > **házirend kulcsok**.
2. Kattintson a **+ Hozzáadás**, majd:
    1. Kattintson a **beállítások** > **feltöltése**.
    2. Adjon meg egy **neve** (például SAMLSigningCert). Az előtag *B2C_1A_* automatikusan hozzáadódik a kulcs neve.
    3. Válassza ki a tanúsítványt, jelölje be **vezérlő feltöltése**. 
    4. Adja meg a tanúsítvány jelszavát, a PowerShell-parancsfájl a megadott.
3. Kattintson a **Create** (Létrehozás) gombra.
4. Győződjön meg arról, hogy létrehozott egy kulcs (például B2C_1A_SAMLSigningCert). Jegyezze fel a teljes nevét (például *B2C_1A_*). Ez a kulcs később a házirend a fog hivatkozni.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>A Salesforce SAML jogcímszolgáltató a alap házirend létrehozása

Adja meg a Salesforce egy jogcímszolgáltatótól, így a felhasználók bejelentkezés Salesforce használatával kell. Ez azt jelenti meg kell adnia a végpontot, amely az Azure AD B2C fognak kommunikálni. A végpont fog *meg* készlete *jogcímek* , amely az Azure AD B2C segítségével győződjön meg arról, hogy egy adott felhasználó hitelesítette. Ehhez adja hozzá a `<ClaimsProvider>` a Salesforce házirend bővítmény fájlban:

1. A munkakönyvtár nyissa meg a kiterjesztésű fájlt (TrustFrameworkExtensions.xml).
2. Keresés a `<ClaimsProviders>` szakasz. Ha nem létezik, hozza létre a legfelső szintű csomópontja alatt.
3. Adjon hozzá egy új `<ClaimsProvider>`:

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

Az a `<ClaimsProvider>` csomópont:

1. Módosítsa az értéket a `<Domain>` egy egyedi érték, amely megkülönbözteti `<ClaimsProvider>` az egyéb identitás-szolgáltatóktól származó.
2. Frissítse az értéket a `<DisplayName>` egy megjelenítési nevet a jogcím-szolgáltató számára. Jelenleg ez az érték nem használt.

### <a name="update-the-technical-profile"></a>A műszaki profil frissítése

A SAML-token beszerzése a Salesforce, definiálni kell a protokollokat, amelyeket az Azure AD B2C kommunikálni az Azure Active Directory (Azure AD) fogja használni. Ehhez a `<TechnicalProfile>` eleme `<ClaimsProvider>`:

1. Frissítés Azonosítóját a `<TechnicalProfile>` csomópont. Az azonosító olyan utal a műszaki profilhoz, a házirend egyéb részeitől.
2. Frissítse az értéket a `<DisplayName>`. Ez az érték jelenik meg a Bejelentkezés gombra, a bejelentkezési oldalon.
3. Frissítse az értéket a `<Description>`.
4. Salesforce a SAML 2.0-s protokollt használja. Győződjön meg arról, hogy az érték `<Protocol>` van **egy SAML2**.

Frissítés a `<Metadata>` szakasz az előző XML megfelelően a beállításokat a megadott Salesforce-fiókhoz. Az XML-kódban a metaadatok értékeinek frissítéséhez:

1. Frissítse az értéket a `<Item Key="PartnerEntity">` a Salesforce metaadatainak URL-címet korábban kimásolt. Rendelkezik a következő formátumban: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. Az a `<CryptographicKeys>` szakaszban, frissítse az értéket mindkét példányai `StorageReferenceId` az aláíró tanúsítványban (például B2C_1A_SAMLSigningCert) kulcs nevét.

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

A házirend van konfigurálva, hogy az Azure AD B2C tudja, hogyan kommunikáljon a Salesforce. Próbálja meg feltölteni a házirend, győződjön meg arról, hogy nincs probléma merül fel, amennyiben a bővítmény fájlt. A bővítményfájl házirend feltöltéséhez:

1. Az Azure AD B2C-bérlő, keresse meg a **házirend** panelen.
2. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.
3. A bővítményfájl (TrustFrameworkExtensions.xml) feltöltése. Győződjön meg arról, hogy azt nem érvényesítése sikertelen.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>A Salesforce SAML jogcímszolgáltató felhasználói út regisztrálása

Ezután adja hozzá a Salesforce SAML-Identitásszolgáltatóként a felhasználó utak egyikének. Ezen a ponton az identitásszolgáltató be van állítva, de nem áll rendelkezésre a felhasználói regisztráció vagy bejelentkezés oldalakon. Az identitásszolgáltató hozzáadni a bejelentkezési oldal, először hozzon létre egy meglévő sablon felhasználói út duplikált. Ezt követően a sablon módosításához, hogy az Azure AD identity provider is rendelkezik.

1. Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
2. Keresés a `<UserJourneys>` elemet, és másolja a teljes `<UserJourney>` érték, beleértve az Id = "SignUpOrSignIn".
3. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml). Keresés a `<UserJourneys>` elemet. Ha az elem nem létezik, hozzon létre egyet.
4. Illessze be a másolt teljes `<UserJourney>` gyermekeként a `<UserJourneys>` elemet.
5. Nevezze át az új azonosítója `<UserJourney>` (például SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Az identity provider gomb megjelenítése

A `<ClaimsProviderSelection>` elem a hasonló egy identitás szolgáltató gomb regisztráció vagy bejelentkezés lapon. Adja hozzá egy `<ClaimsProviderSelection>` Salesforce eleme, egy új gomb akkor jelenik meg, amikor a felhasználó ezt a lapot. Az identity provider gomb megjelenítése:

1. Az a `<UserJourney>` létrehozott, keresse meg a `<OrchestrationStep>` rendelkező `Order="1"`.
2. Adja hozzá a következő XML-kód:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Állítsa be `TargetClaimsExchangeId` logikai értéket. Azt javasoljuk, mint mások azonos egyezmény követően (például  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Az identity provider gomb összekapcsolása egy műveletet

Most, hogy az identity provider gomb helyen, csatolja a műveletet. Ebben az esetben a művelet van az Azure AD B2C fogadni az SAML-jogkivonatból Salesforce folytatott kommunikációhoz. Ehhez a műszaki profil létrehozhatja a Salesforce SAML a jogcím-szolgáltató:

1. Az a `<UserJourney>` csomópontban található az `<OrchestrationStep>` a `Order="2"`.
2. Adja hozzá a következő XML-kód:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Frissítés `Id` ugyanarra az értékre, amelyet korábban a használt `TargetClaimsExchangeId`.
4. Frissítés `TechnicalProfileReferenceId` számára a `Id` műszaki profilhoz, akkor a korábban létrehozott (például ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>A frissített kiterjesztésű fájl feltöltése

Befejezte a bővítmény fájl módosítása. Mentse, majd töltse fel ezt a fájlt. Győződjön meg arról, hogy az összes érvényesítések sikeres.

### <a name="update-the-relying-party-file"></a>A függő entitás fájl frissítése

Következő lépésként frissítse a függő entitásonkénti (RP) fájl, amely kezdeményezi a létrehozott felhasználói út:

1. Készítsen másolatot a SignUpOrSignIn.xml a munkakönyvtárat. Nevezze át (például SignUpOrSignInWithAAD.xml).
2. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel. Azt a nevet a házirend (például SignUpOrSignInWithAAD).
3. Módosítsa a `ReferenceId` attribútumnak `<DefaultUserJourney>` kell egyeznie a `Id` az új felhasználói út (például SignUpOrSignUsingContoso) létrehozott.
4. A módosítások mentéséhez, és majd feltölteni a fájlt.

## <a name="test-and-troubleshoot"></a>Tesztelése és hibakeresése

Az imént feltöltött, egyéni házirend tesztelése az Azure portálon, nyissa meg a házirend paneljét, és kattintson **futtatása most**. Ha a hiba, lásd: [egyéni szabályzatokkal kapcsolatos problémák elhárítása](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Következő lépések

Visszajelzés küldése a [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
