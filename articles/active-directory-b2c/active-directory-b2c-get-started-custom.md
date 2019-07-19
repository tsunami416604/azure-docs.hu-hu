---
title: Ismerkedés az egyéni szabályzatokkal – Azure Active Directory B2C
description: Megtudhatja, hogyan kezdheti el az egyéni házirendeket a Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83269a5ae0d2e5fb7ae2651dbc27926c910a0e03
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302496"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Ismerkedés az egyéni szabályzatokkal Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az [Egyéni házirendek](active-directory-b2c-overview-custom.md) olyan konfigurációs fájlok, amelyek meghatározzák a Azure Active Directory (Azure ad) B2C-bérlő működését. Ebben a cikkben egy olyan egyéni házirendet hoz létre, amely támogatja a helyi fiók regisztrálását vagy bejelentkezését e-mail-cím és jelszó használatával. Az identitás-szolgáltatók hozzáadására is elő kell készítenie a környezetet.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem rendelkezik ilyennel, létre kell hoznia [egy Azure ad B2C bérlőt](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.
- [Regisztrálja az alkalmazást](tutorial-register-applications.md) a létrehozott bérlőn, hogy kommunikálni tudjon a Azure ad B2Cával.

## <a name="add-signing-and-encryption-keys"></a>Aláírási és titkosítási kulcsok hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Kattintson a **könyvtár és előfizetés szűrőre** a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.

### <a name="create-the-signing-key"></a>Az aláíró kulcs létrehozása

1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
2. A **Beállítások**területen válassza `Generate`a lehetőséget.
3. A **név**mezőben adja `TokenSigningKeyContainer`meg a nevet. Lehet, `B2C_1A_` hogy az előtag automatikusan hozzá lesz adva.
4. A **kulcs típusa**beállításnál válassza az **RSA**elemet.
5. A **kulcshasználat**beállításnál válassza az **aláírás**lehetőséget.
6. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-the-encryption-key"></a>A titkosítási kulcs létrehozása

1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
2. A **Beállítások**területen válassza `Generate`a lehetőséget.
3. A **név**mezőben adja `TokenEncryptionKeyContainer`meg a nevet. Lehetséges, `B2C_1A`hogy az _ előtag automatikusan hozzá van adva.
4. A **kulcs típusa**beállításnál válassza az **RSA**elemet.
5. A **kulcshasználat**beállításnál válassza a **titkosítás**lehetőséget.
6. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-the-facebook-key"></a>A Facebook-kulcs létrehozása

Ha már rendelkezik Facebook- [alkalmazási titokkal](active-directory-b2c-setup-fb-app.md), vegye fel a bérlőhöz tartozó szabályzati kulcsként. Ellenkező esetben helyőrző értékkel kell létrehoznia a kulcsot, hogy a szabályzatok át legyenek érvényesítve.

1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás**gombra.
2. A **Beállítások**területen válassza `Manual`a lehetőséget.
3. A **név**mezőbe írja `FacebookSecret`be a következőt:. Lehet, `B2C_1A_` hogy az előtag automatikusan hozzá lesz adva.
4. A **Secret (titkos**kód) mezőben adja meg a `0` Facebook-titkot a Developers.Facebook.com vagy helyőrzőként. Ez az érték a titok, nem az alkalmazás azonosítója.
5. A **kulcshasználat**beállításnál válassza az **aláírás**lehetőséget.
6. Kattintson a **Create** (Létrehozás) gombra.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework-alkalmazások regisztrálása

Azure AD B2C a regisztrációhoz és a bejelentkezéshez használt két alkalmazás regisztrálását igényli: IdentityExperienceFramework (egy webalkalmazás) és ProxyIdentityExperienceFramework (natív alkalmazás) delegált engedéllyel a IdentityExperienceFramework alkalmazásban. Helyi fiókok csak a bérlőben vannak. A felhasználók egy egyedi e-mail-cím/jelszó kombinációval regisztrálhatnak a bérlő által regisztrált alkalmazások eléréséhez.

### <a name="register-the-identityexperienceframework-application"></a>A IdentityExperienceFramework alkalmazás regisztrálása

