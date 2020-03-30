---
title: Egyéni szabályzatok – első lépések
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan kezdheti el az egyéni szabályzatokat az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc87628d8b47435012c3d20ec2e72ac186983555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189327"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Ismerkedés az egyéni szabályzatokkal az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Az egyéni szabályzatok](custom-policy-overview.md) olyan konfigurációs fájlok, amelyek meghatározzák az Azure Active Directory B2C (Azure AD B2C) bérlőjének viselkedését. Ebben a cikkben hozzon létre egy egyéni szabályzatot, amely támogatja a helyi fiók regisztrációját vagy bejelentkezését e-mail-cím és jelszó használatával. A környezetet identitásszolgáltatók hozzáadására is előkészíti.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem rendelkezik ilyen, [hozzon létre egy Azure AD B2C-bérlő,](tutorial-create-tenant.md) amely kapcsolódik az Azure-előfizetéshez.
- [Regisztrálja az alkalmazást](tutorial-register-applications.md) a létrehozott bérlőben, hogy kommunikálhasson az Azure AD B2C-vel.
- Hajtsa végre a [Facebook-alkalmazás konfigurálásához facebookos fiókkal](identity-provider-facebook.md) történő regisztráció és bejelentkezés beállítása című lépéseit. Bár az egyéni irányelvek használatához nem szükséges facebookos alkalmazás, ebben a forgatókönyvben az egyéni házirendekben való közösségi bejelentkezés engedélyezésének szemléltetésére szolgál.

## <a name="add-signing-and-encryption-keys"></a>Aláíró és titkosítási kulcsok hozzáadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. Az áttekintő lap **Házirendek**területén válassza **az Identitásélmény-keretrendszert**.

### <a name="create-the-signing-key"></a>Az aláíró kulcs létrehozása

1. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Generate`területen válassza a lehetőséget.
1. Írja be `TokenSigningKeyContainer`a **Név**mezőbe a . Előfordulhat, `B2C_1A_` hogy az előtag automatikusan hozzáadódik.
1. A **Kulcstípus mezőben**válassza az **RSA**lehetőséget.
1. A **kulcshasználathoz**válassza az **Aláírás**lehetőséget.
1. Kattintson a **Létrehozás** gombra.

### <a name="create-the-encryption-key"></a>A titkosítási kulcs létrehozása

1. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Generate`területen válassza a lehetőséget.
1. Írja be `TokenEncryptionKeyContainer`a **Név**mezőbe a . A _ `B2C_1A`előtag automatikusan hozzáadható.
1. A **Kulcstípus mezőben**válassza az **RSA**lehetőséget.
1. **Kulcshasználat**esetén válassza a **Titkosítás**lehetőséget.
1. Kattintson a **Létrehozás** gombra.

### <a name="create-the-facebook-key"></a>A Facebook-kulcs létrehozása

Add meg a Facebook alkalmazás [App Secret,](identity-provider-facebook.md) mint a politika kulcs. Használhatja a cikk előfeltételei részeként létrehozott alkalmazás titkos kulcsot.

1. Válassza a **Házirendkulcsok,** majd **a Hozzáadás**lehetőséget.
1. A **Beállítások** `Manual`területen válassza a lehetőséget.
1. A **Név** `FacebookSecret`mezőbe írja be a mezőbe. Előfordulhat, `B2C_1A_` hogy az előtag automatikusan hozzáadódik.
1. A **Titkos**, adja meg a Facebook alkalmazás *App Secret* developers.facebook.com. Ez az érték a titkos, nem az alkalmazás azonosítója.
1. A **kulcshasználathoz**válassza az **Aláírás**lehetőséget.
1. Kattintson a **Létrehozás** gombra.

## <a name="register-identity-experience-framework-applications"></a>Identitáskezelési keretrendszer-alkalmazások regisztrálása

