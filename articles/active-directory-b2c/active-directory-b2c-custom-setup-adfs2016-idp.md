---
title: AD FS hozzáadása egy SAML identitásszolgáltatótól az egyéni szabályzatok használatával az Azure Active Directory B2C |} A Microsoft Docs
description: Az SAML-protokoll és az egyéni szabályzatok használata az Azure Active Directory B2C AD FS 2016 beállítása
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669226"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>AD FS egyéni szabályzatok használatával az Azure Active Directory B2C egy SAML-identitásszolgáltató hozzáadása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése az AD FS felhasználói fiókjainak használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Adja hozzá az AD FS fiókkulcs alkalmazás Azure AD B2C-vel

Összevonás az AD FS-fiókkal használt fiók nevében az alkalmazás Azure AD B2C-vel megbízhatósági ügyfélkódot igényel. Az AD FS-tanúsítvány tárolása az Azure AD B2C-bérlő van szüksége. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Válassza ki **címtár váltása**, majd válassza a könyvtár, amely tartalmazza a létrehozott bérlőhöz. Ebben az oktatóanyagban a *contoso* könyvtárat használja, amely tartalmazza a bérlő nevű *contoso0522Tenant.onmicrosoft.com*.

    ![Könyvtár váltása](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t. Érdemes most már a bérlő használatával.
4. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer**.
5. Válassza ki **Szabályzatbejegyzések** elérhető a bérlői kulcsok megtekintése, és kattintson a **Hozzáadás**.
6. Válasszon **feltöltése** paramétert.
7. Adja meg `ADFSSamlCert` neve. Az előtag `B2C_1A_` automatikusan hozzáadhatók.
8. Keresse meg és válassza ki a tanúsítvány .pfx fájlját a titkos kulccsal. Ezt a tanúsítványt a titkos kulccsal azonosnak kell lennie, amely a kibocsátott, és használja az AD FS függő entitáshoz.
9. Kattintson a **létrehozás** győződjön meg arról, hogy létrehozott és a `B2C_1A_ADFSSamlCert` kulcsot.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>A bővítmény a házirend a jogcímeket szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be az ADFS-fiók használatával, definiálhatja a fiók egy jogcímszolgáltatótól szeretne. Ezt megteheti egy végpontot, amely az Azure AD B2C-vel kommunikálva megadásával. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít.

AD FS meghatározni egy jogcímszolgáltatótól, hozzáadásával **ClaimsProvider** elem a bővítmény a házirend fájlban.

1. Nyissa meg a *TrustFrameworkExtensions.xml* házirend fájlt a munkakönyvtárban. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), azaz egy könnyen használható, többplatformos szerkesztő.
2. Adja hozzá a következő XML formátumú, területen a **ClaimsProviders** elemet, és cserélje le **az AD FS-tartomány** a az AD FS-tartomány neve és értékét cserélje le a **identityProvider** kimeneti jogcím a DNS-kiszolgálót (tetszőleges érték, amely azt jelzi, hogy a tartomány), és mentse a fájlt. 

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
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>A regisztrációs és bejelentkezési jogcím-szolgáltató regisztrálása

Ahhoz, hogy az AD FS-fiók identitásszolgáltató érhető el a regisztrációs és bejelentkezési lapok, adja hozzá a kell a **SignUpOrSignIn** felhasználói interakciósorozat. 

Másolatot készít egy meglévő sablon felhasználói interakciósorozat, és módosíthatja azt, hogy az AD FS identitásszolgáltató tartalmazza:

>[!NOTE]
>Ha korábban kimásolt a **UserJourneys** elem a szabályzat alapszintű fájlból a bővítményfájl (*TrustFrameworkExtensions.xml*) ezt a szakaszt kihagyhatja.

