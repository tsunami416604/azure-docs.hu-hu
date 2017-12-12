---
title: "Az Azure Active Directory B2C: Adja hozzá az AD FS-t egyéni házirendekkel SAML-Identitásszolgáltatóként"
description: "A SAML protokoll és az egyéni házirendek használata az AD FS 2016 beállításával kapcsolatos cikkben található útmutató"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 22b360aec8878925ebe8d2c67c76d275a42ca7a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Adja hozzá az AD FS-t egyéni házirendekkel SAML-Identitásszolgáltatóként

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés az AD FS-fiókból keresztül a felhasználók engedélyezése [egyéni házirendek](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

Ezek a lépések az alábbiak:

1.  Az AD FS megbízható függő entitás megbízhatóságának létrehozása.
2.  Az AD FS megbízható függő entitás megbízhatóságának tanúsítvány felvétele az Azure AD B2C.
3.  Jogcím-szolgáltató házirendhez való hozzáadása.
4.  Az AD FS regisztrálása fiók jogcímek szolgáltatót, amelyet a felhasználó út.
5.  A házirend feltöltése az Azure AD B2C bérlői és tesztelik azt.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>A jogcímbarát függő entitás megbízhatóságának létrehozása

Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként az AD FS használatához meg kell egy AD FS megbízható függő entitás megbízhatóságának létrehozása, és adja meg azt a megfelelő paraméterekkel.

Új függő entitás megbízhatóságának hozzáadása az AD FS kezelő beépülő modul használatával, és a beállítások manuális konfigurálásával, hajtsa végre az alábbi eljárás egy összevonási kiszolgálón.

Tagság a **rendszergazdák**, vagy ezzel egyenértékű a helyi számítógépen kell lennie a művelet végrehajtásához. Olvashat a megfelelő fiókok és csoporttagságok [alapértelmezett helyi és tartományi csoportok](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  A Kiszolgálókezelőben kattintson **eszközök**, majd válassza ki **az AD FS felügyeleti**.

2.  Kattintson a **függő entitás megbízhatóságának hozzáadása**.
    ![Függő entitás megbízhatóságának hozzáadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Az a **üdvözlő** lapon, válassza ki **kompatibilis jogcím** kattintson **Start**.
    ![Üdvözli a lapon választhatja ki jogcímeket kompatibilis](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Az a **adatforrás kiválasztása** kattintson **függő entitással kapcsolatos adatok manuális megadása**, és kattintson a **következő**.
    ![Adja meg a függő entitással kapcsolatos adatok](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Az a **megjelenített név meghatározása** írja be egy nevet a **megjelenített név**a **megjegyzések** írja be a függő entitás megbízhatóságának leírását, és kattintson **következő**.
    ![Adja meg a megjelenítendő nevek és megjegyzések](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Választható. Ha rendelkezik egy választható jogkivonat-titkosítási tanúsítványt, majd a **tanúsítvány konfigurálása** lapján kattintson **Tallózás** keresse meg a tanúsítványfájlt, majd **következő**.
    ![Tanúsítvány konfigurálása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Az a **URL konfigurálása** lapon jelölje be a **a SAML 2.0 WebSSO-protokoll támogatásának engedélyezése** jelölőnégyzetet. A **függő entitás SAML 2.0 SSO szolgáltatás URL-címe**, írja be a függő entitás megbízhatóságának a Security Assertion Markup Language (SAML) szolgáltatás végpont URL-CÍMÉT, és kattintson a **következő**.  Az a **függő entitás SAML 2.0 SSO szolgáltatás URL-címe**, illessze be a `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. {Tenant} cserélje le a bérlő neve (például contosob2c.onmicrosoft.com), és a {házirend} cserélje le a bővítményeket házirend nevét (például B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >A házirend nevére, amelyet a signup_or_signin házirend örököl, ebben az esetben: `B2C_1A_TrustFrameworkExtensions`.
    >Az URL-cím lehet például: https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Függő entitás SAML 2.0 SSO URL-címe](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Az a **azonosítók konfigurálása** lapon adja meg az előző lépésben leírt, azonos URL-CÍMÉT, és kattintson **Hozzáadás** vegye fel őket a listára, majd **következő**.
    ![Függő entitások megbízhatósági azonosítóinak](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Az a **hozzáférés-vezérlési szabályzat kiválasztása** jelöljön ki egy házirendet, majd **következő**.
    ![Válassza ki a hozzáférés-vezérlési házirend](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Az a **készen áll a megbízhatóság hozzáadására** lapon tekintse át a beállításokat, és kattintson a **következő** menteni a függő entitás megbízhatóságának adatait.
    ![Mentse a függő entitás megbízhatósági adatok](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Az a **Befejezés** kattintson **Bezárás**, ez a művelet automatikusan megjeleníti a **Jogcímszabályok szerkesztése** párbeszédpanel.
    ![Jogcímszabályok szerkesztése](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Kattintson a **szabály hozzáadása**.  
      ![Új szabály hozzáadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  A **Jogcímszabály-sablon**, jelölje be **LDAP attribútumok küldése jogcímekként**.
    ![Válassza ki az LDAP attribútumok küldése jogcímszabályként sablon](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Adjon meg **Jogcímszabály nevének**. Az a **attribútumtár** válasszon **válassza ki az Active Directory** a következő jogcímeket adhatnak hozzá, majd kattintson az **Befejezés** és **OK**.
    ![A szabály tulajdonságainak beállítása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  A Kiszolgálókezelő ablakában válassza **függő entitás Megbízhatóságai** , majd válassza a függő entitás létrehozott megbízható, és kattintson a **tulajdonságok**.
    ![Függő entitás tulajdonságok szerkesztése](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  A függő entitás megbízhatósági (B2C bemutató) tulajdonságai ablakban kattintson egy **aláírás** fülre, és kattintson **Hozzáadás**.  
    ![Set-aláírás](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Adja hozzá az aláírási tanúsítvány (.cert fájlt, titkos kulcs nélkül).  
    ![Az aláírási tanúsítvány hozzáadása](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  A függő entitás megbízhatósági (B2C bemutató) tulajdonságai ablakban kattintson a **speciális** lapon, és módosítsa a **biztonságos kivonatoló algoritmus** való **SHA-1**, kattintson a **Ok**.  
    ![Állítsa be a biztonságos kivonatoló algoritmus az SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Az AD FS fiókkulcs alkalmazás hozzáadása az Azure AD B2C
Összevonás az AD FS-fiókok egy ügyfélkulcsot szükség van az AD FS fiók nevében az alkalmazás az Azure AD B2C megbízhatóságának. Az Azure AD B2C-bérlő tárolja az AD FS-tanúsítványt kell. 

1.  Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C beállítások** > **identitás élmény keretrendszer**
2.  Válassza ki **házirend kulcsok** érhető el a bérlői kulcsok megtekintéséhez.
3.  Kattintson a **+ Hozzáadás**.
4.  A **beállítások**, használjon **feltöltése**.
5.  A **neve**, használjon `ADFSSamlCert`.  
    Az előtag `B2C_1A_` előfordulhat, hogy automatikusan hozzáadja.
6.  A fájl feltöltése ** válassza ki a .pfx-fájl titkos kulccsal. Megjegyzés: ezt a tanúsítványt (a titkos kulccsal) meg kell egyeznie egy kiállított és az AD FS függő entitáshoz használt.
![Töltse fel a házirend-kulcs](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Kattintson a **Create** (Létrehozás) gombra
8.  Győződjön meg arról, hogy létrehozta a kulcs `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A jogcím-szolgáltató hozzáadása a bővítmény házirend
Ha azt szeretné, hogy a felhasználók az AD FS-fiókkal bejelentkezni, meg kell egy jogcímszolgáltatótól, az AD FS fiók megadása. Ez azt jelenti meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont a jogcímek, az Azure AD B2C által használt győződjön meg arról, hogy egy adott felhasználó engedélyezést biztosít.

Az AD FS meghatározni, a Jogcímszolgáltatók hozzáadásával `<ClaimsProvider>` csomópont a bővítmény a házirend-fájlban:

1. Nyissa meg a házirend bővítményfájl (TrustFrameworkExtensions.xml) a munkakönyvtárat. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy egyszerű platformfüggetlen-szerkesztőben.
2. Keresés a `<ClaimsProviders>` szakasz
3. A következő XML-részletet alatt adja hozzá a `ClaimsProviders` elem és a név felülírandó `identityProvider` a DNS-beli (tetszőleges érték, amely jelzi a tartomány), és mentse a fájlt. 

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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Az AD FS fiók jogcímszolgáltató bejelentkezési regisztrálásához fel, vagy jelentkezzen be a felhasználó út
Ezen a ponton az identitásszolgáltató beállítása.  Azonban nincs sem a sign-Close-Up/sign-in képernyők áll rendelkezésre. Az AD FS fiók identitásszolgáltató hozzáadása a felhasználói most `SignUpOrSignIn` felhasználói út. Tegye elérhetővé, azt hozzon létre egy meglévő sablon felhasználói út duplikált.  Majd hogy módosítsa, ez magában foglalja az AD FS identitásszolgáltató:

>[!NOTE]
>Ha korábban kimásolt a `<UserJourneys>` elem házirend alap fájlból a bővítményfájl (TrustFrameworkExtensions.xml) kihagyhatja ezt a részt.

1.  Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
2.  Keresés a `<UserJourneys>` elem és a teljes tartalmának másolása a `<UserJourneys>` csomópont.
3.  Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keresse a `<UserJourneys>` elemet. Ha az elem nem létezik, vegyen fel egyet.
4.  Illessze be a teljes tartalmát `<UserJournesy>` csomópont gyermekeként másolt a `<UserJourneys>` elemet.

### <a name="display-the-button"></a>A gomb megjelenítése
A `<ClaimsProviderSelections>` elem definiálja a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját.  `<ClaimsProviderSelection>`a elem egy identity provider gombra a sign-Close-Up/sign-in oldalán hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` elem az AD FS-fiókhoz, egy új gomb megjelenik, amikor egy felhasználó fájljai az oldalon. Ez az elem hozzáadása:

1.  Keresés a `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"` a a felhasználók utazás másolt.
2.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont`Order="1"`
3.  Adja hozzá a következő XML-részletet a `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy a gomb helyen, hogy egy művelet kapcsolódnia kell. A művelet, ebben az esetben, akkor az Azure AD B2C fogadhatnak jogkivonatot AD FS-fiók kommunikálni. A gomb művelet mutató hivatkozás létrehozása az AD FS fiók jogcímeket szolgáltató létrehozhatja, ha a műszaki profil:

1.  Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá a következő XML-részletet a `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Győződjön meg arról a `Id` ugyanazzal az értékkel, amely rendelkezik `TargetClaimsExchangeId` az előző szakaszban leírt.
> * Győződjön meg arról `TechnicalProfileReferenceId` be van állítva a műszaki profil létrehozott korábbi (a Contoso-egy SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>A házirend a bérlő feltöltése
1.  A a [Azure-portálon](https://portal.azure.com), átváltani a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **az Azure AD B2C** panelen.
2.  Válassza ki **identitás élmény keretrendszer**.
3.  Nyissa meg a **házirend** panelen.
4.  Válassza ki **házirend feltöltése**.
5.  Ellenőrizze **felülírja a házirendet, ha létezik** mezőbe.
6.  **Töltse fel** TrustFrameworkExtensions.xml, és győződjön meg arról, hogy az érvényesítése nem hiúsul meg az

## <a name="test-the-custom-policy-by-using-run-now"></a>Tesztelje az egyéni házirend segítségével futtatása most
1.  Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.
2.  Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.
3.  Az AD FS-fiókkal jelentkezhet kell lennie.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Választható] Az AD FS fiók jogcímszolgáltató profil szerkesztheti a felhasználói út regisztrálása
Érdemes lehet az AD FS fiók identitásszolgáltató is hozzáadása a felhasználói `ProfileEdit` felhasználói út. Tegye elérhetővé, hogy ismételje meg az utolsó két lépést:

### <a name="display-the-button"></a>A gomb megjelenítése
1.  Nyissa meg a bővítményfájl házirend (például TrustFrameworkExtensions.xml).
2.  Keresés a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"` a a felhasználók utazás másolt.
3.  Keresse meg a `<OrchestrationStep>` tartalmazó csomópont`Order="1"`
4.  Adja hozzá a következő XML-részletet a `<ClaimsProviderSelections>` csomópont:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet
1.  Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
2.  Adja hozzá a következő XML-részletet a `<ClaimsExchanges>` csomópont:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Tesztelje az egyéni házirend profil-módosítása segítségével futtatása most
1.  Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.
3.  Az AD FS-fiókkal jelentkezhet kell lennie.

## <a name="download-the-complete-policy-files"></a>A teljes házirend-fájlok letöltésére
Választható lehetőség: Javasoljuk készít a saját egyéni házirend fájlok az első lépések egyéni házirendekkel befejezése után végezze el a forgatókönyvet. [Házirend mintafájlok csak referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
