---
title: "Az Azure Active Directory B2C: Ismerkedés a egyéni szabályzatokkal |} Microsoft Docs"
description: "Ismerkedés az Azure Active Directory B2C egyéni házirendek"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 4f14dbf4b66f10290cd4f98d56a005f97cc6a207
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Az Azure Active Directory B2C: Egyéni házirendek első lépések

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Miután elvégezte a cikkben leírt lépéseket, az egyéni házirend támogatja-e a "helyi fiók" regisztráció vagy bejelentkezés keresztül egy e-mail címet és jelszót. Akkor is készítse elő a környezetet az identitás-szolgáltatóktól (például a Facebook- vagy Azure Active Directory) hozzáadása. Javasoljuk, hogy más célra az Azure Active Directory (Azure AD) B2C identitás élmény keretrendszer elolvasása előtt lépések végrehajtásának befejezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt ellenőrizze, hogy az Azure AD B2C bérlő számára, amely minden felhasználó, alkalmazások, szabályzatok és több tárolója. Ha egy már nem rendelkezik, akkor [az Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md). Határozottan javasoljuk az összes fejlesztők számára az Azure AD B2C beépített házirend végrehajtására, és az alkalmazások konfigurálása beépített házirendek segítségével a folytatás előtt. Az alkalmazások mindkét házirendtípusban együttműködve után módosítja kisebb lehet meghívni az egyéni házirend házirend nevét.

>[!NOTE]
>Egyéni házirend szerkesztése eléréséhez szüksége van egy csatolva a bérlő érvényes Azure-előfizetéshez. Ha még nem [Azure-előfizetéshez az Azure AD B2C-bérlő kapcsolódó](active-directory-b2c-how-to-enable-billing.md) vagy az Azure-előfizetés le van tiltva, az identitás élmény keretrendszer gomb nem érhető el.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>A B2C-bérlő számára az egyéni házirend aláírási és titkosítási kulcsok hozzáadása

1. Nyissa meg a **identitás élmény keretrendszer** panel az Azure AD B2C bérlő beállításaiban.
2. Válassza ki **házirend kulcsok** érhető el a bérlői kulcsok megtekintéséhez.
3. Ha még nem létezik, hozzon létre B2C_1A_TokenSigningKeyContainer:<br>
    a. Válassza a **Hozzáadás** lehetőséget. <br>
    b. Válassza ki **készítése**.<br>
    c. A **neve**, használjon `TokenSigningKeyContainer`. <br> 
    Az előtag `B2C_1A_` előfordulhat, hogy automatikusan hozzáadja.<br>
    d. A **kulcstípus**, használjon **RSA**.<br>
    e. A **dátumok**, használja az alapértelmezett értékeket. <br>
    f. A **kulcshasználat**, használjon **aláírás**.<br>
    g. Kattintson a **Létrehozás** gombra.<br>
4. Ha még nem létezik, hozzon létre B2C_1A_TokenEncryptionKeyContainer:<br>
 a. Válassza a **Hozzáadás** lehetőséget.<br>
 b. Válassza ki **készítése**.<br>
 c. A **neve**, használjon `TokenEncryptionKeyContainer`. <br>
   Az előtag `B2C_1A`_ előfordulhat, hogy automatikusan hozzáadja.<br>
 d. A **kulcstípus**, használjon **RSA**.<br>
 e. A **dátumok**, használja az alapértelmezett értékeket.<br>
 f. A **kulcshasználat**, használjon **titkosítási**.<br>
 g. Kattintson a **Létrehozás** gombra.<br>
