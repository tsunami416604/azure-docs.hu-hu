---
title: Az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések |} A Microsoft Docs
description: Az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések útmutató.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 36fad697758273246d567dfa1010f0e6bfc68939
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344562"
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Az Azure Active Directory B2C: Egyéni szabályzatok – első lépések

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Miután elvégezte a cikkben ismertetett lépések, az egyéni házirend támogatja-e a "helyi fiók" regisztrálási vagy bejelentkezési keresztül egy e-mail címet és jelszót. Is előkészítheti a környezetet az Identitásszolgáltatók (például Facebook vagy Azure Active Directory) hozzáadása. Javasoljuk, hogy más használatát az Azure Active Directory (Azure AD) B2C identitás-kezelőfelületi keretrendszer elolvasása előtt a lépések végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy egy Azure AD B2C-bérlő, amely minden felhasználó, alkalmazások, szabályzatok és egyéb tárolója. Ha Ön nem rendelkezik ilyennel, akkor [létrehozása az Azure AD B2C-bérlő](active-directory-b2c-get-started.md). Azt javasoljuk minden fejlesztők számára az Azure AD B2C-vel beépített szabályzat Kalauzok és alkalmazásaikat konfigurálása előtt beépített házirendek segítségével. Az alkalmazások munkakörnyezetben kismérvű változás meghívni az egyéni házirendet a házirend nevére, és mindkét típusú szabályzatok működnek.

>[!NOTE]
>Eléréséhez, egyéni szabályzat szerkesztéséhez, bérlőjének kapcsolt érvényes Azure-előfizetéssel kell rendelkeznie. Ha még nem [kapcsolódó Azure-előfizetés az Azure AD B2C-bérlő](active-directory-b2c-how-to-enable-billing.md) vagy az Azure-előfizetés le van tiltva, az identitás-kezelőfelületi keretrendszer gomb nem érhető el.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>A B2C-bérlőre való használatra egyéni szabályzatok aláírási és titkosítási kulcsok hozzáadása

1. Nyissa meg a **identitás-kezelőfelületi keretrendszer** az Azure AD B2C-bérlői beállítások paneljén.
2. Válassza ki **Szabályzatbejegyzések** elérhető a bérlői kulcsok megtekintéséhez.
3. Ha még nem létezik, hozza létre B2C_1A_TokenSigningKeyContainer:<br>
    a. Válassza a **Hozzáadás** lehetőséget. <br>
    b. Válassza ki **készítése**.<br>
    c. A **neve**, használjon `TokenSigningKeyContainer`. <br> 
    Az előtag `B2C_1A_` automatikusan hozzáadhatók.<br>
    d. A **kulcs típusa**, használjon **RSA**.<br>
    e. A **dátumok**, használja az alapértelmezett értékeket. <br>
    f. A **kulcshasználat**, használjon **aláírás**.<br>
    g. Kattintson a **Létrehozás** gombra.<br>
4. Ha még nem létezik, hozza létre B2C_1A_TokenEncryptionKeyContainer:<br>
 a. Válassza a **Hozzáadás** lehetőséget.<br>
 b. Válassza ki **készítése**.<br>
 c. A **neve**, használjon `TokenEncryptionKeyContainer`. <br>
   Az előtag `B2C_1A`_ automatikusan hozzáadhatók.<br>
 d. A **kulcs típusa**, használjon **RSA**.<br>
 e. A **dátumok**, használja az alapértelmezett értékeket.<br>
 f. A **kulcshasználat**, használjon **titkosítási**.<br>
 g. Kattintson a **Létrehozás** gombra.<br>
5. Hozzon létre B2C_1A_FacebookSecret. <br>
Ha már rendelkezik egy Facebook-alkalmazás titkos kulcsot, adja hozzá a szabályzat kulcsaként a bérlőn. Ellenkező esetben létre kell hoznia a kulcsot a helyőrző értékét az, hogy a szabályzatok érvényesíteni.<br>
 a. Válassza a **Hozzáadás** lehetőséget.<br>
 b. A **beállítások**, használjon **manuális**.<br>
 c. A **neve**, használjon `FacebookSecret`. <br>
 Az előtag `B2C_1A_` automatikusan hozzáadhatók.<br>
 d. Az a **titkos** mezőben adja meg a FacebookSecret developers.facebook.com vagy `0` helyettesíti. *Ez a nem a Facebook app ID azonosítójával.* <br>
 e. A **kulcshasználat**, használjon **aláírás**. <br>
 f. Válassza ki **létrehozás** és létrehozásának megerősítése.

## <a name="register-identity-experience-framework-applications"></a>Identitás-kezelőfelületi keretrendszer-alkalmazások regisztrálását

Az Azure AD B2C megköveteli, hogy a regisztráció és bejelentkezés a felhasználókat a motor által használt két további alkalmazások regisztrálását.

>[!NOTE]
>Létre kell hoznia két alkalmazás helyi fiókok használatát, hogy engedélyezze bejelentkezési: IdentityExperienceFramework (webalkalmazás) és ProxyIdentityExperienceFramework (egy natív alkalmazást) a delegált engedély a IdentityExperienceFramework alkalmazásból. Helyi fiókok csak a bérlő szerepel. A felhasználói Regisztrálás az egyedi e-mail cím és jelszó kombinációja a bérlő regisztrált alkalmazások eléréséhez.

