---
title: AADFS hozzáadása SAML-identitásszolgáltatóként egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Az ADFS 2016 beállítása az SAML protokoll és az egyéni szabályzatok használatával az Azure Active Directory B2C-ben
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 000f63ef5f73e77eb22fb539fc6736b929ac6bcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451567"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>AADFS hozzáadása SAML-identitásszolgáltatóként az Azure Active Directory B2C egyéni szabályzatai használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy ADFS-felhasználói fiók egyéni [szabályzatok](custom-policy-overview.md) használatával az Azure Active Directory B2C (Azure AD B2C) használatával. A bejelentkezésengedélyezéséhez [saml technikai profilt](saml-technical-profile.md) adjon hozzá egy egyéni házirendhez.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.
- Győződjön meg arról, hogy rendelkezik egy titkos kulccsal rendelkező tanúsítvány .pfx fájljával. Létrehozhatja saját aláírt tanúsítványát, és feltöltheti azt az Azure AD B2C-be. Az Azure AD B2C ezt a tanúsítványt használja az SAML-identitásszolgáltatónak küldött SAML-kérelem aláírásához.
- Annak érdekében, hogy az Azure elfogadja a .pfx fájl jelszavát, a jelszót az AES256-SHA256-tal ellentétben a Windows Tanúsítványtároló exportálási segédprogramTripleDES-SHA1 beállításával kell titkosítani.

## <a name="create-a-policy-key"></a>Házirendkulcs létrehozása

A tanúsítványt az Azure AD B2C-bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Az Áttekintés lapon válassza az **Identitásélmény-keretrendszer lehetőséget.**
5. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
6. A **Beállítások** `Upload`területen válassza a lehetőséget.
7. Adja meg a házirendkulcs **nevét.** Például: `SamlCert`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. Tallózással keresse meg és jelölje ki a tanúsítvány .pfx fájlját a személyes kulccsal.
9. Kattintson **a Létrehozás gombra.**

## <a name="add-a-claims-provider"></a>Jogcímszolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók ADFS-fiókhasználatával jelentkezzenek be, meg kell határoznia a fiókot olyan jogcímszolgáltatóként, amelyet az Azure AD B2C egy végponton keresztül kommunikálhat. A végpont az Azure AD B2C által használt jogcímek készletét biztosítja annak ellenőrzésére, hogy egy adott felhasználó hitelesítette-e magát.

Az ADFS-fiókot jogcímszolgáltatóként definiálhatja, ha hozzáadja azt a **házirend bővítményfájljának ClaimsProviders** eleméhez. További információt az [SAML technikai profiljának definiálása című](saml-technical-profile.md)témakörben talál.

1. Nyissa meg a *TrustFrameworkExtensions.xml fájlt.*
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem alá.
1. Adjon hozzá egy új **Kárrendezést** az alábbiak szerint:

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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Cserélje `your-ADFS-domain` le az ADFS-tartomány nevét, és cserélje le az **identityProvider** kimeneti jogcím értékét a DNS-re (A tartományt jelző tetszőleges érték).

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

Mostanra úgy konfigurálta a szabályzatot, hogy az Azure AD B2C tudja, hogyan kommunikáljon az ADFS-fiókkal. Próbálja meg feltölteni a házirend bővítményfájlját, csak hogy megerősítse, hogy eddig nincsenek-e problémái.

1. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
2. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

> [!NOTE]
> A Visual Studio B2C kódja a "socialIdpUserId" kifejezést használja. Az ADFS esetében szociálpolitikára is szükség van.
>

## <a name="register-the-claims-provider"></a>A jogcímszolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be van állítva, de nem érhető el a regisztrációs vagy bejelentkezési képernyőkön. Elérhetővé tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói út, majd módosítsa azt úgy, hogy az is rendelkezik az ADFS identitásszolgáltató.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az indítócsomagból.
2. Keresse meg és másolja a **UserJourney** `Id="SignUpOrSignIn"`elemet tartalmazó teljes tartalmát.
3. Nyissa meg a *TrustFrameworkExtensions.xml fájlt,* és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekénül másolt.
5. Nevezze át a felhasználói út azonosítóját. Például: `SignUpSignInADFS`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló az identitásszolgáltató gombjához a regisztrációs vagy bejelentkezési képernyőn. Ha Egy ADFS-fiókhoz ad hozzá **ClaimsProviderSelection** elemet, egy új gomb jelenik meg, amikor egy felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely tartalmazza `Order="1"` a létrehozott felhasználói út.
2. A **ClaimsProviderSelections**csoportban adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét megfelelő értékre, például: `ContosoExchange`

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb csatolása művelethez

Most, hogy van egy gomb a helyén, csatolnia kell egy művelethez. A művelet ebben az esetben az Azure AD B2C kommunikálni egy ADFS-fiók egy jogkivonat fogadására.

1. Keresse meg a **OrchestrationStep,** amely tartalmazza `Order="2"` a felhasználói út.
2. Adja hozzá a következő **ClaimsExchange-elemet,** ügyelve arra, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId**azonosítóhoz használt azonosítóhoz:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott technikai profil azonosítójával. Például: `Contoso-SAML2`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra ellenőrzésre.


## <a name="configure-an-adfs-relying-party-trust"></a>Adfs-függő entitás megbízhatóságának konfigurálása

