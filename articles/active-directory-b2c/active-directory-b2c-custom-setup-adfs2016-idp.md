---
title: Az ADFS-t SAML-identitás-szolgáltatóként adja hozzá a Azure Active Directory B2C-ben lévő egyéni szabályzatok használatával | Microsoft Docs
description: Az ADFS 2016 beállítása az SAML protokoll és az egyéni szabályzatok használatával Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 80accdc4a14a2246ed91a92f6472490479327e2a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827210"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Az ADFS-t SAML-identitás-szolgáltatóként adja hozzá Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti a bejelentkezést egy ADFS-felhasználói fiókhoz a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](active-directory-b2c-overview-custom.md) . A bejelentkezéshez [SAML technikai profilt](saml-technical-profile.md) kell hozzáadnia egy egyéni szabályzathoz.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md).
- Ellenőrizze, hogy van-e hozzáférése egy titkos kulccsal rendelkező Certificate. pfx fájlhoz. Létrehozhat saját aláírt tanúsítványt, és feltöltheti Azure AD B2Cba. Azure AD B2C ezt a tanúsítványt használja a SAML-identitás szolgáltatójának küldendő SAML-kérelem aláírására.
- Ahhoz, hogy az Azure elfogadja a. pfx-fájl jelszavát, a jelszót titkosítani kell a Windows tanúsítványtároló-exportálás segédprogram TripleDES-SHA1 kapcsolóval, a AES256-SHA256 szemben.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A tanúsítványt a Azure AD B2C bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
6. A **Beállítások**területen válassza `Upload`a lehetőséget.
7. Adja meg a szabályzat kulcsának **nevét** . Például: `SamlCert`. A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs nevéhez.
8. Tallózással keresse meg és válassza ki a tanúsítvány. pfx fájlját a titkos kulccsal.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók ADFS-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az ADFS-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions. xml fájlt*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Cserélje `your-ADFS-domain` le az értéket az ADFS-tartomány nevére, és cserélje le a **identityProvider** kimeneti jogcím értékét a DNS-re (tetszőleges érték, amely a tartományt jelzi).
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Mostantól úgy konfigurálta a házirendet, hogy a Azure AD B2C tudja, hogyan kell kommunikálni az ADFS-fiókkal. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
3. Kattintson a **Feltöltés** gombra.