1. Válassza a **minden szolgáltatás** lehetőséget a Azure Portal bal felső sarkában.
1. A keresőmezőbe írja be a `Azure Active Directory` kifejezést.
1. A keresési eredmények között válassza a **Azure Active Directory** lehetőséget.
1. A bal oldali menü **kezelés** területén válassza a **Alkalmazásregisztrációk (örökölt)** lehetőséget.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. A **név**mezőbe írja `IdentityExperienceFramework`be a következőt:.
1. Az **alkalmazás típusa mezőben**válassza a **Web App/API**lehetőséget.
1. A **bejelentkezési URL-cím**mezőbe írja `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`be a `your-tenant-name` (z) nevet, ahol a a Azure ad B2C bérlői tartományneve. Az összes URL-nek most a [B2clogin.com](b2clogin.md)-t kell használnia.
1. Kattintson a **Create** (Létrehozás) gombra. A létrehozást követően másolja ki az alkalmazás AZONOSÍTÓját, és mentse a későbbiekben való használatra.

### <a name="register-the-proxyidentityexperienceframework-application"></a>A ProxyIdentityExperienceFramework alkalmazás regisztrálása

1. **Alkalmazásregisztrációk (örökölt)** területen válassza az **új alkalmazás regisztrálása**lehetőséget.
2. A **név**mezőbe írja `ProxyIdentityExperienceFramework`be a következőt:.
3. Az **alkalmazás típusa mezőben**válassza a **natív**lehetőséget.
4. Az **átirányítási URI**esetében `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`adja meg `your-tenant-name` , hogy hol található a Azure ad B2C bérlője.
5. Kattintson a **Create** (Létrehozás) gombra. A létrehozást követően másolja ki az alkalmazás AZONOSÍTÓját, és mentse a későbbiekben való használatra.
6. A beállítások lapon válassza a **szükséges engedélyek**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
7. Válassza **az API kiválasztása**lehetőséget, keresse meg és válassza ki a **IdentityExperienceFramework**, majd kattintson a **kiválasztás**gombra.
9. Jelölje be a **hozzáférés IdentityExperienceFramework**melletti jelölőnégyzetet, kattintson a **kiválasztás**elemre, majd kattintson a **kész**gombra.
10. Válassza az **engedélyek megadása**lehetőséget, majd erősítse meg az **Igen**lehetőséget.

## <a name="custom-policy-starter-pack"></a>Egyéni házirend alapszintű csomagja

Az egyéni házirendek a Azure AD B2C bérlőre feltöltött, a technikai profilok és a felhasználói utazások meghatározására feltöltött XML-fájlok összessége. Az alapszintű csomagokat számos előre elkészített szabályzattal biztosítjuk, hogy gyorsan elkészüljön. Az alapszintű csomagok mindegyike a leírt forgatókönyvek eléréséhez szükséges technikai profilok és felhasználói utazások legkisebb számát tartalmazza:

- **LocalAccounts** – csak a helyi fiókok használatát engedélyezi.
- **SocialAccounts** – engedélyezi a közösségi (vagy összevont) fiókok használatát.
- **SocialAndLocalAccounts** – lehetővé teszi mind a helyi, mind a közösségi fiókok használatát.
- **SocialAndLocalAccountsWithMFA** – lehetővé teszi a közösségi, a helyi és a többtényezős hitelesítési lehetőségek használatát.

Minden kezdő csomag a következőket tartalmazza:

- **Alapfájl** – kevés módosításra van szükség az alaphoz. Példa: *TrustFrameworkBase. XML*
- **Kiterjesztési fájl** – ez a fájl a legtöbb konfigurációs módosítást hajtja végre. Példa: *TrustFrameworkExtensions. XML*
- **Függő entitás fájljai** – az alkalmazás által meghívott feladat-specifikus fájlok. Példák: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

