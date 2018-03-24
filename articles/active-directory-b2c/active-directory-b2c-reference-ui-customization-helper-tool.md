---
title: 'Az Azure Active Directory B2C: Lap felhasználói felületének testreszabása segédeszköze |} Microsoft Docs'
description: A lap felhasználói felületi testreszabási szolgáltatás az Azure Active Directory B2C bemutatásához használt segítő eszköz
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: f898c626d52b1a4e7df72284190749f4481999ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Az Azure Active Directory B2C: Segítő használt eszköz a lap felhasználói felület (UI) testreszabási szolgáltatás bemutatása
Ez a cikk egy kiegészítő, hogy a [fő felhasználói felület testreszabása cikk](active-directory-b2c-reference-ui-customization.md) az Azure Active Directory (Azure AD) B2C. A következő lépések azt ismertetik, hogyan gyakorlására a lap felhasználói felületi testreszabási szolgáltatás minta HTML- és CSS tartalom mellékelt használatával.

## <a name="get-an-azure-ad-b2c-tenant"></a>Az Azure AD B2C-bérlő beszerzése
Mielőtt testre szabhatja semmit, akkor [az Azure AD B2C-bérlő beszerzése](active-directory-b2c-get-started.md) Ha még nem rendelkezik.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Regisztrálási vagy bejelentkezési szabályzat létrehozása
A mellékelt minta tartalom customze két oldalán használható egy [regisztráció vagy bejelentkezés házirend](active-directory-b2c-reference-policies.md): a [egyesített bejelentkezési oldal](active-directory-b2c-reference-ui-customization.md) és a [önálló szükséges attribútumok lap](active-directory-b2c-reference-ui-customization.md). Ha [a regisztráció vagy bejelentkezés házirend létrehozása](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), adja hozzá a helyi fiókkal (e-mail cím), a Facebook, a Google és a Microsoft részére **identitás-szolgáltatóktól**. Ezek azok a csak IDPs, amely elfogadja a minta HTML-tartalmakat.  Ha is hozzáadhat a IDPs egy részét.

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása
Szüksége lesz a [alkalmazás regisztrálása](active-directory-b2c-app-registration.md) a B2C bérlő a házirend végrehajtásához használható. Az alkalmazás a regisztrálás után futtatja ténylegesen a regisztrációs szabályzatban használt néhány lehetőség közül választhat:

