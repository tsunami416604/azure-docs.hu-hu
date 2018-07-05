---
title: AD FS hozzáadása egy SAML identitásszolgáltatótól az egyéni szabályzatok használatával az Azure Active Directory B2C |} A Microsoft Docs
description: Egy AD FS 2016 SAML-protokoll és az egyéni szabályzatok beállításával kapcsolatos cikkben található útmutató
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a81baae553bbf9c58d42372e25e90cd7588f2952
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445075"
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: AD FS hozzáadása egy SAML identitásszolgáltatótól az egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára az AD FS-fiók használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

Ezeket a lépéseket tartalmazza:

1.  Az AD FS megbízható függő entitás megbízhatóságának létrehozása.
2.  Az AD FS megbízható függő entitás megbízhatóságának tanúsítvány hozzáadása az Azure AD B2C-t.
3.  Jogcím-szolgáltató hozzáadása egy szabályzatot.
4.  Fiók jogcím regisztrálása AD FS-szolgáltatót, hogy egy felhasználói interakciósorozat.
5.  A szabályzat feltöltése egy Azure AD B2C-bérlőben, és a tesztelés közben.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>A jogcímbarát függő entitás megbízhatóságának létrehozása

AD FS használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell az AD FS megbízható függő entitás megbízhatóságának létrehozása, és adja meg azt a megfelelő paraméterekkel.

Adjon hozzá egy új függőentitás-megbízhatóságot az AD FS kezelő beépülő modul használatával, és manuálisan adja meg a beállításokat, hajtsa végre az alábbi eljárás egy összevonási kiszolgálón.