Ebben a cikkben a **SocialAndLocalAccounts** alapszintű csomagban lévő egyéni XML-házirendek fájljait szerkeszti. Ha XML-szerkesztőre van szüksége, próbálja ki a [Visual Studio Code](https://code.visualstudio.com/download)-ot, amely egy könnyű platformfüggetlen szerkesztő.

### <a name="get-the-starter-pack"></a>Az alapszintű csomag beszerzése

Szerezze be az egyéni házirend-előindítási csomagokat a GitHubról, majd frissítse az SocialAndLocalAccounts Starter Pack-ban található XML-fájlokat a Azure AD B2C bérlői nevével.

1. [Töltse le a. zip fájlt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) , vagy az adattár klónozása:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. A **SocialAndLocalAccounts** könyvtár összes fájljában cserélje le a karakterláncot `yourtenant` a Azure ad B2C bérlő nevére.

    Ha például a B2C-bérlő neve *contosotenant*, az összes példánya `yourtenant.onmicrosoft.com` lesz. `contosotenant.onmicrosoft.com`

### <a name="add-application-ids-to-the-custom-policy"></a>Alkalmazás-azonosítók hozzáadása az egyéni szabályzathoz

Adja hozzá az alkalmazás-azonosítókat a *TrustFrameworkExtensions. XML*kiterjesztésű fájlhoz.

1. Nyissa meg és keresse `<TechnicalProfile Id="login-NonInteractive">`meg `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** az elemet.
1. Cserélje le mindkét példányát `IdentityExperienceFrameworkAppId` a korábban létrehozott IdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.
1. Cserélje le mindkét példányát `ProxyIdentityExperienceFrameworkAppId` a korábban létrehozott ProxyIdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.
1. Mentse a fájlt.

## <a name="upload-the-policies"></a>A szabályzatok feltöltése

1. Válassza ki az **Identity Experience Framework** menüpontot a B2C-bérlőben a Azure Portalban.
1. Válassza az **egyéni házirend feltöltése**lehetőséget.
1. Ebben a sorrendben töltse fel a házirend-fájlokat:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

A fájlok feltöltésekor az Azure hozzáadja az előtagot `B2C_1A_` mindegyikhez.

> [!TIP]
> Ha az XML-szerkesztő támogatja az érvényesítést, ellenőrizze a `TrustFrameworkPolicy_0.3.0.0.xsd` fájlokat az alapszintű csomag gyökérkönyvtárában található XML-séma alapján. Az XML-séma ellenőrzése a feltöltés előtt hibákat azonosít.

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Az **Egyéni házirendek**területen válassza a **B2C_1A_signup_signin**lehetőséget.
1. Az egyéni házirend áttekintés lapján jelölje ki az **alkalmazás kiválasztása** lehetőséget, majd válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást.
1. Győződjön meg arról, hogy a **Válasz URL-címe** `https://jwt.ms`:.
1. Válassza a **Futtatás most**lehetőséget.
1. Regisztráljon e-mail-cím használatával.
1. Válassza a **Futtatás most** újra lehetőséget.
1. Jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy megfelelő-e a konfigurációja.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook hozzáadása identitás-szolgáltatóként

1. A Facebook-alkalmazás konfigurálásához hajtsa végre a [regisztráció és bejelentkezés Facebook-fiókkal](active-directory-b2c-setup-fb-app.md) való beállításának lépéseit.
1. A fájlban cserélje le a Facebook-alkalmazás azonosítóját a (z) értékre `client_id`: `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Töltse fel a *TrustFrameworkExtensions. XML* fájlt a bérlőbe.
1. Az **Egyéni házirendek**területen válassza a **B2C_1A_signup_signin**lehetőséget.
1. Kattintson a **Futtatás most** lehetőségre, és válassza a Facebook lehetőséget a Facebook használatával való bejelentkezéshez és az egyéni szabályzat teszteléséhez. Vagy hívja meg közvetlenül a regisztrált alkalmazásból a szabályzatot.

## <a name="next-steps"></a>További lépések

Ezután próbálkozzon Azure Active Directory (Azure AD) identitás-szolgáltatóként való hozzáadásával. Az első lépéseket ismertető útmutatóban használt alapfájl már tartalmazza a más személyazonossági szolgáltatók (például az Azure AD) hozzáadásához szükséges tartalmakat.

Az Azure AD-t és az identitás-szolgáltató beállításával kapcsolatos információkért tekintse [meg a regisztrálás és bejelentkezés beállítása Azure Active Directory fiókkal Active Directory B2C egyéni házirendek használatával](active-directory-b2c-setup-aad-custom.md)című témakört.