* Build az Azure AD B2C az "Első lépések" című részében felsorolt gyors üzembe helyezési alkalmazások egyik [jelentkezzen regisztrálása és beléptetése az alkalmazások a fogyasztói](active-directory-b2c-overview.md#get-started).
* Használja az előre elkészített [az Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) alkalmazás. Ha a playground használatát választja, regisztrálnia kell egy alkalmazást a B2C-bérlő használata a a **átirányítási URI** `https://aadb2cplayground.azurewebsites.net/`.
* Használja a **Futtatás most** gombra a házirend a [Azure-portálon](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Szabja testre a házirendet
A házirend megjelenésének és arculatának testreszabásához szüksége először létre kell hoznia az adott egyezmények az Azure AD B2C használatával HTML- és CSS fájlok. Majd feltöltheti a statikus tartalom nyilvánosan elérhető helyre, hogy az Azure AD B2C-e hozzáférési engedélye. Ennek oka az lehet, a saját dedikált webkiszolgálón, Azure Blob Storage, Azure Content Delivery Network vagy bármely más statikus erőforrás-üzemeltetési szolgáltató. A csak követelményei, hogy a tartalom elérhető HTTPS-KAPCSOLATON keresztül, és CORS használatával érhető el. Ha a statikus tartalmat a webkiszolgálón van közzétéve, szerkesztheti a erre a helyre mutasson, és biztosítsa, hogy a tartalom az ügyfelek számára a házirendben. A [fő felhasználói felület testreszabása cikk](active-directory-b2c-reference-ui-customization.md) részletesen bemutatja az Azure AD B2C testreszabási szolgáltatás működését.

Ez az oktatóanyag céljából már előre létrehozott néhány minta tartalom és üzemelteti az Azure Blob Storage tárolóban. A minta tartalma a fiktív vállalat, a "Wingtip Toys" a témában nagyon egyszerű testreszabását. Próbálja ki a saját házirendben, kövesse az alábbi lépéseket:

1. Jelentkezzen be a bérlő számítógépen a [Azure-portálon](https://portal.azure.com/) , és keresse meg a B2C funkciók panelje.
2. Kattintson a **regisztráció vagy bejelentkezés házirendek** és kattintson a házirend (például "b2c\_1\_bejelentkezési\_mentése\_bejelentkezési\_a").
3. Kattintson a **lap felhasználói felületének testreszabása** , majd **egyesített előfizetési vagy a bejelentkezési oldal**.
4. Váltás a **használata egyéni lap** váltani **Igen**. Az a **egyéni lap URI** mezőbe írja be `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Kattintson az **OK** gombra.
5. Kattintson a **helyi fiók előfizetéshez**. Váltás a **egyéni sablon használata** váltani **Igen**. Az a **egyéni lap URI** mezőbe írja be `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Az azonos ismételje meg a a **közösségi fiók bejelentkezési oldalának**.
   Kattintson a **OK** kétszer a felhasználói felület testreszabása paneleken gombra.
7. Kattintson a **Save** (Mentés) gombra.

Most tekinthetők meg a testreszabott házirendet. Használhatja az alkalmazását, vagy az Azure AD B2C playground Ha kívánja, de egyszerűen is kattinthat a **Futtatás most** parancsot a szabályzat paneljén. A legördülő listából válassza ki az alkalmazását, és válassza ki a megfelelő átirányítási URI-t. Kattintson a **futtatása most** gombra. Egy új böngészőlapon nyílik meg, és a felhasználói élmény iratkozik fel az alkalmazás és a hely az új tartalom segítségével futtathatja!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>A minta tartalom feltöltése az Azure Blob Storage
Ha szeretné használni az Azure Blob Storage a lap tartalmát, a saját storage-fiók létrehozása, és a B2C segédeszköze használja a fájlok feltöltéséhez.

### <a name="create-a-storage-account"></a>Create a storage account
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **+ új** > **adatok + tárolás** > **tárfiók**. Szüksége lesz egy Azure Blob Storage-fiók létrehozásához Azure-előfizetéssel. Regisztrálhat egy ingyenes próbaverziót: a [Azure-webhelyen](https://azure.microsoft.com/pricing/free-trial/).
3. Adjon meg egy **neve** a tárolási fiók (például "contoso"), és válassza ki a megfelelő eltávolításra **tarifacsomag**, **erőforráscsoport** és **előfizetés**. Győződjön meg arról, hogy rendelkezik a **a kezdőpulton rögzít** beállítás be van jelölve. Kattintson a **Create** (Létrehozás) gombra.
4. Lépjen vissza a kezdőpulton, és kattintson az imént létrehozott tárfiókra.
5. Az a **összegzés** területén kattintson **tárolók**, és kattintson a **+ Hozzáadás**.
6. Adjon meg egy **neve** a tároló (például "b2c"), és válassza ki a **Blob** , a **hozzáférési típus**. Kattintson az **OK** gombra.
7. A létrehozott tároló megjelenik a listában a **Blobok** panelen. Jegyezze fel az URL-címet, a tároló; például az alábbihoz hasonlít `https://contoso.blob.core.windows.net/b2c`. Zárja be a **Blobok** panelen.
8. A storage-fiók panelen kattintson **kulcsok** írja le az értékeket a **Tárfióknév** és **elsődleges elérési kulcsot** mezőket.

> [!NOTE]
> **Elsődleges elérési kulcsot** van egy fontos biztonsági hitelesítő adatok.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Töltse le a segítő eszközre és fájlok
Letöltheti a [Azure Blob Storage segítő eszközre és .zip-fájlként fájlok](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) vagy klónozza a Githubról:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Ebben a tárházban tartalmaz egy `sample_templates\wingtip` címtár, például HTML, CSS, és a képeket tartalmazó. Ezek a sablonok hivatkoznak a saját Azure Blob Storage-fiók akkor a HTML-fájlok szerkesztése. Nyissa meg `unified.html` és `selfasserted.html` , és cserélje le minden példányát `https://localhost` saját tárolót, amelyet az előző lépésben URL-címét. Abszolút elérési útja a HTML-fájlokat kell használnia, mivel a HTML-KÓDBAN Azure Active Directory, a tartományi által kiszolgált ebben az esetben `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>A minta-fájl feltöltése
Bontsa ki az adott adattár a `B2CAzureStorageClient.zip` , és futtassa a `B2CAzureStorageClient.exe` fájlon belül. Ez a program egyszerűen csak meg kell adnia a tárfiók könyvtárban található összes fájl feltöltése, és engedélyezze a hozzáférést a CORS azokat a fájlokat. Ha követte a fenti lépéseket, a HTML- és CSS fájlok most kell mutat a tárfiókhoz. Vegye figyelembe, hogy a tárfiók nevét a része, amely megelőzi `blob.core.windows.net`, például `contoso`. Ellenőrizheti, hogy a tartalom feltöltötte megfelelően által elérni kívánt `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` egy böngésző. Is [ http://test-cors.org/ ](http://test-cors.org/) győződjön meg arról, hogy a tartalom jelenleg engedélyezett CORS. (Keressen "XHR állapota: 200" az eredményben.)

### <a name="customize-your-policy-again"></a>A házirendet, újra testreszabása
Most, hogy a minta tartalmat a saját tárfiók feltöltött, szerkesztenie kell a regisztrációs szabályzatban arra mutató hivatkozás használatára. Ismételje meg a lépéseket a ["Testreszabhatja az"](#customize-your-policy) a fenti szakaszban ezúttal a saját storage-fiók URL-címeket. Például a helyét a `unified.html` fájl lenne `<url-of-your-container>/wingtip/unified.html`.

Most már használhatja a **Futtatás most** gombra vagy a saját-alkalmazás a házirend újra. Az eredmény kell szinte teljesen is ugyanúgy néznek ki – akkor használja az azonos mintában HTML- és CSS mindkét esetben. Azonban a házirend most hivatkoznak a saját Azure Blob Storage példányát, és szabadon szerkesztése, majd töltse fel újra, ha Ön a fájlok adja. A HTML- és CSS testreszabásáról további információkért tekintse meg a [fő felhasználói felület testreszabása cikk](active-directory-b2c-reference-ui-customization.md).