1. Nyissa meg a szabályzat alapszintű fájlt. Ha például *TrustFrameworkBase.xml*.
2. A teljes tartalmának másolása a **UserJourneys** elemet.
3. Nyissa meg a kiterjesztésű fájlt (*TrustFrameworkExtensions.xml*), és illessze be a teljes tartalmát **UserJourneys** elem, amely a bővítményfájl másolt.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelections** elem definiálja a jogcímeket szolgáltató beállításokat és a sorrendjük listáját.  A **hiányzik a ClaimsProviderSelection** elem hasonlatos egy identity provider gombra egy regisztrációs és bejelentkezési oldalon. Ha hozzáad egy **hiányzik a ClaimsProviderSelection** eleme egy AD FS-fiókot, egy új gomb jelenik meg, amikor a felhasználó kap, az oldal. Ez az elem hozzáadása:

1. Az a **UserJourney** elem azonosítója, amelyet az `SignUpOrSignIn` a felhasználói utak kimásolt, keresse meg a **OrchestrationStep** eleme `Order="1"`.
2. Adjon hozzá következő **hiányzik a ClaimsProviderSelection** elemet a **ClaimsProviderSelections** elem:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot AD FS-fiókkal. A gomb összekapcsolása egy műveletet a technikai profil összekapcsolásának az ADFS-fiók jogcímszolgáltatótól:

1. Keresse meg a **OrchestrationStep** , `Order="2"` alatt a **UserJourney** elemet.
2. Adjon hozzá következő **ClaimsExchange** elemet a **ClaimsExchanges** elem:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Győződjön meg arról, hogy a `Id` , ugyanazzal az értékkel rendelkezik `TargetClaimsExchangeId` az előző szakaszban.
> * Győződjön meg arról, hogy a `TechnicalProfileReferenceId` van beállítva a technikai profil létrehozott korábbi (Contoso-egy SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Opcionális] Profil szerkesztése a jogcím-szolgáltató regisztrálása

Érdemes azt is, az AD FS-fiók identitásszolgáltató hozzáadása a profil szerkesztése felhasználói interakciósorozat.

### <a name="display-the-button"></a>A gomb megjelenítése

1. Nyissa meg a szabályzat a kiterjesztésű fájlt. Ha például *TrustFrameworkExtensions.xml*.
2. Az a **UserJourney** azonosítójú elem `ProfileEdit` a felhasználói utak kimásolt, keresse meg a **OrchestrationStep** eleme `Order="1"`.
3. Adjon hozzá következő **hiányzik a ClaimsProviderSelection** elemet **ClaimsProviderSelections** elem:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

1. Keresse meg a **OrchestrationStep** , `Order="2"` alatt a **UserJourney** elemet.
2. Adjon hozzá következő **ClaimsExchange** elemet a **ClaimsExchanges** elem:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>A szabályzat feltöltése a bérlőhöz

1. Az Azure Portalon válassza ki a **összes szabályzat**.
2. Válassza ki **szabályzat feltöltése**.
3. Engedélyezése **szabályzat felülírása, ha létezik**.
4. Keresse meg és válassza a *TrustFrameworkExtensions.xml* szabályzatot tartalmazó fájlt, és válassza ki **feltöltése**. Győződjön meg arról, hogy az ellenőrzés sikeres.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurálja az AD FS megbízható függő entitás

AD FS használata Identitásszolgáltatóként az Azure AD B2C-ben, szeretne egy AD FS megbízható függő entitás megbízhatóságának létrehozása az Azure AD B2C-vel SAML-metaadatokat. Az alábbi példa bemutatja egy Azure AD B2C-vel technikai profil SAML-metaadataira mutató URL-címet:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **a bérlő** az Ön bérlőneve, például a tenant.onmicrosoft.com.
- **a szabályzat** a házirend neve. Használja a házirend, konfigurálhatja a SAML-alapú technikai profilban szolgáltató, vagy egy szabályzatot, amely örökli az adott házirendnek.
- **a technikai profil** együtt a SAML identity provider technikai profil neve.
 
Nyisson meg egy böngészőt, és keresse meg az URL-címet. Győződjön meg róla, írja be a helyes URL-CÍMÉT, és érheti el az XML-metaadatait tartalmazó fájl.