Tagság a **rendszergazdák**, vagy ezekkel egyenértékű a helyi számítógépen a művelet végrehajtásához szükséges minimális. Olvashat a megfelelő fiókok és csoporttagságok [helyi és tartományi alapértelmezett csoportok](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  A Kiszolgálókezelőben kattintson a **eszközök**, majd válassza ki **AD FS felügyeleti**.

2.  Kattintson a **függő entitás megbízhatóságának hozzáadása**.
    ![Függő entitás megbízhatóságának hozzáadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Az a **üdvözlő** lapon a **jogcímeket figyelembe** kattintson **Start**.
    ![Az üdvözlőoldalon kattintson a jogcímeket figyelembe](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Az a **adatforrás kiválasztása** kattintson **függő entitással kapcsolatos adatok manuális megadása**, és kattintson a **tovább**.
    ![Függő entitással kapcsolatos adatok megadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Az a **megjelenítendő név megadása** írja be egy nevet a **megjelenítendő név**alatt **megjegyzések** adjon meg egy leírást a függő entitás megbízhatóságaként, majd kattintson **tovább** .
    ![Adja meg a megjelenítendő név és megjegyzések](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Választható. Ha egy nem kötelező token titkosítási tanúsítványt, majd a a **tanúsítvány konfigurálása** kattintson **Tallózás** keresse meg a tanúsítványfájlt, és kattintson a **tovább**.
    ![Tanúsítvány konfigurálása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Az a **URL-cím konfigurálása** lapon válassza ki a **a SAML 2.0 WebSSO-protokoll támogatásának engedélyezése** jelölőnégyzetet. A **függő entitás SAML 2.0 SSO szolgáltatás URL-címe**, írja be a függő entitás megbízhatóságához a Security Assertion Markup Language (SAML) szolgáltatás végpont URL-címe, és kattintson **tovább**.  Az a **függő entitás SAML 2.0 SSO szolgáltatás URL-címe**, illessze be a `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. {Tenant} helyére a bérlő neve (például: contosob2c.onmicrosoft.com), és cserélje le a {házirend} a bővítmények a házirend nevét (például B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >A szabályzat nevét, amely signup_or_signin szabályzatot örökli, ebben az esetben ez: `B2C_1A_TrustFrameworkExtensions`.
    >Az URL-cím lehet például: https://login.microsoftonline.com/te/ **contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Függő entitás SAML 2.0 egyszeri bejelentkezési szolgáltatás URL-címe](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Az a **azonosítók konfigurálása** lapon adja meg az ugyanazon URL-címet az előző lépésben leírt, kattintson a **Hozzáadás** vegye fel a listára, és kattintson a **tovább**.
    ![Függő entitás megbízhatósági azonosítók](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Az a **válassza a hozzáférés-vezérlési házirend** válasszon ki egy szabályzatot, és kattintson a **tovább**.
    ![Válassza ki a hozzáférés-vezérlési házirend](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Az a **készen áll a megbízhatóság hozzáadására** lapon tekintse át a beállításokat, és kattintson a **tovább** , mentse a függő entitás megbízhatóságának adatait.
    ![Mentse a függő entitás megbízhatósági adatok](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  A a **Befejezés** kattintson **Bezárás**, ez a művelet automatikusan megjeleníti a **Jogcímszabályok szerkesztése** párbeszédpanel bezárásához.
    ![Jogcímszabályok szerkesztése](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Kattintson a **szabály hozzáadása**.  
      ![Új szabály hozzáadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  A **Jogcímszabály-sablon**válassza **LDAP attribútumok küldése jogcímekként**.
    ![Válassza ki az LDAP attribútumok küldése jogcímszabályként sablon](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Adja meg **Jogcímszabály neve**. Az a **attribútumtár** kiválasztása **válassza ki az Active Directory** adja hozzá a következő jogcímeket, majd kattintson a **Befejezés** és **OK**.
    ![A szabály tulajdonságainak beállítása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  A Kiszolgálókezelőben válasza **függő entitás Megbízhatóságai** , majd válassza ki a függő entitás megbízhatósági hozott létre, és kattintson **tulajdonságok**.
    ![Függő entitás tulajdonságok szerkesztése](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  A függő entitás megbízhatósági (B2C bemutató) tulajdonságai ablakban kattintson egy **aláírás** fülre, és **Hozzáadás**.  
    ![Set-aláírás](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Adja hozzá az aláírási tanúsítványt (.cert fájlt, titkos kulcs nélkül).  
    ![Az aláírási tanúsítvány hozzáadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  A függő entitás megbízhatósági (B2C bemutató) tulajdonságai ablakban kattintson a **speciális** lapon, és módosítsa a **biztonságos kivonatoló algoritmus** való **SHA-1**, kattintson a **Ok**.  
    ![Állítsa be a biztonságos kivonatoló algoritmus az SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Adja hozzá az AD FS fiókkulcs alkalmazás Azure AD B2C-vel
Összevonás az AD FS-fiókok ADFS fiók nevében az alkalmazás Azure AD B2C-vel megbízhatóságának ügyfélkódot igényel. Az AD FS-tanúsítvány tárolása az Azure AD B2C-bérlő van szüksége. 

1.  Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**
2.  Válassza ki **Szabályzatbejegyzések** elérhető a bérlői kulcsok megtekintéséhez.
3.  Kattintson a **+ Hozzáadás**.
4.  A **beállítások**, használjon **feltöltése**.
5.  A **neve**, használjon `ADFSSamlCert`.  
    Az előtag `B2C_1A_` automatikusan hozzáadhatók.
6.  A fájl feltöltése a ** válassza ki a tanúsítvány .pfx fájlját és a titkos kulcs. Megjegyzés: ezt a tanúsítványt (a titkos kulccsal) egyeznie kell, amely a kibocsátott, és használja az AD FS függő entitáshoz.
![Töltse fel a házirendjének kulcsa](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Kattintson a **Create** (Létrehozás) gombra
8.  Győződjön meg arról, hogy a kulcs létrehozott `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A bővítmény a házirend a jogcímeket szolgáltató hozzáadása
Ha azt szeretné, hogy a felhasználók jelentkezhetnek be az ADFS-fiók használatával, definiálhatja ADFS-fiók egy jogcímszolgáltatótól szeretne. Más szóval meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít.

AD FS meghatározni egy jogcímszolgáltatótól, hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend fájlban:

1. Nyissa meg a bővítmény a házirend fájlt (TrustFrameworkExtensions.xml) a munkakönyvtár. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy könnyen használható, többplatformos szerkesztő.
2. Keresse meg a `<ClaimsProviders>` szakasz
3. Adja hozzá a következő XML-részletet a `ClaimsProviders` elemet, és cserélje le `identityProvider` a DNS-kiszolgálót (tetszőleges érték, amely azt jelzi, hogy a tartomány), és mentse a fájlt. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Az AD FS fiók jogcímszolgáltató bejelentkezési regisztrálásához fel, vagy jelentkezzen be a felhasználói interakciósorozat
Ezen a ponton az identitásszolgáltató be lett állítva.  Azonban ez nem érhető összes regisztrálási-regisztrálási vagy bejelentkezési képernyőt. Most adja hozzá az AD FS-fiók identitásszolgáltató a felhasználónak kell `SignUpOrSignIn` felhasználói interakciósorozat. Elérhető legyen, létrehozunk egy meglévő sablon felhasználói interakciósorozat másolatát.  Ezután módosítjuk, így az AD FS identitásszolgáltató tartalmazza:

>[!NOTE]
>Ha korábban kimásolt a `<UserJourneys>` elem a szabályzat alapszintű fájlból a kiterjesztésű fájlt (TrustFrameworkExtensions.xml) ezt a szakaszt kihagyhatja.

1.  Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
2.  Keresse meg a `<UserJourneys>` elemet, és másolja a teljes tartalmát `<UserJourneys>` csomópont.
3.  Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adjon hozzá egyet.
4.  Illessze be a teljes tartalmát `<UserJournesy>` csomópont gyermekeként kimásolt a `<UserJourneys>` elemet.

### <a name="display-the-button"></a>A gomb megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját.  `<ClaimsProviderSelection>` a elem egy identity provider gombjára egy regisztrálási-regisztrálási vagy bejelentkezési oldal hasonló. Ha hozzáad egy `<ClaimsProviderSelection>` elem ADFS-fiók, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadása:

1.  Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a másolt felhasználói interakciósorozat.
2.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`
3.  Adja hozzá az alábbi kódrészletet XML `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot AD FS-fiókkal. A gomb összekapcsolása egy műveletet a technikai profil összekapcsolásának az ADFS-fiók jogcímszolgáltatótól:

1.  Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá az alábbi kódrészletet XML `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Győződjön meg, hogy a `Id` , ugyanazzal az értékkel rendelkezik `TargetClaimsExchangeId` az előző szakaszban.
> * Győződjön meg, hogy `TechnicalProfileReferenceId` van beállítva a technikai profil létrehozott korábbi (Contoso-egy SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>A szabályzat feltöltése a bérlőhöz
1.  Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **Azure AD B2C-vel** panelen.
2.  Válassza ki **identitás-kezelőfelületi keretrendszer**.
3.  Nyissa meg a **összes szabályzat** panelen.
4.  Válassza ki **szabályzat feltöltése**.
5.  Ellenőrizze **szabályzat felülírása, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy azt érvényesítése nem hiúsul meg a

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a Futtatás most
1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.
2.  Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.
3.  Jelentkezhet be az ADFS-fiók használatával kell lennie.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Opcionális] Az AD FS fiók jogcímszolgáltató Profilmódosítás felhasználói interakciósorozat regisztrálása
Az AD FS-fiók identitásszolgáltató hozzáadása a felhasználói is érdemes `ProfileEdit` felhasználói interakciósorozat. Elérhető legyen, hogy az utolsó két lépést ismételje meg:

### <a name="display-the-button"></a>A gomb megjelenítése
1.  Nyissa meg a szabályzat (például TrustFrameworkExtensions.xml) a kiterjesztésű fájlt.
2.  Keresse meg a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"` a másolt felhasználói interakciósorozat.
3.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"`
4.  Adja hozzá az alábbi kódrészletet XML `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
1.  Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá az alábbi kódrészletet XML `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Az egyéni Profilmódosítás házirend tesztelése a Futtatás most
1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.
3.  Jelentkezhet be az ADFS-fiók használatával kell lennie.

## <a name="download-the-complete-policy-files"></a>A teljes-fájlok letöltése
Választható lehetőség: Javasoljuk, hogy a forgatókönyv a saját egyéni házirend végig fájlok elvégezte az első lépéseket az egyéni szabályzatok használatával létrehozhat. [A házirend mintafájlok csak referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