5. Hozzon létre B2C_1A_FacebookSecret. <br>
Ha már rendelkezik egy Facebook alkalmazás titkos kulcs, adja hozzá a házirend kulcsként a bérlő. Ellenkező esetben létre kell hoznia a kulcsot egy helyőrző értékét úgy, hogy a házirendek érvényesíteni.<br>
 a. Válassza a **Hozzáadás** lehetőséget.<br>
 b. A **beállítások**, használjon **manuális**.<br>
 c. A **neve**, használjon `FacebookSecret`. <br>
 Az előtag `B2C_1A_` előfordulhat, hogy automatikusan hozzáadja.<br>
 d. Az a **titkos** mezőbe írja be a FacebookSecret a developers.facebook.com vagy `0` helyőrzőként. *Ez nem a Facebook app ID azonosítójával.* <br>
 e. A **kulcshasználat**, használjon **aláírás**. <br>
 f. Válassza ki **létrehozása** , majd erősítse meg a létrehozása.

## <a name="register-identity-experience-framework-applications"></a>Identitás élmény keretrendszer alkalmazások regisztrálása

Az Azure AD B2C meg, és a felhasználók bejelentkezését a motor által használt két további alkalmazások regisztrálni kell.

>[!NOTE]
>Létre kell hoznia két alkalmazás helyi fiókok használatát, hogy engedélyezze bejelentkezés: IdentityExperienceFramework (webalkalmazás) és ProxyIdentityExperienceFramework (a natív alkalmazással) a jogosultságot a IdentityExperienceFramework alkalmazásból. Helyi fiókok csak a bérlő szerepel. A felhasználók hozzáférhessenek a bérlő regisztrálva alkalmazásokhoz jelentkezzen egy egyedi e-mail cím/jelszó kombináció.

### <a name="create-the-identityexperienceframework-application"></a>A IdentityExperienceFramework alkalmazás létrehozása