> [!NOTE]
> A Visual Studio Code B2C bővítmény "socialIdpUserId"-t használ. Az ADFS-hez is szükség van egy közösségi házirendre.
>

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs vagy bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói útvonalról, majd módosítsa úgy, hogy az ADFS-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase. XML* fájlt az alapszintű csomagból.
2. A **UserJourney** elem `Id="SignUpOrSignIn"`teljes tartalmának megkeresése és másolása.
3. Nyissa meg a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInADFS`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a bejelentkezési vagy bejelentkezési képernyőn lévő Identity Provider gombhoz. Ha ad hozzá egy **ClaimsProviderSelection** elemet egy ADFS-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg az Ön által létrehozott `Order="1"` felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelections**területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. A művelet, ebben az esetben a Azure AD B2C, hogy egy ADFS-fiókkal kommunikáljon a tokenek fogadásához.

1. Keresse meg a felhasználói útra `Order="2"` kiterjedő OrchestrationStep.
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `Contoso-SAML2`.

3. Mentse a *TrustFrameworkExtensions. XML* fájlt, és töltse fel újra az ellenőrzéshez.


## <a name="configure-an-adfs-relying-party-trust"></a>ADFS-függő entitás megbízhatóságának konfigurálása

Ha az ADFS-t identitás-szolgáltatóként szeretné használni Azure AD B2Cban, létre kell hoznia egy ADFS-függő entitás megbízhatóságát a Azure AD B2C SAML-metaadatokkal. Az alábbi példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét jeleníti meg:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **a** bérlő neve, például Your-Tenant.onmicrosoft.com.
- **az Ön** házirendje a szabályzat nevével. Például: B2C_1A_signup_signin_adfs.
- az **Ön műszaki profilja** a SAML-identitás szolgáltatójának műszaki profiljának nevével. Például: contoso-egy SAML2.

Nyisson meg egy böngészőt, és navigáljon az URL-címre. Győződjön meg arról, hogy a helyes URL-címet adja meg, és hogy van-e hozzáférése az XML-metaadat fájlhoz. Új függő entitás megbízhatóságának az ADFS-kezelő beépülő modullal való hozzáadásához és a beállítások manuális konfigurálásához hajtsa végre az alábbi eljárást egy összevonási kiszolgálón. Az eljárás végrehajtásához legalább a **rendszergazdák** vagy ezzel egyenértékű csoport tagjának kell lennie a helyi számítógépen.

1. A Kiszolgálókezelőben válassza az **eszközök**, majd az ADFS- **kezelés**lehetőséget.
2. Válassza a **függő entitás megbízhatóságának hozzáadása**lehetőséget.
3. Az **Üdvözöljük** lapon válassza a jogcímek, majd az **Indítás**lehetőséget.
4. Az adatforrás **kiválasztása** lapon válassza a **függő entitáshoz tartozó adatok importálása online vagy helyi hálózaton**lehetőséget, adja meg a Azure ad B2C metaadat URL-címét, majd kattintson a **tovább**gombra.
5. A **megjelenítendő név megadása** lapon adja meg a **megjelenítendő nevet**a **Megjegyzések**területen, írja be a függő entitás megbízhatóságának leírását, majd kattintson a **tovább**gombra.
6. A **Access Control házirend kiválasztása** lapon válasszon ki egy házirendet, majd kattintson a **tovább**gombra.
7. A **készen áll a megbízhatósági kapcsolat hozzáadására** lapon tekintse át a beállításokat, majd kattintson a **tovább** gombra a függő entitás megbízhatósági adatainak mentéséhez.
8. A **Befejezés** lapon kattintson a **Bezárás**gombra, ez a művelet automatikusan megjeleníti a **jogcím szabályainak szerkesztése** párbeszédpanelt.
9. Válassza a **szabály hozzáadása**elemet.
10. A **jogcím-szabály sablonjában**válassza az **LDAP-attribútumok küldése jogcímként**lehetőséget.
11. Adja meg a **jogcím szabályának nevét**. Az **attribútum-tárolóban**válassza a **Active Directory kiválasztása**lehetőséget, adja hozzá a következő jogcímeket, majd kattintson a **Befejezés** és **az OK gombra**.

    | LDAP-attribútum | Kimenő jogcím típusa |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | vezetéknév; | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Megjelenítendő név | name |

    Vegye figyelembe, hogy ezek a nevek nem fognak megjelenni a kimenő jogcím típusa legördülő listában. Manuálisan kell beírnia azokat a alkalmazásban. (A legördülő lista valójában szerkeszthető).

12.  A tanúsítvány típusa alapján előfordulhat, hogy a KIVONATOLÓ algoritmust kell beállítania. A függő entitás megbízhatósága (B2C-bemutató) tulajdonságai ablakban válassza a **speciális** fület `SHA-256`, és módosítsa a **biztonságos kivonatoló algoritmust** a verzióra, majd kattintson **az OK**gombra.
13. A Kiszolgálókezelőben válassza az **eszközök**, majd az ADFS- **kezelés**lehetőséget.
14. Válassza ki a létrehozott függő entitás megbízhatóságát, válassza a **frissítés az összevonási metaadatokból**elemet, majd kattintson a **frissítés**elemre.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C-alkalmazás létrehozása

A Azure AD B2Ckel folytatott kommunikáció egy, a B2C-bérlőben regisztrált alkalmazáson keresztül történik. Ez a szakasz azokat a választható lépéseket sorolja fel, amelyekkel elvégezheti a tesztelési alkalmazások létrehozását, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot a *SignUpOrSignIn. XML fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például a *SignUpSignInADFS. XML fájlba*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInADFS`.
3. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignInADFS).
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új szabályzatot a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van választva az **alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**lehetőségre kattintva.