Adjon hozzá egy új függőentitás-megbízhatóságot az AD FS kezelő beépülő modul használatával, és manuálisan adja meg a beállításokat, hajtsa végre az alábbi eljárás egy összevonási kiszolgálón. Tagság a **rendszergazdák** vagy a helyi számítógépen megfelelője a művelet végrehajtásához szükséges. Olvashat a megfelelő fiókok és csoporttagságok [helyi és tartományi alapértelmezett csoportok](http://go.microsoft.com/fwlink/?LinkId=83477).

1. A Kiszolgálókezelőben válasza **eszközök**, majd válassza ki **AD FS felügyeleti**.
2. Válassza ki **függő entitás megbízhatóságának hozzáadása**.
3. Az a **üdvözlő** lapon a **jogcímeket figyelembe**, és kattintson a **Start**.
4. Az a **adatforrás kiválasztása** lapra, jelölje be **függő entitással kapcsolatos adatok importálása online vagy helyi hálózaton közzétenni**, adja meg az Azure AD B2C-metaadatok URL-CÍMÉT, és kattintson **következő**.
5. Az a **megjelenítendő név megadása** lap, adja meg egy **megjelenített név**alatt **megjegyzések**, adjon meg egy leírást a függő entitás megbízhatóságához, és kattintson **következő**.
6. Az a **válassza a hozzáférés-vezérlési házirend** lapon válasszon ki egy szabályzatot, és kattintson a **tovább**.
7. Az a **készen áll a megbízhatóság hozzáadására** lapon tekintse át a beállításokat, és kattintson a **tovább** , mentse a függő entitás megbízhatóságának adatait.
8. A a **Befejezés** kattintson **Bezárás**, ez a művelet automatikusan megjeleníti a **Jogcímszabályok szerkesztése** párbeszédpanel bezárásához.
9. Válassza ki **szabály hozzáadása**.  
10. A **Jogcímszabály-sablon**válassza **LDAP attribútumok küldése jogcímekként**.
11. Adjon meg egy **Jogcímszabály neve**. Az a **attribútumtár**válassza **válassza ki az Active Directory**, adja hozzá a következő jogcímeket, majd kattintson a **Befejezés** és **OK**.

    ![A szabály tulajdonságainak beállítása](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  A tanúsítvány típusa alapján, szükség lehet a KIVONATOLÓ algoritmus beállítása. A függő entitás megbízhatósági (B2C bemutató) tulajdonságai ablakban válassza ki a **speciális** lapon, és módosítsa a **biztonságos kivonatoló algoritmus** való `SHA-1` vagy `SHA-256`, és kattintson a **Ok**.  

### <a name="update-the-relying-party-metadata"></a>Frissítés a függő entitás-metaadatok

SAML-technikaiprofilban módosítása szükséges, hogy frissítse az AD FS a frissített metaadatokat verziójával. Nem kell frissíteni a metaadatokat, amikor a függő entitás alkalmazást hoz létre, de amikor módosítja, az AD FS metaadatok frissíti.

1. A Kiszolgálókezelőben válasza **eszközök**, majd válassza ki **AD FS felügyeleti**.
2. Válassza ki a függő entitás megbízhatóságához hozott létre, jelölje be **összevonási metaadatokat frissítés**, és kattintson a **frissítés**. 

### <a name="test-the-policy-by-using-run-now"></a>A szabályzat teszteléséhez használja a Futtatás most

1.  Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.
2.  Nyissa meg **B2C_1A_ProfileEdit**, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**. Jelentkezhet be az ADFS-fiók használatával kell lennie.

## <a name="download-the-complete-policy-files"></a>A teljes-fájlok letöltése

Választható lehetőség: Hozhat létre saját egyéni házirend-fájlok használata a lépések végrehajtása után a forgatókönyvtől [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md). Például a fájlok, lásd: [házirend mintafájlok csak referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