1. Az a [Azure-portálon](https://portal.azure.com), átváltani a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md).
2. Nyissa meg a **Azure Active Directory** panel (nem a **az Azure AD B2C** panel). Előfordulhat, hogy kell kiválasztania **több szolgáltatások** megtalálni.
3. Válassza ki **App regisztrációk**.
4. Válassza ki **új alkalmazás regisztrációja**.
   * A **neve**, használjon `IdentityExperienceFramework`.
   * A **alkalmazástípus**, használjon **Web app/API**.
   * A **bejelentkezési URL-cím**, használjon `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, ahol `yourtenant` van az Azure AD B2C-bérlő tartománynevét.
5. Kattintson a **Létrehozás** gombra.
6. A már létrehozott, válassza ki az újonnan létrehozott alkalmazást **IdentityExperienceFramework**.<br>
   * Válassza ki **tulajdonságok**.<br>
   * Másolja az Alkalmazásazonosító, és elmenti későbbi használatra.

### <a name="create-the-proxyidentityexperienceframework-application"></a>A ProxyIdentityExperienceFramework alkalmazás létrehozása

1. Válassza ki **App regisztrációk**.
1. Válassza ki **új alkalmazás regisztrációja**.
   * A **neve**, használjon `ProxyIdentityExperienceFramework`.
   * A **alkalmazástípus**, használjon **natív**.
   * A **átirányítási URI-**, használjon `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, ahol `yourtenant` az Azure AD B2C-bérlő.
1. Kattintson a **Létrehozás** gombra.
1. Miután létrejött, válassza ki az alkalmazást **ProxyIdentityExperienceFramework**.<br>
   * Válassza ki **tulajdonságok**. <br>
   * Másolja az Alkalmazásazonosító, és elmenti későbbi használatra.
1. Válassza ki **szükséges engedélyek**.
1. Válassza a **Hozzáadás** lehetőséget.
1. Válassza ki **API kiválasztása**.
1. Keresse meg a IdentityExperienceFramework nevét. Válassza ki **IdentityExperienceFramework** az eredményeket, majd **válasszon**.
1. Jelölje be a jelölőnégyzetet a **hozzáférés IdentityExperienceFramework**, és kattintson a **válasszon**.
1. Válassza ki **végzett**.
1. Válassza ki **engedélyt adjon**, majd erősítse meg kiválasztásával **Igen**.

## <a name="download-starter-pack-and-modify-policies"></a>Alapszintű csomag letöltése, és módosítsa a házirendek

Egyéni házirendek olyan XML-fájlok, amelyek az Azure AD B2C-bérlő fel kell tölteni. Az első is gyorsan alapszintű csomagok nyújtunk. A következő listában található minden alapszintű csomag tartalmazza a legkevesebb műszaki profilok és a leírt forgatókönyvek eléréséhez szükséges felhasználói utak:
 * LocalAccounts. Csak a helyi fiókok használatát teszi lehetővé.
 * SocialAccounts. Csak a közösségi (vagy összevont) fiókok használatát teszi lehetővé.
 * **SocialAndLocalAccounts**. Ez a fájl a forgatókönyv használjuk.
 * SocialAndLocalAccountsWithMFA. Ide tartoznak a társadalombiztosítási, a helyi és a többtényezős hitelesítés beállításai.

Minden egyes alapszintű csomag tartalmazza:

* A [alap fájl](active-directory-b2c-overview-custom.md#policy-files) a házirend. A következő néhány módosítások szükségesek.
* A [bővítményfájl](active-directory-b2c-overview-custom.md#policy-files) a házirend.  A fájl, ahol a legtöbb konfigurációs módosításai.
* [Függő entitás fájlok](active-directory-b2c-overview-custom.md#policy-files) az alkalmazás által meghívott tevékenység-specifikus fájlok.

>[!NOTE]
>Az XML-szerkesztő támogatja az olyan, ha ellenőrizni a TrustFrameworkPolicy_0.3.0.0.xsd XML-séma, amely az alapszintű csomag gyökérkönyvtárában található fájlokat. XML-sémaérvényesítése feltöltés előtt észleli a hibákat.

 Lássunk neki:

1. Töltse le a Githubról active-directory-b2c-custom-policy-starterpack. [Töltse le a .zip fájlt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) vagy futtatása

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Nyissa meg a SocialAndLocalAccounts mappát.  A helyi és szociális vállalati fiókok szükséges tartalma (TrustFrameworkBase.xml) Alap fájl a mappában. A közösségi tartalom ne zavarja a helyi fiókok használatba lépéseit.
3. Nyissa meg a TrustFrameworkBase.xml. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy egyszerű platformfüggetlen-szerkesztőben.
4. A legfelső szintű `TrustFrameworkPolicy` elem, a frissítés a `TenantId` és `PublicPolicyUri` attribútumok cseréje `yourtenant.onmicrosoft.com` együtt az Azure AD B2C-bérlő tartománynevét:
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
   >`PolicyId`van a házirend nevére, melyek megjelennek a portálon, a neve, amellyel ez a házirend-fájl más házirendfájljait hivatkozik.

5. Mentse a fájlt.
6. Nyissa meg a TrustFrameworkExtensions.xml. Ugyanazokat a módosításokat két tartományvezérlőkkel történő lecserélésével `yourtenant.onmicrosoft.com` és az Azure AD B2C bérlő. Ellenőrizze az azonos váltja fel a `<TenantId>` elem három módosítások összesen. Mentse a fájlt.
7. Nyissa meg a SignUpOrSignIn.xml. Ugyanazokat a módosításokat tartományvezérlőkkel történő lecserélésével `yourtenant.onmicrosoft.com` és az Azure AD B2C bérlő három helyen. Mentse a fájlt.
8. Nyissa meg a jelszó alaphelyzetbe állítása és a profil szerkesztése a fájlokat. Ugyanazokat a módosításokat tartományvezérlőkkel történő lecserélésével `yourtenant.onmicrosoft.com` együtt az Azure AD B2C-bérlő minden fájl három helyen. Menti a fájlokat.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Az alkalmazás azonosítók hozzá az egyéni házirend
Adja hozzá a Alkalmazásazonosítók a bővítmények fájlhoz (`TrustFrameworkExtensions.xml`):

1. A bővítmények fájlban (TrustFrameworkExtensions.xml), az elem található `<TechnicalProfile Id="login-NonInteractive">`.
2. Cserélje le mindkét példányát `IdentityExperienceFrameworkAppId` a korábban létrehozott identitás élmény keretrendszer alkalmazás az alkalmazás azonosítójával. Például:

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Cserélje le mindkét példányát `ProxyIdentityExperienceFrameworkAppId` a korábban létrehozott Proxy identitás élmény keretrendszer alkalmazás az alkalmazás azonosítójával.
4. A bővítmények fájl mentéséhez.

## <a name="upload-the-policies-to-your-tenant"></a>Töltse fel a házirendeket a bérlő

1. A a [Azure-portálon](https://portal.azure.com), átváltani a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), és nyissa meg a **az Azure AD B2C** panelen.
2. Válassza ki **identitás élmény keretrendszer**.
3. Válassza ki **házirend feltöltése**.

    >[!WARNING]
    >Az egyéni házirend fájlokat fel kell tölteni, a következő sorrendben:

1. Töltse fel a TrustFrameworkBase.xml.
2. Töltse fel a TrustFrameworkExtensions.xml.
3. Töltse fel a SignUpOrSignin.xml.
4. A többi házirend fájlok feltöltése.

Ha a fájl feltöltése, a házirend-fájl neve $a-e a `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Tesztelje az egyéni házirend segítségével futtatása most

1. Nyissa meg **az Azure AD B2C beállítások** , és navigáljon **identitás élmény keretrendszer**.

   >[!NOTE]
   >**Futtassa most** legalább egy alkalmazás a tenant preregistered lehet szükséges. Alkalmazások szerepelnie kell a B2C bérlő valamelyikét a **alkalmazások** menüpont kiválasztásának az Azure AD B2C vagy beépített és az egyéni házirendek meghívni a identitás élmény keretrendszer használatával. Csak egy regisztrációszolgáltatóhoz alkalmazásonként van szükség.<br><br>
   Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.  

2. Nyissa meg a B2C_1A_signup_signin, a függő entitásonkénti (RP) egyéni házirend feltöltött. Válassza ki **futtatása most**.

3. Iratkozhat fel e-mail cím használatával kell lennie.

4. Jelentkezzen be ugyanazt a fiókot annak ellenőrzéséhez, hogy rendelkezik-e a megfelelő konfigurációt.

>[!NOTE]
>Egy bejelentkezési hiba általános oka egy nem megfelelően konfigurált IdentityExperienceFramework alkalmazást.


## <a name="next-steps"></a>Következő lépések

### <a name="add-facebook-as-an-identity-provider"></a>Adja hozzá a Facebook identitás-szolgáltatóként
Facebook beállítása:
1. [Beállíthat egy Facebook-alkalmazást a developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [A Facebook-alkalmazás titkos kulcs hozzáadása az Azure AD B2C-bérlő](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. A TrustFrameworkExtensions házirend fájlban cserélje le a `client_id` Facebook alkalmazást azonosítóval:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. A TrustFrameworkExtensions.xml házirend fájlt feltölteni a bérlő.
5. A teszt **futtatása most** , vagy hogy a házirend-ről a regisztrált alkalmazáshoz.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adja hozzá az Azure Active Directory identitás-szolgáltatóként
A már szerepel az első lépésekről szóló útmutatót alap fájl tartalmaz néhány, a tartalom az egyéb identitás-szolgáltatóktól hozzáadása szükséges. Bejelentkezések beállításával kapcsolatos információkért lásd: a [Azure Active Directory B2C: Jelentkezzen be Azure AD-fiókok használatával](active-directory-b2c-setup-aad-custom.md) cikk.

Egyéni házirendek az Azure AD B2C az identitás-élmény keretrendszerével áttekintéséért lásd: a [Azure Active Directory B2C: egyéni házirendek](active-directory-b2c-overview-custom.md) cikk. 
