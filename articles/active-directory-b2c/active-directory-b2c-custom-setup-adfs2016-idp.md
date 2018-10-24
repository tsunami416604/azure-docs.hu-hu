---
title: AD FS hozzáadása egy SAML identitásszolgáltatótól az egyéni szabályzatok használatával az Azure Active Directory B2C |} A Microsoft Docs
description: Az SAML-protokoll és az egyéni szabályzatok használata az Azure Active Directory B2C AD FS 2016 beállítása
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6f7fced5163476dc1de866474484f98d546d1901
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945722"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>AD FS egyéni szabályzatok használatával az Azure Active Directory B2C egy SAML-identitásszolgáltató hozzáadása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése az AD FS felhasználói fiókjainak használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).
- Győződjön meg arról, hogy a tanúsítvány .pfx fájlját a titkos kulccsal, AD FS által kiállított elérhető lesz.

## <a name="create-a-policy-key"></a>Hozzon létre egy házirendjének kulcsa

Az AD FS-tanúsítvány tárolása az Azure AD B2C-bérlő van szüksége.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer – előzetes verzió**.
5. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
6. A **beállítások**, válassza a `Upload`.
7. Adjon meg egy **neve** a házirend-kulcs. Például: `ADFSSamlCert`. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
8. Keresse meg és válassza ki a tanúsítvány .pfx fájlját a titkos kulccsal.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>A jogcímeket szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be az ADFS-fiók használatával, definiálhatja a fiók, amely az Azure AD B2C-végponton keresztül kommunikálhat egy jogcímszolgáltatótól szeretne. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít. 

Definiálhat egy AD FS-fiókot, egy jogcímszolgáltatótól hozzáadásával, hogy a **ClaimsProviders** elem a bővítmény fájlban a szabályzat.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a legfelső szintű elem alatt.
3. Vegyen fel egy új **ClaimsProvider** módon:

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

4. Cserélje le `your-ADFS-domain` az AD FS-tartomány nevét és értékét cserélje le a **identityProvider** kimenő jogcímet a DNS-kiszolgálót (tetszőleges érték, amely azt jelzi, hogy a tartomány).
5. Mentse a fájlt.

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

Már konfigurálta a szabályzatot, hogy az Azure AD B2C-vel képes kommunikálni az ADFS-fiók. A szabályzat csak, győződjön meg arról, hogy minden problémát, amennyiben nem rendelkezik a bővítmény fájlt töltsön fel.

1. Az a **egyéni szabályzatok** az Azure AD B2C-bérlő, válassza a lap **szabályzat feltöltése**.
2. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrációs vagy bejelentkezési képernyőt. Elérhető legyen, hozzon létre egy meglévő sablon felhasználói interakciósorozat másolatát, és ezután módosítsa, hogy az AD FS identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* az alapszintű csomag fájlt.
2. Keresse meg és másolja ki a teljes tartalmát a **UserJourney** , amely tartalmazza az elem `Id="SignUpOrSignIn"`.
3. Nyissa meg a *TrustFrameworkExtensions.xml* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a teljes tartalmát a **UserJourney** gyermekeként kimásolt elem a **UserJourneys** elemet.
5. Nevezze át a felhasználói út azonosítója. Például: `SignUpSignInADFS`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **hiányzik a ClaimsProviderSelection** elem ehhez hasonló regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy **hiányzik a ClaimsProviderSelection** elem egy AD FS-fiók, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon.

