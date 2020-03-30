---
title: Nyugodt szolgáltatás biztosítása az Azure AD B2C-ben
titleSuffix: Azure AD B2C
description: Biztosítsa az egyéni REST API-jogcímek cseréjét az Azure AD B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa2e2fb4eb6e269f45494db6d87eef40182971a2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346930"
---
# <a name="secure-your-restful-services"></a>Biztosítsa restful szolgáltatásait 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ha egy REST API-t integrál egy Azure AD B2C felhasználói úton, hitelesítéssel kell védenie a REST API-végpontot. Ez biztosítja, hogy csak a megfelelő hitelesítő adatokkal, például az Azure AD B2C-vel rendelkező szolgáltatások kezdeményezhetnek hívásokat a REST API-végpontra.

Ismerje meg, hogyan integrálhatja a REST API-t az Azure AD B2C felhasználói út az [érvényesítési felhasználói bevitel](custom-policy-rest-api-claims-validation.md) és [rest API-jogcímek cseréje egyéni szabályzatok.](custom-policy-rest-api-claims-exchange.md)

Ez a cikk ismerteti, hogyan biztosíthatja a REST API-t HTTP alapszintű, ügyféltanúsítványvagy OAuth2 hitelesítéssel. 

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az alábbi útmutatók egyikének lépéseit:

- [Integrálja a REST API-jogcímek cseréjét az Azure AD B2C felhasználói útba a felhasználói bevitel érvényesítéséhez.](custom-policy-rest-api-claims-validation.md)
- [REST API-jogcímek cseréje egyéni házirendekhez](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP alapszintű hitelesítés

A HTTP alapfokú hitelesítést a [2617-es számú RFC](https://tools.ietf.org/html/rfc2617)határozza meg. Az alapszintű hitelesítés a következőképpen működik: Az Azure AD B2C HTTP-kérelmet küld az engedélyezési fejlécben lévő ügyfélhitelesítő adatokkal. A hitelesítő adatok base64 kódolású "name:password" karakterláncként vannak formázva.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API-felhasználónév és jelszóházirend-kulcsok hozzáadása

A REST API műszaki profiljának HTTP-szintű hitelesítéssel történő konfigurálásához hozza létre a következő titkosítási kulcsokat a felhasználónév és a jelszó tárolásához:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
1. Válassza a **Házirendkulcsok**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. A **Beállítások területen**válassza a **Kézi**lehetőséget.
1. A **Name (Név)** mezőbe írja be a **RestApiUsername (RestApiUsername ) nevet.**
    Előfordulhat, *hogy B2C_1A_* előtag automatikusan hozzáadódik.
1. A **Titkos** mezőbe írja be a REST API felhasználónevét.
1. **Kulcshasználat**esetén válassza a **Titkosítás**lehetőséget.
1. Kattintson a **Létrehozás** gombra.
1. Ismét válassza **a házirendkulcsok lehetőséget.**
1. Válassza a **Hozzáadás** lehetőséget.
1. A **Beállítások területen**válassza a **Kézi**lehetőséget.
1. A **Name mezőbe**írja be a **RestApiPassword (RestApiPassword ) nevet.**
    Előfordulhat, *hogy B2C_1A_* előtag automatikusan hozzáadódik.
1. A **Titkos** mezőbe írja be a REST API jelszavát.
1. **Kulcshasználat**esetén válassza a **Titkosítás**lehetőséget.
1. Kattintson a **Létrehozás** gombra.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>A REST API technikai profiljának konfigurálása a HTTP alapszintű hitelesítés használatára

A szükséges kulcsok létrehozása után konfigurálja a REST API technikai profil metaadatait a hitelesítő adatokra való hivatkozáshoz.

1. A munkakönyvtárban nyissa meg a bővítményházirend-fájlt (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilját. Például `REST-ValidateProfile`vagy `REST-GetProfile`.
1. Keresse `<Metadata>` meg az elemet.
1. Módosítsa a *AuthenticationType típust* a rendszerre. `Basic`
1. Módosítsa az *AllowInsecureAuthInProduction (AllowInsecureAuthInProduction) (AllowInsecureAuthInProduction) (AllowInsecureAuthInProduction* `false`
1. Közvetlenül a `</Metadata>` záró elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Az alábbi példa egy HTTP alapfokú hitelesítéssel konfigurált RESTful technikai profilt mutat be:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS-ügyfél tanúsítványának hitelesítése

Az ügyféltanúsítvány-hitelesítés egy kölcsönös tanúsítványalapú hitelesítés, ahol az ügyfél, az Azure AD B2C, biztosítja az ügyféltanúsítványt a kiszolgálónak az identitásának igazolására. Ez az SSL kézfogás részeként történik. Csak a megfelelő tanúsítványokkal rendelkező szolgáltatások, például az Azure AD B2C férhetnek hozzá a REST API-szolgáltatáshoz. Az ügyféltanúsítvány egy X.509 digitális tanúsítvány. Éles környezetben azt egy hitelesítésszolgáltatónak kell aláírnia.

### <a name="prepare-a-self-signed-certificate-optional"></a>Önaláírt tanúsítvány előkészítése (nem kötelező)

Nem éles környezetben, ha még nem rendelkezik tanúsítvánnyal, önaláírt tanúsítványt is használhat. Windows rendszerben a PowerShell [Új-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) parancsmaghasználatával hozhat létre tanúsítványt.

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
1. Nyissa **meg a Felhasználói tanúsítványok** > **kezelése az aktuális felhasználói** > **személyes** > **tanúsítványok yourappname.yourtenant.onmicrosoft.com.** > *yourappname.yourtenant.onmicrosoft.com*
1. Válassza ki a > **Művelet** > **minden feladat exportálása** > **.**
1. Válassza **az Igen:** > **Next** > **Igen lehetőséget, exportálja a személyes kulcsot** > **Tovább**.
1. A **Fájlformátum exportálása**alapértelmezéseinek elfogadása.
1. Adja meg a tanúsítvány jelszavát.

### <a name="add-a-client-certificate-policy-key"></a>Ügyféltanúsítvány-házirendkulcs hozzáadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
1. Válassza a **Házirendkulcsok**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. A **Beállítások párbeszédpanelen** válassza a **Feltöltés**lehetőséget.
1. A **Név** mezőbe írja be a **RestApiClientCertificate (RestApiClientCertificate ) nevet.**
    A *B2C_1A_* előtag automatikusan hozzáadódik.
1. A **Fájlfeltöltés** mezőben válassza ki a tanúsítvány .pfx fájlját egy személyes kulccsal.
1. A **Jelszó** mezőbe írja be a tanúsítvány jelszavát.
1. Kattintson a **Létrehozás** gombra.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>A REST API műszaki profiljának konfigurálása az ügyféltanúsítvány-hitelesítés használatára

A szükséges kulcs létrehozása után konfigurálja a REST API műszaki profil metaadatait, hogy az ügyféltanúsítványra hivatkozzon.

1. A munkakönyvtárban nyissa meg a bővítményházirend-fájlt (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilját. Például `REST-ValidateProfile`vagy `REST-GetProfile`.
1. Keresse `<Metadata>` meg az elemet.
1. Módosítsa a *AuthenticationType típust* a rendszerre. `ClientCertificate`
1. Módosítsa az *AllowInsecureAuthInProduction (AllowInsecureAuthInProduction) (AllowInsecureAuthInProduction) (AllowInsecureAuthInProduction* `false`
1. Közvetlenül a `</Metadata>` záró elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Az alábbi példa egy HTTP-ügyféltanúsítvánnyal konfigurált RESTful technikai profilt mutat be:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 bemutatóra szóló hitelesítés 

A tulajdonosi jogkivonat-hitelesítés az [OAuth2.0 engedélyezési keretrendszerében van definiálva: tulajdonosi jogkivonat-használat (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). A tulajdonosi jogkivonat-hitelesítés, az Azure AD B2C http-kérelmet küld egy jogkivonatot az engedélyezési fejlécben.

```http
Authorization: Bearer <token>
```

A tulajdonosi token átlátszatlan karakterlánc. Lehet egy JWT-hozzáférési jogkivonat vagy bármely karakterlánc, amelya REST API-t elvárja az Azure AD B2C az engedélyezési fejlécben. Az Azure AD B2C a következő típusokat támogatja:

- **Bemutatóra szóló token**. Ahhoz, hogy a bemutatóra szóló jogkivonatot a Restful technikai profilban küldhesse el, a szabályzatnak először be kell szereznie a tulajdonosi jogkivonatot, majd használnia kell azt a RESTful technikai profilban.  
- **Statikus bemutatóra szóló token**. Ezt a módszert akkor használja, ha a REST API hosszú távú hozzáférési jogkivonatot ad ki. Statikus tulajdonosi jogkivonat használatához hozzon létre egy házirendkulcsot, és a RESTful technikai profilból a házirendkulcsra hivatkozzon. 

## <a name="using-oauth2-bearer"></a>Az OAuth2 hordozó használata  

A következő lépések bemutatják, hogyan használhatja az ügyfél hitelesítő adatait egy tulajdonosi jogkivonat beszerzéséhez, és adja át azt a REST API-hívások engedélyezési fejlécében.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Jogcím definiálása a tulajdonosi token tárolásához

A jogcím az adatok ideiglenes tárolását biztosítja az Azure AD B2C-szabályzat végrehajtása során. A [jogcímséma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. A hozzáférési jogkivonatot egy jogcímben kell tárolni, amelyet később használni kell. 

1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a város tulajdonosiToken a **ClaimsSchema** elem.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Hozzáférési jogkivonat beszerzése 

A hozzáférési jogkivonatot többféleképpen szerezheti be: [összevont identitásszolgáltatótól,](idp-pass-through-custom.md)egy hozzáférési jogkivonatot visszaadó REST API-nak, [egy ROPC-folyamatnak](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)a használatával vagy az [ügyfél hitelesítő adatfolyamának](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)használatával.  

A következő példa egy REST API technikai profilt használ, hogy az Azure AD token végpontja az ügyfél hitelesítő adatok http-alapszintű hitelesítésként átadott használatával. Konfigurálása az Azure AD-ben, lásd: [Microsoft identity platform és az OAuth 2.0 ügyfél hitelesítő adatok áramlását.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) Előfordulhat, hogy módosítania kell ezt az identitásszolgáltatóval való kapcsolathoz. 

A ServiceUrl,cserélje le a bérlő nevét az Azure AD-bérlő nevét. Az összes rendelkezésre álló opciót lásd a [RESTful műszaki](restful-technical-profile.md) profilhivatkozásban.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>A REST technikai profiljának módosítása a tulajdonosi token hitelesítésének használatára

A tulajdonosi jogkivonat-hitelesítés egyéni házirendben való támogatásához módosítsa a REST API technikai profilját az alábbiakkal:

1. Nyissa meg a megbízhatósági könyvtárban a *TrustFrameworkExtensions.xml* kiterjesztés házirendfájlját.
1. Keresse meg `<TechnicalProfile>` a csomópontot, amely tartalmazza `Id="REST-API-SignUp"`a .
1. Keresse `<Metadata>` meg az elemet.
1. Módosítsa a *AuthenticationType típust* *bemutatóra,* az alábbiak szerint:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Módosítsa vagy adja hozzá a *UseClaimAsBearerToken-t* *a bearerToken-hez*az alábbiak szerint. A *tulajdonosi token* annak a jogcímnek a neve, amelyből a `SecureREST-AccessToken`tulajdonosi jogkivonat (a kimeneti jogcím származik).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Győződjön meg róla, hogy a fenti jogcímet bemeneti jogcímként adja hozzá:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

A fenti kódrészletek hozzáadása után a technikai profilnak a következő XML-kódnak kell lennie:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Statikus OAuth2 hordozó használata 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Az OAuth2-tulajdonosi token házirendkulcsának hozzáadása

Hozzon létre egy házirendkulcsot a tulajdonosi token értékének tárolásához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
1. Válassza a **Házirendkulcsok**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. A **Beállítások** `Manual`területen válassza a lehetőséget.
1. Adja meg a házirendkulcs **nevét.** Például: `RestApiBearerToken`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
1. A **Titkos**mezőbe írja be a korábban rögzített ügyféltitkot.
1. A Kulcs használata `Encryption` **esetén**válassza a lehetőséget.
1. Kattintson a **Létrehozás** gombra.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurálja a REST API technikai profilját a tulajdonosi jogkivonat-házirendkulcs használatára

A szükséges kulcs létrehozása után konfigurálja a REST API technikai profil metaadatait, hogy a tulajdonosi jogkivonatra hivatkozzon.

1. A munkakönyvtárban nyissa meg a bővítményházirend-fájlt (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilját. Például `REST-ValidateProfile`vagy `REST-GetProfile`.
1. Keresse `<Metadata>` meg az elemet.
1. Módosítsa a *AuthenticationType típust* a rendszerre. `Bearer`
1. Módosítsa az *AllowInsecureAuthInProduction (AllowInsecureAuthInProduction) (AllowInsecureAuthInProduction) (AllowInsecureAuthInProduction* `false`
1. Közvetlenül a `</Metadata>` záró elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Az alábbi példa egy RESTful technikai profilt mutat be tulajdonosi jogkivonat-hitelesítéssel konfigurálva:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>További lépések

- További információ a [Restful technical profile](restful-technical-profile.md) elemről az IEF-hivatkozásban. 
