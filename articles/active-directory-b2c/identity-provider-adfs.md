---
title: AD FS hozzáadása SAML-identitás-szolgáltatóként egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: AD FS 2016 beállítása az SAML protokoll és az egyéni házirendek használatával Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 767f60cae2f74f7e2a928253d45011bb6ceb5d0e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653843"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>AD FS hozzáadása SAML-identitás-szolgáltatóként egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy AD FS felhasználói fiókhoz Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) . A bejelentkezést egy SAML-identitás- [szolgáltató technikai profiljának](saml-identity-provider-technical-profile.md) egy egyéni szabályzathoz való hozzáadásával engedélyezheti.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](custom-policy-get-started.md).
- Ellenőrizze, hogy van-e hozzáférése egy titkos kulccsal rendelkező Certificate. pfx fájlhoz. Létrehozhat saját aláírt tanúsítványt, és feltöltheti Azure AD B2Cba. Azure AD B2C ezt a tanúsítványt használja a SAML-identitás szolgáltatójának küldendő SAML-kérelem aláírására. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [aláíró tanúsítvány létrehozása](identity-provider-salesforce-saml.md#generate-a-signing-certificate).
- Ahhoz, hogy az Azure elfogadja a. pfx-fájl jelszavát, a jelszót titkosítani kell a Windows tanúsítványtároló-exportálás segédprogram TripleDES-SHA1 kapcsolóval, a AES256-SHA256 szemben.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A tanúsítványt a Azure AD B2C bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Upload` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `ADFSSamlCert`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. Tallózással keresse meg és válassza ki a tanúsítvány. pfx fájlját a titkos kulccsal.
9. Kattintson a **Létrehozás** gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók egy AD FS-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Egy AD FS fiókot jogcím-szolgáltatóként is megadhat, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában. További információ: SAML- [identitás-szolgáltató technikai profiljának megadása](saml-identity-provider-technical-profile.md).

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Cserélje le a `your-AD-FS-domain` nevet a AD FS tartomány nevére, és cserélje le a **identityProvider** kimeneti jogcím értékét a DNS-re (tetszőleges érték, amely a tartományt jelzi).

1. Keresse meg a `<ClaimsProviders>` szakaszt, és adja hozzá a következő XML-kódrészletet. Ha a házirend már tartalmazza a `SM-Saml-idp` technikai profilt, ugorjon a következő lépésre. További információ: [egyszeri bejelentkezéses munkamenet-kezelés](custom-policy-reference-sso.md).

    ```xml
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

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a házirendet, hogy Azure AD B2C tudja, hogyan kommunikálhat AD FS-fiókkal. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

> [!NOTE]
> A Visual Studio Code B2C bővítmény "socialIdpUserId"-t használ. AD FShoz is szükség van egy közösségi házirendre.
>

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs vagy bejelentkezési képernyőkön nem érhető el. A szolgáltatás elérhetővé tételéhez hozzon létre egy meglévő sablon felhasználói utazásának egy másodpéldányát, majd módosítsa úgy, hogy az a AD FS identitás-szolgáltatóval is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInADFS`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha AD FS-fiókhoz ad hozzá egy **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelections** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy egy AD FS-fiókkal kommunikáljon a tokenek fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `Contoso-SAML2`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.


## <a name="configure-an-ad-fs-relying-party-trust"></a>AD FS függő entitás megbízhatóságának konfigurálása

Ha AD FS identitás-szolgáltatóként szeretné használni a Azure AD B2Cban, létre kell hoznia egy AD FS függő entitás megbízhatóságát a Azure AD B2C SAML-metaadatokkal. Az alábbi példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét jeleníti meg:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **a** bérlő neve, például Your-Tenant.onmicrosoft.com.
- **az Ön** házirendje a szabályzat nevével. Például B2C_1A_signup_signin_adfs.
- az **Ön műszaki profilja** a SAML-identitás szolgáltatójának műszaki profiljának nevével. Például: contoso-egy SAML2.

Nyisson meg egy böngészőt, és navigáljon az URL-címre. Győződjön meg arról, hogy a helyes URL-címet adja meg, és hogy van-e hozzáférése az XML-metaadat fájlhoz. Ha új függő entitás megbízhatóságát szeretné hozzáadni a AD FS felügyeleti beépülő modullal, és manuálisan konfigurálja a beállításokat, hajtsa végre az alábbi eljárást egy összevonási kiszolgálón. Az eljárás végrehajtásához legalább a **rendszergazdák** vagy ezzel egyenértékű csoport tagjának kell lennie a helyi számítógépen.

1. A Kiszolgálókezelőben válassza az **eszközök**, majd a **AD FS felügyelet** lehetőséget.
2. Válassza a **függő entitás megbízhatóságának hozzáadása** lehetőséget.
3. Az **Üdvözöljük** lapon válassza a **jogcímek**, majd az **Indítás** lehetőséget.
4. Az adatforrás **kiválasztása** lapon válassza a **függő entitáshoz tartozó adatok importálása online vagy helyi hálózaton** lehetőséget, adja meg a Azure ad B2C metaadat URL-címét, majd kattintson a **tovább** gombra.
5. A **megjelenítendő név megadása** lapon adja meg a **megjelenítendő nevet** a **Megjegyzések** területen, írja be a függő entitás megbízhatóságának leírását, majd kattintson a **tovább** gombra.
6. A **Access Control házirend kiválasztása** lapon válasszon ki egy házirendet, majd kattintson a **tovább** gombra.
7. A **készen áll a megbízhatósági kapcsolat hozzáadására** lapon tekintse át a beállításokat, majd kattintson a **tovább** gombra a függő entitás megbízhatósági adatainak mentéséhez.
8. A **Befejezés** lapon kattintson a **Bezárás** gombra, ez a művelet automatikusan megjeleníti a **jogcím szabályainak szerkesztése** párbeszédpanelt.
9. Válassza a **szabály hozzáadása** elemet.
10. A **jogcím-szabály sablonjában** válassza az **LDAP-attribútumok küldése jogcímként** lehetőséget.
11. Adja meg a **jogcím szabályának nevét**. Az **attribútum-tárolóban** válassza a **Active Directory kiválasztása** lehetőséget, adja hozzá a következő jogcímeket, majd kattintson a **Befejezés** és **az OK gombra**.

    | LDAP-attribútum | Kimenő jogcím típusa |
    | -------------- | ------------------- |
    | Egyszerű felhasználónév | userPrincipalName |
    | vezetéknév; | family_name |
    | Given-Name | given_name |
    | E-mail-cím | e-mail |
    | Display-Name | name |

    Vegye figyelembe, hogy ezek a nevek nem fognak megjelenni a kimenő jogcím típusa legördülő listában. Manuálisan kell beírnia azokat a alkalmazásban. (A legördülő lista valójában szerkeszthető).

12.  A tanúsítvány típusa alapján előfordulhat, hogy a KIVONATOLÓ algoritmust kell beállítania. A függő entitás megbízhatósága (B2C-bemutató) tulajdonságai ablakban válassza a **speciális** fület, és módosítsa a **biztonságos kivonatoló algoritmust** a verzióra `SHA-256` , majd kattintson **az OK** gombra.
13. A Kiszolgálókezelőben válassza az **eszközök**, majd a **AD FS felügyelet** lehetőséget.
14. Válassza ki a létrehozott függő entitás megbízhatóságát, válassza a **frissítés az összevonási metaadatokból** elemet, majd kattintson a **frissítés** elemre.

### <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInADFS.xmlra*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInADFS`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignInADFS).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most** lehetőségre kattintva.