Az Azure AD B2C megköveteli, hogy regisztráljon két alkalmazást, amelyet a helyi fiókkal rendelkező felhasználók regisztrálásához és bejelentkezéséhez használ: *IdentityExperienceFramework*, egy webes API és *proxyidentityExperienceFramework,* egy natív alkalmazás, amely delegált engedéllyel rendelkezik az IdentityExperienceFramework alkalmazáshoz. A felhasználók feliratkozhatnak egy e-mail címmel vagy felhasználónévvel és egy jelszóval a bérlő által regisztrált alkalmazások eléréséhez, amely "helyi fiókot" hoz létre. A helyi fiókok csak az Azure AD B2C-bérlőben léteznek.

Ezt a két alkalmazást csak egyszer kell regisztrálnia az Azure AD B2C-bérlőben.

### <a name="register-the-identityexperienceframework-application"></a>Az IdentityExperienceFramework alkalmazás regisztrálása

Egy alkalmazás regisztrálásához az Azure AD B2C-bérlőben használhatja az **alkalmazásregisztrációk (Legacy)** vagy az új egyesített **alkalmazás-regisztrációk (előzetes verzió).** [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
1. Az **Azure Active Directory** áttekintése menü **Kezelése**területén válassza **az Alkalmazásregisztrációk (Örökölt)** lehetőséget.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. A **Név** `IdentityExperienceFramework`mezőbe írja be a mezőbe.
1. Az **Alkalmazástípus mezőben**válassza a **Web app/API**lehetőséget.
1. A **Bejelentkezési URL-cím**mezőbe írja be, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`hogy hol `your-tenant-name` található az Azure AD B2C bérlői tartományneve. Mostantól minden URL-nek [b2clogin.com](b2clogin.md)kell használnia.
1. Kattintson a **Létrehozás** gombra. Létrehozása után másolja az alkalmazás azonosítóját, és mentse későbbi használatra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. A **Név** `IdentityExperienceFramework`mezőbe írja be a mezőbe.
1. A **Támogatott fióktípusok csoportban**válassza **a Csak ebben a szervezeti címtárban a Fiókok**lehetőséget.
1. Az **URI átirányítása csoportban**válassza `your-tenant-name` a **Web**lehetőséget, majd írja be a t, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`ahol az Azure AD B2C bérlői tartományneve.
1. **Az Engedélyek csoportban**jelölje be a Rendszergazda hozzájárulása a *nyílt és offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Ezután tegye elérhetővé az API-t egy hatókör hozzáadásával:

1. A **Kezelés csoportban**válassza **az API-k felfedése**lehetőséget.
1. Válassza **a Hatókör hozzáadása**lehetőséget, majd a Mentés lehetőséget, és fogadja továbbra **is** az alapértelmezett alkalmazásazonosító URI-t.
1. Adja meg a következő értékeket, hogy hozzon létre egy hatókört, amely lehetővé teszi az egyéni szabályzat végrehajtását az Azure AD B2C bérlőben:
    * **Hatókör neve**:`user_impersonation`
    * **Rendszergazdai hozzájárulás megjelenítendő neve**:`Access IdentityExperienceFramework`
    * **Rendszergazdai hozzájárulás leírása:**`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Lehetőség **hozzáadása lehetőséget.**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>A ProxyIdentityExperienceFramework alkalmazás regisztrálása

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Az **Alkalmazásregisztrációk (Örökölt)** területen válassza az **Új alkalmazás regisztrációja**lehetőséget.
1. A **Név** `ProxyIdentityExperienceFramework`mezőbe írja be a mezőbe.
1. Az **Alkalmazástípus mezőben**válassza a **Natív**lehetőséget.
1. Az **átirányításURI,adja**meg, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`ahol `your-tenant-name` az Azure AD B2C bérlő.
1. Kattintson a **Létrehozás** gombra. Létrehozása után másolja az alkalmazás azonosítóját, és mentse későbbi használatra.
1. Válassza a **Beállítások**lehetőséget, majd a **Szükséges engedélyek**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Válassza **az API kiválasztása**lehetőséget, keresse meg az **IdentityExperienceFramework**elemet, és kattintson **a Kijelölés gombra.**
1. Jelölje be az **Access IdentityExperienceFramework ( Access IdentityExperienceFramework**) melletti **jelölőnégyzetet,** kattintson a Kijelölés gombra, majd a **Kész**gombra.
1. Válassza **az Engedélyek megadása**lehetőséget, majd az **Igen**lehetőség kiválasztásával erősítse meg a megerősítést.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. A **Név** `ProxyIdentityExperienceFramework`mezőbe írja be a mezőbe.
1. A **Támogatott fióktípusok csoportban**válassza **a Csak ebben a szervezeti címtárban a Fiókok**lehetőséget.
1. Az **Átirányítás URI csoportban**válassza a **Nyilvános ügyfél/natív (mobil & asztali)** lehetőséget a legördülő menüben.
1. Az **átirányításURI,adja**meg, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`ahol `your-tenant-name` az Azure AD B2C bérlő.
1. **Az Engedélyek csoportban**jelölje be a Rendszergazda hozzájárulása a *nyílt és offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Ezután adja meg, hogy az alkalmazást nyilvános ügyfélként kell kezelni:

1. A **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. Válassza **az Új felület kipróbálása** lehetőséget (ha látható).
1. A **Speciális beállítások csoportban**engedélyezze **az alkalmazás kezelése nyilvános ügyfélként** lehetőséget (válassza az **Igen**lehetőséget).
1. Kattintson a **Mentés** gombra.

Most adjon engedélyeket az *IdentityExperienceFramework* regisztrációja korábban elérhetővé tett API-hatókörhöz:

1. A **Kezelés csoportban**válassza az **API-engedélyek lehetőséget.**
1. A **Konfigurált engedélyek**csoportban válassza **az Engedély hozzáadása**lehetőséget.
1. Válassza a **Saját API-k** lapot, majd válassza az **IdentityExperienceFramework** alkalmazást.
1. Az **Engedély csoportban**jelölje ki a korábban megadott **user_impersonation** hatókört.
1. Válassza **az Engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblépne a következő lépésre.
1. Válassza **a Rendszergazdai hozzájárulás megadása (a bérlő neve) lehetőséget.**
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy fiókkal az Azure AD B2C-bérlőben, amely legalább a *felhőalkalmazás-rendszergazdai* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **Frissítés**lehetőséget, majd ellenőrizze, hogy a "Granted for ..." mindkét hatókör **Állapota** csoportban jelenik meg. Az engedélyek propagálása eltarthat néhány percig.

* * *

## <a name="custom-policy-starter-pack"></a>Egyéni házirend kezdőcsomagja

Az egyéni szabályzatok az Azure AD B2C-bérlőbe feltöltött XML-fájlok, amelyek technikai profilok és felhasználói utak meghatározásához kerülnek. Az induló csomagokszámos előre elkészített szabályzatot tartalmaznak a gyors beindításhoz. Az induló csomagok mindegyike a leírt forgatókönyvek eléréséhez szükséges legkevesebb technikai profilt és felhasználói utat tartalmazza:

- **LocalAccounts** – csak helyi fiókok használatát engedélyezi.
- **SocialAccounts** - Csak a közösségi (vagy összevont) fiókok használatát engedélyezi.
- **SocialAndLocalAccounts** - Lehetővé teszi a helyi és a közösségi fiókok használatát.
- **SocialAndLocalAccountsWithMFA** – Közösségi, helyi és többtényezős hitelesítési lehetőségek engedélyezése.

Minden kezdőcsomag a következőket tartalmazza:

- **Alapfájl** – Néhány módosítás szükséges az alaphoz. Példa: *TrustFrameworkBase.xml*
- **Kiterjesztés fájl** - Ez a fájl, ahol a legtöbb konfigurációs módosításokat hajtvégre. Példa: *TrustFrameworkExtensions.xml*
- **Függő fél fájlok** - Feladat-specifikus fájlokat hívott az alkalmazás. Példák: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

Ebben a cikkben szerkesztheti az XML egyéni házirendfájlokat a **SocialAndLocalAccounts** kezdőcsomagban. Ha XML-szerkesztőre van szüksége, próbálja ki a [Visual Studio Code-ot,](https://code.visualstudio.com/download)egy könnyű platformfüggetlen szerkesztőt.

### <a name="get-the-starter-pack"></a>Szerezd meg az indítócsomagot

Az egyéni szabályzat kezdőcsomagjait a GitHubról szerezheti be, majd frissítse az XML-fájlokat a SocialAndLocalAccounts kezdőcsomagban az Azure AD B2C bérlői nevével.

1. [Töltse le a .zip fájlt,](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) vagy klónozza a tárolót:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. A **SocialAndLocalAccounts** könyvtárösszes fájljában cserélje le `yourtenant` a karakterláncot az Azure AD B2C-bérlő nevére.

    Ha például a B2C-bérlő neve *contosotenant,* akkor `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`a lesz.

### <a name="add-application-ids-to-the-custom-policy"></a>Alkalmazásazonosítók hozzáadása az egyéni házirendhez

Adja hozzá az alkalmazásazonosítókat a *TrustFrameworkExtensions.xml*kiterjesztésfájlhoz.

1. Nyissa `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** meg és `<TechnicalProfile Id="login-NonInteractive">`keresse meg az elemet.
1. Cserélje le mindkét `IdentityExperienceFrameworkAppId` példányt a korábban létrehozott IdentityExperienceFramework alkalmazás alkalmazásazonosítójára.
1. Cserélje le mindkét `ProxyIdentityExperienceFrameworkAppId` példányt a korábban létrehozott ProxyIdentityExperienceFramework alkalmazás azonosítójára.
1. Mentse a fájlt.

## <a name="upload-the-policies"></a>A házirendek feltöltése

1. Válassza ki az **Identitáskezelési keretrendszer** menüelemet a B2C-bérlőben az Azure Portalon.
1. Válassza **az Egyéni házirend feltöltése lehetőséget.**
1. Ebben a sorrendben töltse fel a házirendfájlokat:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfilSzerkesztés.xml*
    1. *PasswordReset.xml fájl*

A fájlok feltöltése közben az `B2C_1A_` Azure hozzáadja az előtagot mindegyikhez.

> [!TIP]
> Ha az XML-szerkesztő támogatja az ellenőrzést, érvényesítse a fájlokat az `TrustFrameworkPolicy_0.3.0.0.xsd` indítócsomag gyökérkönyvtárában található XML-sémával szemben. Az XML-séma érvényesítése a feltöltés előtt azonosítja a hibákat.

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Az **Egyéni házirendek csoportban**válassza **a B2C_1A_signup_signin**lehetőséget.
1. Az **alkalmazás kiválasztása** az egyéni házirend áttekintő lapján válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást.
1. Győződjön meg arról, hogy a **Válasz URL-címe** . `https://jwt.ms`
1. Válassza **a Futtatás most**lehetőséget.
1. Regisztráljon egy e-mail címmel.
1. Válassza a **Futtatás most** újra lehetőséget.
1. Jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy a megfelelő konfigurációval rendelkezik-e.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook hozzáadása identitásszolgáltatóként

Ahogy az [előfeltételek](#prerequisites)ben is szerepel, a Facebook *nem* kötelező az egyéni házirendek használatához, hanem itt arra szolgál, hogy bemutassa, hogyan engedélyezheti az összevont közösségi bejelentkezést egy egyéni házirendben.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** A fájlban cserélje le `client_id` az értékét a Facebook alkalmazás azonosítójával:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Töltse fel a *TrustFrameworkExtensions.xml* fájlt a bérlőbe.
1. Az **Egyéni házirendek csoportban**válassza **a B2C_1A_signup_signin**lehetőséget.
1. Válaszd a **Futtatás most** lehetőséget, és válaszd a Facebook lehetőséget a Facebookkal való bejelentkezéshez és az egyéni szabályzat teszteléséhez.

## <a name="next-steps"></a>További lépések

Ezután próbálja meg hozzáadni az Azure Active Directoryt (Azure AD) identitásszolgáltatóként. Az első lépések útmutatóban használt alapfájl már tartalmaz néhány olyan tartalmat, amely más identitásszolgáltatók, például az Azure AD hozzáadásához szükséges.

Az Azure AD beállítása mint és identitásszolgáltató, olvassa [el a Regisztráció beállítása és bejelentkezés Azure Active Directory-fiókkal az Active Directory B2C egyéni szabályzatok használatával című témakört.](identity-provider-azure-ad-single-tenant-custom.md)