1. Keresse meg a **OrchestrationStep** , amely tartalmazza az elem `Order="1"` az Ön által létrehozott felhasználói interakciósorozat.
2. A **ClaimsProviderSelections**, adja hozzá a következő elemet. Állítsa az értékét **TargetClaimsExchangeId** egy megfelelő értéket, például a `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C fogadhatnak jogkivonatot AD FS fiókkal folytatott kommunikációhoz.

1. Keresse meg a **OrchestrationStep** tartalmazó `Order="2"` a felhasználói interakciósorozatban szereplő.
2. Adja hozzá a következő **ClaimsExchange** gondoskodik róla, hogy ugyanazt az értéket használt elem **azonosító** során használt **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Frissítse az értéket a **TechnicalProfileReferenceId** , a **azonosító** a korábban létrehozott technikai profil. Például: `Contoso-SAML2`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.


## <a name="configure-an-adfs-relying-party-trust"></a>Az AD FS függőentitás-megbízhatóság konfigurálása

AD FS használata Identitásszolgáltatóként az Azure AD B2C-ben, szeretne egy AD FS megbízható függő entitás megbízhatóságának létrehozása az Azure AD B2C-vel SAML-metaadatokat. Az alábbi példa bemutatja egy Azure AD B2C-vel technikai profil SAML-metaadataira mutató URL-címet:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **a bérlő** az Ön bérlőneve, például a tenant.onmicrosoft.com.
- **a szabályzat** a házirend neve. Ha például B2C_1A_signup_signin_adfs.
- **a technikai profil** együtt a SAML identity provider technikai profil neve. Ha például Contoso-egy SAML2.
 
Nyisson meg egy böngészőt, és keresse meg az URL-címet. Győződjön meg róla, írja be a helyes URL-CÍMÉT, és érheti el az XML-metaadatait tartalmazó fájl. Adjon hozzá egy új függőentitás-megbízhatóságot az AD FS kezelő beépülő modul használatával, és manuálisan adja meg a beállításokat, hajtsa végre az alábbi eljárás egy összevonási kiszolgálón. Tagság a **rendszergazdák** vagy a helyi számítógépen megfelelője a művelet végrehajtásához szükséges.

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
12.  A tanúsítvány típusa alapján, szükség lehet a KIVONATOLÓ algoritmus beállítása. A függő entitás megbízhatósági (B2C bemutató) tulajdonságai ablakban válassza ki a **speciális** lapon, és módosítsa a **biztonságos kivonatoló algoritmus** való `SHA-1` vagy `SHA-256`, és kattintson a **Ok**.  
13. A Kiszolgálókezelőben válasza **eszközök**, majd válassza ki **AD FS felügyeleti**.
14. Válassza ki a függő entitás megbízhatóságához hozott létre, jelölje be **összevonási metaadatokat frissítés**, és kattintson a **frissítés**. 

## <a name="create-an-azure-ad-b2c-application"></a>Az Azure AD B2C-alkalmazás létrehozása

Kommunikáció az Azure AD B2C-vel hoz létre a bérlő alkalmazás keresztül történik. Ez a szakasz felsorolja a nem kötelező lépések is elvégezheti egy test-alkalmazás létrehozása, ha ezt még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adja meg egy nevet az alkalmazásnak, például *testapp1*.
6. A **Web App / Web API**, jelölje be `Yes`, majd adja meg `https://jwt.ms` a a **válasz URL-cím**.
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="update-and-test-the-relying-party-file"></a>Frissítse és a függő entitás fájl tesztelése

Frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárban, és nevezze át. Például nevezze át, hogy *SignUpSignInADFS.xml*.
2. Nyissa meg az új fájlt, és frissítse az értéket, a **PolicyId** az attribútum **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInADFS`.
3. Frissítse az értéket a **PublicPolicyUri** URI-a szabályzat. Például "http://contoso.com/B2C_1A_signup_signin_adfs" >
4. Frissítse az értéket, a **hivatkozásazonosító** attribútum **DefaultUserJourney** megfelelően (SignUpSignInADFS) létrehozott új felhasználói interakciósorozat azonosítója.
5. A módosítások mentéséhez, feltöltheti a fájlt, és válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C-alkalmazást az van kiválasztva, a **válassza ki az alkalmazás** mezőben, majd tesztelje kattintva **Futtatás most**.

