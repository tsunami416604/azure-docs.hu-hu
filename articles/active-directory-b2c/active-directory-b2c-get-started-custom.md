---
title: Egyéni szabályzatok – Azure Active Directory B2C – első lépések |} A Microsoft Docs
description: Az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések útmutató.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b414529d7756812f1e1e16d2d0184c8472c0c55f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916750"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Egyéni szabályzatok](active-directory-b2c-overview-custom.md) konfigurációs fájlok, amelyek az Azure Active Directory (Azure AD) B2C-bérlő viselkedésének megadása. Ebben a cikkben létrehozhat egy egyéni szabályzatot, amely támogatja a helyi fiók regisztrálási vagy bejelentkezési e-mail cím és jelszó használatával. Emellett készítse elő a környezetet az identitás-szolgáltatóktól hozzáadása.

## <a name="prerequisites"></a>Előfeltételek

- Ha Ön nem rendelkezik ilyennel, akkor [létrehozása az Azure AD B2C-bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.
- [Az alkalmazás regisztrálásához](tutorial-register-applications.md) hozott létre, hogy képes legyen kommunikálni az Azure AD B2C-bérlőben.

## <a name="add-signing-and-encryption-keys"></a>Aláírási és titkosítási kulcsok hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő tartalmazó könyvtárba. Kattintson a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása. 
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer – előzetes verzió**.

### <a name="create-the-signing-key"></a>Az aláíró kulcs létrehozása

1. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
2. A **beállítások**, válassza a `Generate`.
3. A **neve**, adja meg `TokenSigningKeyContainer`. Az előtag `B2C_1A_` automatikusan hozzáadhatók.
4. A **kulcs típusa**válassza **RSA**.
5. A **kulcshasználat**válassza **aláírás**.
6. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-the-encryption-key"></a>A titkosítási kulcs létrehozása

1. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
2. A **beállítások**, válassza a `Generate`.
3. A **neve**, adja meg `TokenEncryptionKeyContainer`. Az előtag `B2C_1A`_ automatikusan hozzáadhatók.
4. A **kulcs típusa**válassza **RSA**.
5. A **kulcshasználat**válassza **titkosítási**.
6. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-the-facebook-key"></a>A Facebook-kulcs létrehozása

Ha már rendelkezik egy [Facebook-alkalmazás titkos](active-directory-b2c-setup-fb-app.md), szabályzat kulcsaként hozzá a bérlőjéhez. Ellenkező esetben létre kell hoznia a kulcsot a helyőrző értékét az, hogy a szabályzatok érvényesíteni.

1. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
2. A **beállítások**, válassza a `Manual`.
3. A **neve**, adja meg `FacebookSecret`. Az előtag `B2C_1A_` automatikusan hozzáadhatók.
4. A **titkos**, adja meg a Facebook titkos kód developers.facebook.com vagy `0` helyettesíti. Ez az érték a titkos kulcsot, nem az alkalmazás azonosítója.
5. A **kulcshasználat**válassza **aláírás**.
6. Kattintson a **Create** (Létrehozás) gombra.

## <a name="register-identity-experience-framework-applications"></a>Identitás-kezelőfelületi keretrendszer-alkalmazások regisztrálását

Az Azure AD B2C szükséges, hogy regisztráljon, és a felhasználók használt két alkalmazás regisztrálása: IdentityExperienceFramework (webalkalmazás), és a delegált engedély a IdentityExperienceFramework alkalmazásból (egy natív alkalmazást) ProxyIdentityExperienceFramework. Helyi fiókok csak a bérlő szerepel. A felhasználói Regisztrálás az egyedi e-mail cím és jelszó kombinációja a bérlő regisztrált alkalmazások eléréséhez.

### <a name="register-the-identityexperienceframework-application"></a>Register the IdentityExperienceFramework application

1. Válasszon **minden szolgáltatás** az Azure portal bal felső sarkában lévő keresése és kiválasztása **alkalmazásregisztrációk**.
2. Válassza az **Új alkalmazás regisztrálása** elemet.
3. A **neve**, adja meg `IdentityExperienceFramework`.
4. A **alkalmazástípus**, válassza a **Web app és az API**.
5. A **bejelentkezési URL-**, adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, ahol `your-tenant-name` van az Azure AD B2C-bérlői tartomány neve.
6. Kattintson a **Create** (Létrehozás) gombra. 
7. Miután elkészült, az alkalmazás azonosítóját, és mentse későbbi használatra.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Register the ProxyIdentityExperienceFramework application

1. Válassza ki **alkalmazásregisztrációk**, majd válassza ki **új alkalmazásregisztráció**.
2. A **neve**, adja meg `ProxyIdentityExperienceFramework`.
3. A **alkalmazástípus**, válassza a **natív**.
4. A **átirányítási URI-t**, adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, ahol `yourtenant` van az Azure AD B2C-bérlőben.
5. Kattintson a **Create** (Létrehozás) gombra. Miután elkészült, az alkalmazás azonosítóját, és mentse későbbi használatra.
6. A beállítások oldalon válassza ki a **szükséges engedélyek**, majd válassza ki **Hozzáadás**.
7. Válasszon **API kiválasztása**, keresése és kiválasztása **IdentityExperienceFramework**, és kattintson a **kiválasztása**.
9. Jelölje be a **hozzáférés IdentityExperienceFramework**, kattintson a **kiválasztása**, és kattintson a **kész**.
10. Válassza ki **engedélyek megadása**, majd erősítse meg a kiválasztásával **Igen**.

## <a name="download-starter-pack-and-modify-policies"></a>Kezdőcsomag letöltése és szabályzatok módosítása

Egyéni szabályzatok olyan XML-fájlokat, amelyek fel kell tölteni az Azure AD B2C-bérlővel. Ahhoz, hogy gyorsabban fájlok alapszintű csomagok biztosítják. A következő listában található minden egyes alapszintű csomag a legkevesebb technikai profilok és a leírt forgatókönyvek eléréséhez szükséges felhasználói utak tartalmazza:

- LocalAccounts – csak a helyi fiókok használatát teszi lehetővé.
- SocialAccounts – csak a közösségi (vagy összevont) fiókok használatát teszi lehetővé.
- SocialAndLocalAccounts – a helyi fiókok és a közösségi fiókok mindkét használatát teszi lehetővé.
- SocialAndLocalAccountsWithMFA – lehetővé teszi, hogy szociális, helyi és a multi-factor Authentication szolgáltatás beállításainak.

Minden egyes alapszintű csomag tartalmazza:

- Az alap fájl. A következő néhány módosítások szükségesek.
- A bővítményfájl.  Ez a fájl, ahol a legtöbb konfigurációs módosításokat.
- A függő entitás fájlokat. Az alkalmazás által meghívott tevékenység-specifikus fájlok.

>[!NOTE]
>Az XML-szerkesztő támogatja, ha a fájlok, szemben, amely az alapszintű csomag gyökérkönyvtárában található TrustFrameworkPolicy_0.3.0.0.xsd XML-séma érvényesítése. XML-sémaérvényesítése hibák feltöltése előtt azonosítja.

1. [Töltse le a .zip fájlt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) vagy futtatása:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. A SocialAndLocalAccounts mappába, a fájlokat, és cserélje le az összes szerkesztése `yourtenant` a bérlő nevét. Például: `contosoTenant.onmicrosoft.com`. Ha egy XML-szerkesztőt kell [próbálja meg a Visual Studio Code](https://code.visualstudio.com/download), egy könnyen használható, többplatformos szerkesztő.

### <a name="add-application-ids-to-the-custom-policy"></a>Alkalmazás-azonosítók hozzáadása az egyéni házirend

Vegye fel az alkalmazásazonosítót a bővítmények fájlba *TrustFrameworkExtensions.xml*.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt és keresse meg az elem `<TechnicalProfile Id="login-NonInteractive">`.
2. Cserélje le mindkét példányát `IdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer alkalmazás, amelyet korábban hozott létre az alkalmazás azonosítójával. Cserélje le mindkét példányát `ProxyIdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer Proxy alkalmazás, amelyet korábban hozott létre az alkalmazás azonosítójával.
3. Mentse a bővítmények fájlt.

## <a name="upload-the-policies"></a>Töltse fel a házirendeket

1. Identitás-kezelőfelületi keretrendszer egyéni szabályzatok lapon válassza az **szabályzat feltöltése**.
1. Ebben a sorrendben feltöltése *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* , és *PasswordReset.xml*. A fájl feltöltésekor előtaggal kiegészített-e a házirend-fájl neve `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Egyéni szabályzatok lapon válassza ki a **B2C_1A_signup_signin**.
2. A **válassza ki az alkalmazás** az egyéni házirend áttekintése lapon válassza ki a nevű webalkalmazás *webapp1* , amely korábban regisztrálva. Győződjön meg arról, hogy a **válasz URL-cím** van `https://jwt.ms`.
3. Válassza ki **Futtatás most**.
4. Regisztráció e-mail-címmel kell lennie.
5. Jelentkezzen be ugyanazzal a fiókkal, győződjön meg arról, hogy rendelkezik-e a megfelelő konfigurációt.

## <a name="add-facebook-as-an-identity-provider"></a>Adja hozzá a Facebook identitás-szolgáltatóként

1. Konfigurálja a [Facebook-alkalmazás](active-directory-b2c-setup-fb-app.md).
2. Az a *TrustFrameworkExtensions.xml* fájlt, cserélje le a értékét `client_id` Facebook Alkalmazásazonosítóval rendelkező:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Töltse fel a *TrustFrameworkExtensions.xml* fájlt a bérlőhöz.
4. A teszt **Futtatás most** vagy közvetlenül a regisztrált egy alkalmazást a szabályzat meghívásával.

## <a name="next-steps"></a>További lépések

- Adja hozzá az Azure Active Directory identitás-szolgáltatóként. A alap fájl már szerepel az első lépésekről szóló útmutatót tartalmaz néhány, a tartalom hozzáadása az egyéb identitás-szolgáltatóktól szükséges. Bejelentkezések beállításával kapcsolatos további információkért lásd: a [beállítása regisztráció és bejelentkezés az Azure Active Directory-fiókkal, Active Directory B2C-vel egyéni szabályzatok használatával](active-directory-b2c-setup-aad-custom.md) cikk.
