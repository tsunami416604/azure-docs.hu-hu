---
title: Az Azure AD B2C konfigurálása SAML IdP-ként az alkalmazásokhoz
title-suffix: Azure AD B2C
description: Az Azure AD B2C konfigurálása SAML protokollállítások biztosítása az alkalmazások (szolgáltatók) számára. Az Azure AD B2C az SAML-alkalmazás egyetlen identitásszolgáltatójaként (IdP) fog működni.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051619"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>SAML-alkalmazás regisztrálása az Azure AD B2C-ben

Ebben a cikkben megtudhatja, hogyan konfigurálhatja az Azure Active Directory B2C (Azure AD B2C) biztonsági helyestmondás-azonosító nyelv (SAML) identitásszolgáltató (IdP) az alkalmazások.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Az Azure AD B2C-t ügyfélidentitás- és hozzáférés-kezelési megoldásként használó szervezeteknek interakcióra lehet szükségük az IMAprotokoll használatával történő hitelesítésre konfigurált identitásszolgáltatókkal vagy alkalmazásokkal.

Az Azure AD B2C kétféleképpen valósítja meg az SAML-együttműködési műveleteket:

* *Identitásszolgáltatóként* (IdP) való működéssel és egyszeri bejelentkezés (SSO) elérésével az SAML-alapú szolgáltatókkal (az Ön alkalmazásaival)
* Szolgáltatóként *service provider* (SP) és saml-alapú identitásszolgáltatókkal, például a Salesforce-szal és az ADFS-sel való együttműködéssel

![Diagram: B2C identitásszolgáltatóként a bal oldalon és B2C mint szolgáltató a jobb oldalon](media/saml-identity-provider/saml-idp-diagram-01.jpg)

A két nem kizárólagos alapforgatókönyv összefoglalása az SAML-lel:

| Forgatókönyv | Azure AD B2C szerepkör | Használati útmutató |
| -------- | ----------------- | ------- |
| Az alkalmazás elvárja, hogy egy SAML-állítás a hitelesítés befejezéséhez. | **Az Azure AD B2C identitásszolgáltatóként (IdP) működik**<br />Az Azure AD B2C saml idp-ként működik az alkalmazásokhoz. | Ez a cikk. |
| A felhasználóknak egyszeri bejelentkezésre van szükségük egy SAML-kompatibilis identitásszolgáltatóval, például az ADFS-sel, a Salesforce-szal vagy a Shibboleth-tel.  | **Az Azure AD B2C szolgáltatóként (SP) működik**<br />Az Azure AD B2C szolgáltatóként működik, amikor az SAML-identitásszolgáltatóhoz csatlakozik. Ez egy összevonási proxy az alkalmazás és az SAML-identitásszolgáltató között.  | <ul><li>[Az ADFS-sel való bejelentkezés beállítása SAML idP-ként egyéni házirendek használatával](identity-provider-adfs2016-custom.md)</li><li>[Bejelentkezés beállítása Salesforce SAML-szolgáltatóval egyéni házirendek használatával](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre az [Azure AD B2C egyéni szabályzatokkal való ismerkedés című](custom-policy-get-started.md)lépéseit. Szüksége van a *SocialAndLocalAccounts* egyéni szabályzat a cikkben tárgyalt egyéni szabályzat kezdőcsomag.
* A Security Assertion Markup Language (SAML) protokoll alapvető ismerete.
* SAML-szolgáltatóként (SP) konfigurált webalkalmazás. Ebben az oktatóanyagban használhatja az általunk biztosított [SAML tesztalkalmazást.][samltest]

## <a name="components-of-the-solution"></a>A megoldás összetevői

A forgatókönyvhöz három fő összetevő szükséges:

* **SAML-szolgáltató,** amely képes SAML-kérelmek küldésére, fogadására, dekódolására és az Azure AD B2C SAML-kijelentéseire való válaszadásra. Ez is ismert, mint a függő fél.
* Nyilvánosan elérhető SAML **metaadat-végpont** a szolgáltató számára.
* [Azure AD B2C-bérlő](tutorial-create-tenant.md)

Ha még nem rendelkezik SAML-szolgáltatóval és társított metaadat-végponttal, használhatja ezt a minta SAML-alkalmazást, amelyet tesztelésre tettünk elérhetővé:

[SAML tesztalkalmazás][samltest]

## <a name="1-set-up-certificates"></a>1. Tanúsítványok beállítása

A szolgáltató és az Azure AD B2C közötti megbízhatósági kapcsolat létrehozásához meg kell adnia az X509-es webalkalmazás-tanúsítványokat.

* **Szolgáltatói tanúsítványok**
  * Tanúsítvány a webalkalmazásban tárolt személyes kulccsal. Ezt a tanúsítványt a szolgáltató az Azure AD B2C-nek küldött SAML-kérelem aláírására használja. Az Azure AD B2C beolvassa a nyilvános kulcsot a szolgáltató metaadataiból az aláírás érvényesítéséhez.
  * (Nem kötelező) Tanúsítvány a webalkalmazásban tárolt személyes kulccsal. Az Azure AD B2C beolvassa a nyilvános kulcsot a szolgáltató metaadataiaz SAML-állítás titkosításához. A szolgáltató ezután a személyes kulccsal visszafejti a feltételt.
* **Azure AD B2C tanúsítványok**
  * Tanúsítvány egy személyes kulc az Azure AD B2C. Ezt a tanúsítványt az Azure AD B2C használja a szolgáltatónak küldött SAML-válasz aláírásához. A szolgáltató beolvassa az Azure AD B2C metaadat-nyilvános kulcsot az SAML-válasz aláírásának ellenőrzéséhez.

Használhatja a nyilvános hitelesítésszolgáltató által kiállított tanúsítványt, vagy ebben az oktatóanyagban önaláírt tanúsítványt.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Önaláírt tanúsítvány előkészítése

Ha még nem rendelkezik tanúsítvánnyal, az oktatóanyaghoz önaláírt tanúsítványt is használhat. Windows rendszerben a PowerShell [Új-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) parancsmaghasználatával hozhat létre tanúsítványt.

1. Hajtsa végre ezt a PowerShell-parancsot önaláírt tanúsítvány létrehozásához. Módosítsa `-Subject` az argumentumot az alkalmazás és az Azure AD B2C bérlői név nek megfelelően. A `-NotAfter` dátumot úgy is módosíthatja, hogy a tanúsítványhoz más lejárati értéket adjon meg.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. A **Felhasználói tanúsítványok** > **kezelése – Aktuális felhasználói** > **személyes** > **tanúsítványok** > *yourappname.yourtenant.onmicrosoft.com* megnyitása
1. A **művelet** > >**minden feladat exportálása** > tanúsítvány**kiválasztása**
1. Válassza az **Igen** > **következő** > **igen lehetőséget, exportálja a személyes kulcsot** > **Tovább**
1. A **Fájlformátum exportálása** alapértelmezéseinek elfogadása
1. Jelszó megadása a tanúsítványhoz

### <a name="12-upload-the-certificate"></a>1.2 A tanúsítvány feltöltése

Ezután töltse fel az SAML-állítás- és válaszaláíró tanúsítványt az Azure AD B2C-be.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az Azure AD B2C-bérlőt.
1. A **Házirendek**csoportban válassza **az Identitáskezelési keretrendszert,** majd **a Házirendkulcsok lehetőséget.**
1. Válassza **a Hozzáadás**lehetőséget, majd a**Feltöltés** **beállításai lehetőséget.** > 
1. Írjon be egy **nevet**, például *SamlIdpCert*. A *B2C_1A_* előtag automatikusan hozzáadódik a kulcs nevéhez.
1. Töltse fel a tanúsítványt a fájlfeltöltési vezérlővel.
1. Adja meg a tanúsítvány jelszavát.
1. Kattintson a **Létrehozás** gombra.
1. Ellenőrizze, hogy a kulcs a várt módon jelenik-e meg. Például *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Készítse elő a politika

### <a name="21-create-the-saml-token-issuer"></a>2.1 Az SAML token kibocsátójának létrehozása

Most adja hozzá a bérlő saml tokenek kiadására képes [saml tokenek, SAML token kibocsátó](saml-issuer-technical-profile.md) és [SAML munkamenet-szolgáltató](custom-policy-reference-sso.md#samlssosessionprovider) technikai profilok használatával.

Nyissa `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** meg az egyéni házirend kezdőcsomagjában.

Keresse `<ClaimsProviders>` meg a szakaszt, és adja hozzá a következő XML-kódrészletet.

Módosíthatja a `IssuerUri` metaadatok értékét. Ez a kiállító URI-t, amely az Azure AD B2C SAML válaszában ad vissza. A függő entitás alkalmazást úgy kell konfigurálni, hogy az SAML-helyességérvényesítési ellenőrzés során elfogadja a kiállító URI-ját.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Adja hozzá az SAML függő entitás házirend

Most, hogy a bérlő saml-állításokat adhat ki, létre kell hoznia az SAML függő entitás házirendjét, és módosítania kell a felhasználói utat úgy, hogy egy JWT helyett SAML-állítást adjon ki.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Előfizetési vagy bejelentkezési szabályzat létrehozása

1. Hozzon létre egy másolatot a *SignUpOrSignin.xml* fájlról az indítócsomag munkakönyvtárában, és mentse el egy új névvel. Például *SignUpOrSigninSAML.xml*. Ez a függő entitás házirendfájlja.

1. Nyissa meg a *SignUpOrSigninSAML.xml* fájlt a kívánt szerkesztőben.

1. Változtassa `PolicyId` meg `PublicPolicyUri` a és a `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` politika _B2C_1A_signup_signin_saml_ és az alábbiakban látható módon.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Adja hozzá a következő XML-kódrészletet közvetlenül az `<RelyingParty>` elem előtt. Ez az XML felülírja a _SignUpOrSignIn_ felhasználói út 7-es számú vezénylési lépését. Ha az indítócsomag egy másik mappájából indította el, vagy vezénylési lépések hozzáadásával vagy eltávolításával testreszabta a felhasználói utazást, győződjön meg arról, hogy a szám `order` (az elemben) a tokenkibocsátó lépéshez megadott hoz igazodik (például a másik kezdőcsomag mappájában a 4. lépés `LocalAccounts`a , 6 és `SocialAccounts` 9) esetében). `SocialAndLocalAccountsWithMfa`

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Cserélje le `<TechnicalProfile>` az `<RelyingParty>` elem teljes elemét a következő technikai profil XML-re.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Frissítse `tenant-name` az Azure AD B2C-bérlő nevével.

A végső függő entitás házirendfájljának a következőkre kell hasonlítania:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Az irányelv metaadatainak feltöltése és tesztelése

Mentse a módosításokat, és töltse fel az új házirendfájlt. Miután feltöltötte mindkét házirendet (a bővítményt és a függő entitás fájljait), nyisson meg egy webböngészőt, és keresse meg a házirend metaadatait.

Az Azure AD B2C-házirend IDP metaadatai az SAML protokollban használt adatok az SAML-identitásszolgáltató konfigurációjának felfedéséhez. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési módszert stb. Az Azure AD B2C-szabályzat metaadatai a következő URL-címen érhetők el. Cserélje `tenant-name` le az Azure AD B2C-bérlő nevére és `policy-name` a szabályzat nevére (ID):

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Az egyéni szabályzat és az Azure AD B2C-bérlő már készen áll. Ezután hozzon létre egy alkalmazásregisztrációt az Azure AD B2C-ben.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Beállítási alkalmazás az Azure AD B2C címtárban

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Az alkalmazás regisztrálása az Azure Active Directoryban

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *SAMLApp1*.
1. A **Támogatott fióktípusok csoportban**válassza a **Csak ebben a szervezeti címtárban lévő Fiókok lehetőséget.**
1. Az **Átirányítás URI csoportban**válassza `https://localhost`a **Web**lehetőséget, majd írja be a billentyűt. Ezt az értéket később módosíthatja az alkalmazásregisztrációs jegyzékfájljában.
1. Kattintson a **Register** (Regisztrálás) elemre.

### <a name="42-update-the-app-manifest"></a>4.2 Az alkalmazásjegyzék frissítése

Saml-alkalmazások esetén számos tulajdonságot kell konfigurálnia az alkalmazásregisztrációs jegyzékben.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az előző szakaszban létrehozott alkalmazásregisztrációt.
1. A **Kezelés csoportban**válassza a **Jegyzékfájl** lehetőséget a jegyzékszerkesztő megnyitásához. A következő szakaszokban több tulajdonságot is módosíthat.

#### <a name="identifieruris"></a>identifierUris

A `identifierUris` egy karakterlánc-gyűjtemény, amely a felhasználó által definiált URI(k) tartalmazó, amely egyedileg azonosítja a webalkalmazást az Azure AD B2C bérlőn belül. A szolgáltatónak ezt az `Issuer` értéket az SAML-kérelem elemében kell beállítania.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Ez a tulajdonság a szolgáltató nyilvánosan elérhető metaadat-URL-címét jelöli. A metaadat-URL-cím bármely névtelenül elérhető végpontra feltöltött metaadatfájlra mutathat, például blob storage-ra.

A metaadatok az SAML protokollban használt információk egy SAML-fél, például egy szolgáltató konfigurációjának felfedésére. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési módszert stb. Az Azure AD B2C beolvassa a szolgáltató metaadatait, és ennek megfelelően jár el. A metaadatok nem szükségesek. Megadhat néhány attribútumot is, például a válasz URI-ját és a kijelentkezési URI-t közvetlenül az alkalmazásjegyzékben.

Ha az SAML metaadat-URL-címében és az alkalmazásregisztrációs jegyzékben *is* vannak megadva tulajdonságok, a **rendszer egyesíti**őket. A metaadat-URL-címben megadott tulajdonságok at dolgozza fel először, és elsőbbséget élveznek.

Ehhez az oktatóanyaghoz, amely az SAML tesztalkalmazást használja, használja a következő `samlMetadataUrl`értéket:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (nem kötelező)

Ha nem ad meg metaadat-URI-t, explicit módon megadhatja a válasz URL-címét. Ez a választható `AssertionConsumerServiceUrl` tulajdonság`SingleSignOnService` a ( URL-címet jelöli `BindingType` a szolgáltató `HTTP POST`metaadataiban), és a t an atanak kell tekinteni.

Ha úgy dönt, hogy konfigurálja a válasz URL-címét és a kijelentkezési URL-t az alkalmazásjegyzékben a szolgáltató metaadatainak használata nélkül, az Azure AD B2C nem ellenőrzi az SAML-kérelem aláírását, és nem titkosítja az SAML-választ.

Ebben az oktatóanyagban, amelyben az SAML `url` tesztalkalmazást `replyUrlsWithType` használja, állítsa be a tulajdonságát a következő JSON-kódrészletben látható értékre.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (nem kötelező)

Ez a választható `Logout` tulajdonság`SingleLogoutService` az URL-címet ( URL-t `BindingType` jelöli a függő `Http-Redirect`entitás metaadataiban), és az ehhez a függvényt feltételezi.

Ehhez az oktatóanyaghoz, amely az `logoutUrl` SAML `https://samltestapp2.azurewebsites.net/logout`tesztalkalmazást használja, hagyja a következőre:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Az alkalmazás kódjának frissítése

Az utolsó lépés az, hogy az Azure AD B2C saml idp-ként engedélyezze az SAML függő entitás alkalmazásában. Minden alkalmazás más, és a lépések, hogy erre változik. A részleteket az alkalmazás dokumentációjában találja.

Az alábbiak némelyike vagy mindegyike általában kötelező:

* **Metaadatok**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Kiállító**: Az entitásazonosító használata a metaadatfájlban
* **Bejelentkezési url/SAML végpont/SAML URL**: Ellenőrizze az értéket a metaadatfájlban
* **Tanúsítvány**: Ez *B2C_1A_SamlIdpCert,* de a személyes kulcs nélkül. A tanúsítvány nyilvános kulcsának beszerezése:

    1. Nyissa meg a fent megadott metaadat-URL-címet.
    1. Másolja az `<X509Certificate>` elem becses értékét.
    1. Illessze be egy szövegfájlba.
    1. Mentse a szövegfájlt *.cer* fájlként.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Teszt az SAML tesztalkalmazással (nem kötelező)

Az oktatóanyag befejezéséhez a [SAML tesztalkalmazásunk][samltest]használatával:

* A bérlő nevének frissítése
* Házirend nevének frissítése, például *B2C_1A_signup_signin_saml*
* Adja meg ezt a kiállító URI-ját:`https://contoso.onmicrosoft.com/app-name`

Válassza **a Bejelentkezés** lehetőséget, és megjelenik egy felhasználói bejelentkezési képernyő. Bejelentkezéskor egy SAML-feltétel t a mintaalkalmazás visszaad.

## <a name="sample-policy"></a>Minta szabályzat

Teljes mintaszabályzatot biztosítunk, amelyet az SAML tesztalkalmazással való teszteléshez használhat.

1. Az [SAML-SP által kezdeményezett bejelentkezési mintaházirend](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) letöltése
1. A `TenantId` bérlő nevének megfelelő frissítés, például *contoso.b2clogin.com*
1. A házirend nevének megtartása *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Támogatott és nem támogatott SAML módozat

A következő SAML függő entitás (RP) forgatókönyvek a saját metaadat-végponton keresztül támogatottak:

* Több kijelentkezési URL-cím vagy POST kötés az alkalmazás/egyszerű szolgáltatásobjektum kijelentkezési URL-címéhez.
* Adja meg az aláíró kulcsot az alkalmazás/egyszerű szolgáltatásobjektum RP-kérelmeinek ellenőrzéséhez.
* Adja meg a token titkosítási kulcsot az alkalmazás/egyszerű szolgáltatásobjektumban.
* Az identitásszolgáltató által kezdeményezett bejelentkezések jelenleg nem támogatottak az előzetes verzióban.

## <a name="next-steps"></a>További lépések

- Az [SAML protokollról](https://www.oasis-open.org/)további információt az OASIS honlapján talál.
- Az SAML-teszt webalkalmazás beszerezhető az [Azure AD B2C GitHub közösségi tártárából.](https://github.com/azure-ad-b2c/saml-sp-tester)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