Az ADFS identitásszolgáltatóként való használatához az Azure AD B2C-ben létre kell hoznia egy ADFS-függő entitás megbízhatóságát az Azure AD B2C SAML metaadatokkal. A következő példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét mutatja be:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **bérlője** a bérlő nevével, például your-tenant.onmicrosoft.com.
- **a házirend** nevével. Például B2C_1A_signup_signin_adfs.
- **az Ön technikai profilját** az Ön SAML identitásszolgáltatójának technikai profiljával. Például Contoso-SAML2.

Nyisson meg egy böngészőt, és keresse meg az URL-címet. Győződjön meg arról, hogy a megfelelő URL-címet írta be, és hogy van hozzáférése az XML-metaadatfájlhoz. Ha az ADFS Management beépülő modul használatával új függő entitás megbízhatóságát szeretné hozzáadni, és manuálisan szeretné konfigurálni a beállításokat, hajtsa végre az alábbi eljárást egy összevonási kiszolgálón. Az eljárás végrehajtásához a rendszergazdák **vagy** azzal egyenértékű jogosultság szükséges a helyi számítógépen.

1. A Kiszolgálókezelőben válassza az **Eszközök**lehetőséget, majd az **ADFS-kezelés**lehetőséget.
2. Válassza **a Függő entitás megbízhatóságának hozzáadása lehetőséget.**
3. Az **Üdvözlőlapon** válassza a **Jogcímek tisztában**lehetőséget, majd kattintson a **Start**gombra.
4. Az **Adatforrás kiválasztása** lapon válassza **az Adatok importálása a függő entitás online vagy helyi hálózaton történő közzétételéről**lehetőséget, adja meg az Azure AD B2C metaadat-URL-címét, majd kattintson a **Tovább**gombra.
5. A **Megjelenítendő név megadása** lapon adja meg a **Megjelenítendő név nevet**a Jegyzetek **csoportban,** adja meg a függő entitás megbízhatóságának leírását, majd kattintson a **Tovább**gombra.
6. A **Hozzáférés-vezérlési házirend kiválasztása** lapon jelöljön ki egy házirendet, majd kattintson a **Tovább**gombra.
7. A **Megbízhatóság hozzáadása gombra való feladása** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra a függő entitás megbízhatósági adatainak mentéséhez.
8. A **Befejezés** lapon kattintson a **Bezárás**gombra, ez a művelet automatikusan megjeleníti a **Jogcímszabályok szerkesztése** párbeszédpanelt.
9. Válassza **a Szabály hozzáadása lehetőséget.**
10. A **Jogcímszabály sablonban**válassza **az LDAP-attribútumok küldése jogcímként**lehetőséget.
11. Adja meg **a jogcímszabály nevét**. Az **Attribútumtárolóban**válassza az **Active Directory kiválasztása**lehetőséget, adja hozzá a következő jogcímeket, majd kattintson a **Befejezés** **gombra és az OK gombra.**

    | LDAP attribútum | Kimenő jogcím típusa |
    | -------------- | ------------------- |
    | Egyszerű felhasználónév | userPrincipalName |
    | vezetéknév; | family_name |
    | Utónév | given_name |
    | E-mail cím | e-mail |
    | Megjelenítendő név | név |

    Ne feledje, hogy ezek a nevek nem jelennek meg a kimenő jogcímtípus legördülő menüjében. Manuálisan kell beírnia őket. (A legördülő lista valóban szerkeszthető).

12.  A tanúsítvány típusa alapján szükség lehet a HASH algoritmus beállítására. A függő entitás megbízhatósága (B2C Bemutató) tulajdonságablakban jelölje ki a `SHA-256` **Speciális** lapot, és módosítsa a Biztonságos **kivonatoló algoritmust** a értékre, majd kattintson az Ok **gombra.**
13. A Kiszolgálókezelőben válassza az **Eszközök**lehetőséget, majd az **ADFS-kezelés**lehetőséget.
14. Jelölje ki a létrehozott függő entitás megbízhatóságát, válassza **a Frissítés lehetőséget az összevonási metaadatok közül**, majd kattintson a Frissítés **gombra.**

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C alkalmazás létrehozása

Az Azure AD B2C-vel való kommunikáció egy olyan alkalmazáson keresztül történik, amelyet a B2C-bérlőben regisztrál. Ez a szakasz felsorolja a tesztalkalmazás létrehozásához végezhető választható lépéseket, ha még nem tette meg.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Készítsen másolatot a *SignUpOrSignIn.xml fájlról* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInADFS.xml fájlra.*
2. Nyissa meg az új fájlt, és frissítse az egyedi értékkel rendelkező **TrustFrameworkPolicy** **PolicyId** attribútumának értékét. Például: `SignUpSignInADFS`.
3. Frissítse a **PublicPolicyUri** értékét a házirend URI-jával. Például,`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Frissítse a **DefaultUserJourney** **ReferenceId** attribútumának értékét, hogy megfeleljen a létrehozott új felhasználói út (SignUpSignInADFS) azonosítójának.
5. Mentse a módosításokat, töltse fel a fájlt, majd válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C alkalmazás ki van jelölve az **Alkalmazás kiválasztása** mezőben, majd tesztelje a **Futtatás most**gombra kattintva.