## <a name="troubleshooting-ad-fs-service"></a>AD FS szolgáltatás hibaelhárítása  

A AD FS a Windows-alkalmazás naplójának használatára van konfigurálva. Ha a Azure AD B2C egyéni szabályzatait használó SAML-Identitáskezelő AD FSt hoz létre, érdemes megtekinteni a AD FS eseménynaplót:

1. A Windows **keresési sávon** írja be a **Eseménynapló** kifejezést, majd válassza ki a **Eseménynapló** asztali alkalmazást.
1. Egy másik számítógép naplójának megtekintéséhez kattintson a jobb gombbal **Eseménynapló (helyi)** elemre. Válassza a **Kapcsolódás másik számítógéphez** lehetőséget, majd adja meg a mezőket a **számítógép kiválasztása** párbeszédpanel befejezéséhez.
1. A **eseménynaplóban** nyissa meg az **alkalmazások és szolgáltatások naplóit**.
1. Válassza a **AD FS**, majd a **rendszergazda** elemet. 
1. Egy esemény további információinak megtekintéséhez kattintson duplán az eseményre.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>Az SAML-kérelem nincs aláírva a várt aláírási algoritmus eseményével

Ez a hiba azt jelzi, hogy Azure AD B2C által elküldett SAML-kérelem nincs aláírva a AD FSban konfigurált várt aláírási algoritmussal. Például az SAML-kérelem aláírása az aláírási algoritmussal történik `rsa-sha256` , de a várt aláírási algoritmus a következő: `rsa-sha1` . A probléma megoldásához ellenőrizze, hogy a Azure AD B2C és a AD FS is ugyanazzal az aláírási algoritmussal van-e konfigurálva.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>1. lehetőség: az aláírási algoritmus beállítása Azure AD B2Cban  

Megadhatja, hogyan írja alá az SAML-kérelmet Azure AD B2C. A [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) -metaadatok a `SigAlg` SAML-kérelemben szereplő paraméter (lekérdezési karakterlánc vagy post paraméter) értékét vezérlik. Az alábbi példa a Azure AD B2C az `rsa-sha256` aláírási algoritmus használatára konfigurálja.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>2. lehetőség: az aláírási algoritmus beállítása AD FSban 

Azt is megteheti, hogy az SAML-kérelem aláírási algoritmusát AD FSban konfigurálja.

1. A Kiszolgálókezelőben válassza az **eszközök**, majd a **AD FS felügyelet** lehetőséget.
1. Válassza ki a korábban létrehozott **függő entitás megbízhatóságát** .
1. Válassza a **Tulajdonságok**, majd az **előleg** lehetőséget.
1. Konfigurálja a **biztonságos kivonatoló algoritmust**, majd kattintson az **OK gombra** a módosítások mentéséhez.

::: zone-end