### <a name="create-the-identityexperienceframework-application"></a>IdentityExperienceFramework-alkalmazás létrehozása

1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md).
2. Nyissa meg a **Azure Active Directory** panel (nem a **Azure AD B2C-vel** panelen). Előfordulhat, hogy ki kell választania **további szolgáltatások** is megkeresheti.
3. Válassza az **Alkalmazásregisztrációk** elemet.
4. Válassza az **Új alkalmazás regisztrálása** elemet.
   * A **neve**, használjon `IdentityExperienceFramework`.
   * A **alkalmazástípus**, használjon **Web app és az API**.
   * A **bejelentkezési URL-**, használjon `https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com`, ahol `yourtenant` van az Azure AD B2C-bérlői tartomány neve.
5. Kattintson a **Létrehozás** gombra.
6. Miután elkészült, válassza ki az újonnan létrehozott alkalmazást **IdentityExperienceFramework**.<br>
   * Válassza ki **tulajdonságok**.<br>
   * Másolja az Alkalmazásazonosítót, és mentse későbbi használatra.

### <a name="create-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework-alkalmazás létrehozása

1. Válassza az **Alkalmazásregisztrációk** elemet.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
   * A **neve**, használjon `ProxyIdentityExperienceFramework`.
   * A **alkalmazástípus**, használjon **natív**.
   * A **átirányítási URI-t**, használjon `https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com`, ahol `yourtenant` van az Azure AD B2C-bérlőben.
1. Kattintson a **Létrehozás** gombra.
1. Miután létrejött, válassza ki az alkalmazást **ProxyIdentityExperienceFramework**.<br>
   * Válassza ki **tulajdonságok**. <br>
   * Másolja az Alkalmazásazonosítót, és mentse későbbi használatra.
1. Válassza ki **szükséges engedélyek**.
1. Válassza a **Hozzáadás** lehetőséget.
1. Kattintson az **API kiválasztása** gombra.
1. Keresse meg a IdentityExperienceFramework nevét. Válassza ki **IdentityExperienceFramework** az eredményeket, majd kattintson a **kiválasztása**.
1. Jelölje be a **hozzáférés IdentityExperienceFramework**, és kattintson a **kiválasztása**.
1. Válassza a **Done** (Kész) lehetőséget.
1. Válassza ki **engedélyek megadása**, majd erősítse meg a kiválasztásával **Igen**.

## <a name="download-starter-pack-and-modify-policies"></a>Kezdőcsomag letöltése és szabályzatok módosítása

Egyéni szabályzatok olyan XML-fájlokat, amelyek fel kell tölteni az Azure AD B2C-bérlővel. Ahhoz, hogy gyorsabban alapszintű csomagot biztosítunk. A következő listában található minden egyes alapszintű csomag a legkevesebb technikai profilok és a leírt forgatókönyvek eléréséhez szükséges felhasználói utak tartalmazza:
 * LocalAccounts. Csak a helyi fiókok használatát teszi lehetővé.
 * SocialAccounts. Csak a közösségi (vagy összevont) fiókok használatát teszi lehetővé.
 * **SocialAndLocalAccounts**. A forgatókönyv használjuk ezt a fájlt.
 * SocialAndLocalAccountsWithMFA. Ide tartoznak a társadalombiztosítási azonosító, a helyi és a multi-factor Authentication szolgáltatás.

Minden egyes alapszintű csomag tartalmazza:

* A [alap fájl](active-directory-b2c-overview-custom.md#policy-files) a szabályzat. A következő néhány módosítások szükségesek.
* A [bővítményfájl](active-directory-b2c-overview-custom.md#policy-files) a szabályzat.  Ez a fájl, ahol a legtöbb konfigurációs módosításokat.
* [Függő entitás fájlok](active-directory-b2c-overview-custom.md#policy-files) hívja meg az alkalmazás tevékenység-specifikus fájlok.

>[!NOTE]
>Az XML-szerkesztő támogatja, ha a fájlok, szemben, amely az alapszintű csomag gyökérkönyvtárában található TrustFrameworkPolicy_0.3.0.0.xsd XML-séma érvényesítése. XML-sémaérvényesítése hibák feltöltése előtt azonosítja.

 Első lépések:

1. Töltse le a Githubról active-directory-b2c-custom-policy-starterpack. [Töltse le a .zip fájlt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) vagy futtatása

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Nyissa meg a SocialAndLocalAccounts mappát.  Ebben a mappában (TrustFrameworkBase.xml) alapszintű fájl tartalma a helyi és a közösségi vagy vállalati fiókok esetében szükséges. A közösségi tartalmat ne zavarja meg a helyi fiókok és a lépéseket.
3. Nyissa meg a TrustFrameworkBase.xml. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy könnyen használható, többplatformos szerkesztő.
4. A legfelső szintű `TrustFrameworkPolicy` elem, frissítse a `TenantId` és `PublicPolicyUri` attribútumok, és cserélje le `yourtenant.onmicrosoft.com` a tartomány nevét az Azure AD B2C-bérlő:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` a házirend nevére a portálon látható, és a neve, amely szerint ez a házirend a fájl egyéb házirend-fájlok hivatkozik.

5. Mentse a fájlt.
6. Nyissa meg a TrustFrameworkExtensions.xml. Az ugyanazon a két módosítást lecserélésével `yourtenant.onmicrosoft.com` az Azure AD B2C-bérlőben. Győződjön meg arról, az azonos váltja fel a `<TenantId>` elem három módosítások összesen. Mentse a fájlt.
7. Nyissa meg a SignUpOrSignIn.xml. Győződjön meg arról, a módosításokat, és cserélje le a `yourtenant.onmicrosoft.com` három helyen az Azure AD B2C-bérlőben. Mentse a fájlt.
8. Nyissa meg a jelszó-visszaállítás, és fájlokat szerkeszthet profilt. Győződjön meg arról, a módosításokat, és cserélje le a `yourtenant.onmicrosoft.com` az Azure AD B2C-bérlőben az egyes fájlok három helyen. Mentse a fájlokat.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Az alkalmazás-azonosítók hozzáadása az egyéni házirend
Vegye fel az alkalmazásazonosítót a bővítmények fájlba (`TrustFrameworkExtensions.xml`):

1. A bővítmények fájlt (TrustFrameworkExtensions.xml), keresse meg az elem `<TechnicalProfile Id="login-NonInteractive">`.
2. Cserélje le mindkét példányát `IdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer alkalmazás, amelyet korábban hozott létre az alkalmazás azonosítójával. Például:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Cserélje le mindkét példányát `ProxyIdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer Proxy alkalmazás, amelyet korábban hozott létre az alkalmazás azonosítójával.
4. Mentse a bővítmények fájlt.

## <a name="upload-the-policies-to-your-tenant"></a>Töltse fel a házirendeket a bérlőhöz

1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **Azure AD B2C-vel** panelen.
2. Válassza ki **identitás-kezelőfelületi keretrendszer**.
3. Válassza ki **szabályzat feltöltése**.

    >[!WARNING]
    >Az egyéni házirend-fájlokat kell feltölteni a következő sorrendben:

1. Töltse fel a TrustFrameworkBase.xml.
2. Töltse fel a TrustFrameworkExtensions.xml.
3. Töltse fel a SignUpOrSignin.xml.
4. A többi házirend-fájlok feltöltése.

A fájl feltöltésekor előtaggal kiegészített-e a házirend-fájl neve `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a Futtatás most

1. Nyissa meg **Azure AD B2C-beállítások** , majd **identitás-kezelőfelületi keretrendszer**.

   >[!NOTE]
   >**Futtatás most** kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. A B2C-bérlőben, keresztül alkalmazásokat regisztrálni kell a **alkalmazások** menü kiválasztása az Azure AD B2C-ben vagy az identitás-kezelőfelületi keretrendszer meghívni a beépített és az egyéni szabályzatok használatával. Csak egy regisztrációs alkalmazásonként van szükség.<br><br>
   Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.  

2. Nyissa meg a B2C_1A_signup_signin, a függő entitásonkénti (RP) egyéni-szabályzattal, a feltöltött. Válassza ki **Futtatás most**.

3. Regisztráció e-mail-címmel kell lennie.

4. Jelentkezzen be ugyanazzal a fiókkal, győződjön meg arról, hogy rendelkezik-e a megfelelő konfigurációt.

>[!NOTE]
>A sikertelen bejelentkezési gyakori oka egy helytelenül konfigurált IdentityExperienceFramework alkalmazást.


## <a name="next-steps"></a>További lépések

### <a name="add-facebook-as-an-identity-provider"></a>Adja hozzá a Facebook identitás-szolgáltatóként
Facebook beállítása:
1. [Egy Facebook-alkalmazás konfigurálása a developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [A Facebook-alkalmazás titkos kód hozzáadása az Azure AD B2C-bérlő](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. A TrustFrameworkExtensions házirend fájlban cserélje le a értékét `client_id` Facebook Alkalmazásazonosítóval rendelkező:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Töltse fel a TrustFrameworkExtensions.xml házirendfájl a bérlőn.
5. A teszt **Futtatás most** vagy közvetlenül a regisztrált egy alkalmazást a szabályzat meghívásával.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adja hozzá az Azure Active Directory identitás-szolgáltatóként
A alap fájl már szerepel az első lépésekről szóló útmutatót tartalmaz néhány, a tartalom hozzáadása az egyéb identitás-szolgáltatóktól szükséges. Bejelentkezések beállításával kapcsolatos további információkért lásd: a [Azure Active Directory B2C: Jelentkezzen be az Azure AD-fiókok használatával](active-directory-b2c-setup-aad-custom.md) cikk.

Egyéni házirendek az Azure AD B2C-t használhatja az identitáskezelési keretrendszert is, lásd a [Azure Active Directory B2C: egyéni szabályzatok](active-directory-b2c-overview-custom.md) cikk. 
