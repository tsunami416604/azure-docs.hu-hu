---
title: Oldal-UI testreszabása segédeszköze az Azure Active Directory B2C |} A Microsoft Docs
description: A lap felhasználói felületi testreszabási szolgáltatás az Azure Active Directory B2C használatával bemutatható segédeszköze.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 18f921fb718aeb7ae4add2836fbb6ffabd66668f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445058"
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Az Azure Active Directory B2C: Segítő használt eszköz a lap felhasználói felületének (UI) testreszabási szolgáltatás bemutatása
Ez a cikk remek kiegészítése a [fő felhasználói felület testreszabása a cikk](active-directory-b2c-reference-ui-customization.md) Azure Active Directory (Azure AD) B2C-ben. A következő lépések bemutatják, hogyan, amelyeket szeretne a lap felhasználói felületének testreszabási funkcióról a minta HTML és CSS tartalmat, lehetőséget biztosítunk.

## <a name="get-an-azure-ad-b2c-tenant"></a>Azure AD B2C-bérlő beszerzése
Mielőtt bármit testreszabhatja, kell [lekérése az Azure AD B2C-bérlő](active-directory-b2c-get-started.md) Ha még nem rendelkezik egy.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Regisztrálási vagy bejelentkezési szabályzat létrehozása
Összeállítottunk mintatartalmakat próbálhat indításkor két oldalainak használható egy [regisztrálási vagy bejelentkezési szabályzat](active-directory-b2c-reference-policies.md): a [egységes bejelentkezési oldal](active-directory-b2c-reference-ui-customization.md) és a [önellenőrzött attribútumok lap](active-directory-b2c-reference-ui-customization.md). Amikor [a regisztrálási vagy bejelentkezési szabályzat létrehozása](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), adja hozzá a helyi fiókkal (e-mail-cím), a Facebook, a Google és a Microsoft optimális **Identitásszolgáltatók**. Ezek azok a csak identitásszolgáltató használatát, amely elfogadja a minta HTML-tartalmakat.  Ezen identitásszolgáltató használatát egy része azt is megteheti, ha szeretné.

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása
Kell [alkalmazás regisztrálása](active-directory-b2c-app-registration.md) a B2C-bérlőben a szabályzat végrehajtásához használható. Az alkalmazás a regisztrálás után futtatja ténylegesen a regisztrációs szabályzatban használt néhány lehetőség közül választhat:

* Hozzon létre egyet az Azure AD B2C az "Első lépések" szakaszban felsorolt alkalmazások gyors üzembe helyezési [jelentkezzen be, és jelentkezzen be a fogyasztói alkalmazásokba](active-directory-b2c-overview.md).
* Használja az előre elkészített [Azure AD B2C-vel Playground](https://aadb2cplayground.azurewebsites.net) alkalmazás. Ha a playground használata mellett dönt, regisztrálnia kell egy alkalmazást a B2C-bérlőben használatával a **átirányítási URI** `https://aadb2cplayground.azurewebsites.net/`.
* Használja a **Futtatás most** gombra a szabályzat a [az Azure portal](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Szabja testre a házirendet
Testre szabhatja a megjelenését és működését a szabályzatot, meg kell először hozzon létre az adott konvenciók Azure AD B2C használatával HTML és CSS-fájlokat. Ezt követően feltöltheti a statikus tartalmakat egy nyilvánosan elérhető helyre, hogy az Azure AD B2C-vel hozzá tud férni. Ez lehet a saját dedikált webkiszolgálón, az Azure Blob Storage, Azure Content Delivery Network vagy bármely más statikus erőforrás-üzemeltetési szolgáltató. A csak követelmények, hogy a tartalom HTTPS-kapcsolaton keresztül érhető el, és a CORS használatával érhetők el. A statikus tartalmat, a weben is elérhető, ha erre a helyre mutasson, és az ügyfelek számára, hogy a tartalom található a szabályzat szerkesztheti. A [fő felhasználói felület testreszabása a cikk](active-directory-b2c-reference-ui-customization.md) részletesen ismerteti, hogyan működik, az Azure AD B2C-vel testreszabási funkcióról.

Ez az oktatóanyag az alkalmazásában hogy már létrehozta minta hiánytalanul és üzemeltetett, az Azure Blob Storage. A minta tartalma a témában a fiktív vállalat, "Wingtip Toys" nagyon alapszintű testreszabást. Próbálja ki a saját szabályzatban, kövesse az alábbi lépéseket:

1. Jelentkezzen be a bérlő a [az Azure portal](https://portal.azure.com/) , és keresse meg a B2C funkciók paneljére.
2. Kattintson a **regisztrálási vagy bejelentkezési szabályzatok**, kattintson a szabályzatra, majd kattintson a Szerkesztés (például "b2c\_1\_bejelentkezési\_mentése\_bejelentkezési\_a").
3. Kattintson a **oldal-UI testreszabása** , majd **egyesített regisztrálási vagy bejelentkezési oldal**.
4. Váltás a **használata egyéni lap** váltson **Igen**. Az a **egyéni oldal URI-ja** írja be a következőt `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Kattintson az **OK** gombra.
5. Kattintson a **helyi fiók regisztrálási oldala**. Váltás a **egyéni sablon használata** váltson **Igen**. Az a **egyéni oldal URI-ja** írja be a következőt `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Az azonos lépést ismételje meg a **közösségi fiók regisztrálására szolgáló oldal**.
   Kattintson a **OK** kétszer, hogy a felhasználói felület testreszabása paneljeinek a bezárásához.
7. Kattintson a **Save** (Mentés) gombra.

Most kipróbálhatja a testreszabott házirendet. Használhatja a saját alkalmazás vagy az Azure AD B2C-vel playground Ha kívánja, de egyszerűen kattintson a **Futtatás most** parancsot a szabályzat paneljén. A legördülő listából válassza ki az alkalmazását, és válassza ki a megfelelő átirányítási URI-t. Kattintson a **Futtatás most** gombra. Egy új böngészőlapon nyílik meg, és futtathatja a felhasználói élmény, hogyan működik az alkalmazás az új tartalommal helyen keresztül!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>A minta tartalom feltöltése az Azure Blob Storage
Ha szeretné üzemeltetni az oldal tartalmát az Azure Blob Storage használata a saját tárfiók létrehozása, és a B2C segédeszköze használatával a fájlok feltöltése.

### <a name="create-a-storage-account"></a>Create a storage account
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **+ új** > **adatok + tárolás** > **tárfiók**. Szüksége lesz egy Azure Blob Storage-fiók létrehozásához Azure-előfizetés. Regisztrálhat egy ingyenes próbaidőszakot a [Azure-webhelyen](https://azure.microsoft.com/pricing/free-trial/).
3. Adjon meg egy **neve** a storage-fiók (például "contoso"), és válassza ki a megfelelő összetevőket **tarifacsomag**, **erőforráscsoport** és  **Előfizetés**. Győződjön meg arról, hogy rendelkezik-e a **kezdőpulton rögzít** lehetőség be van jelölve. Kattintson a **Create** (Létrehozás) gombra.
4. Lépjen vissza a kezdőpulton, és kattintson az imént létrehozott tárfiókra.
5. Az a **összefoglalás** területén kattintson **tárolók**, és kattintson a **+ Hozzáadás**.
6. Adjon meg egy **neve** a tároló (például "b2c"), majd válassza a **Blob** , a **típus eléréséhez**. Kattintson az **OK** gombra.
7. A létrehozott tárolót fog megjelenni a listában a **Blobok** panelen. Jegyezze fel az URL-címe a tárolóhoz; Ha például azt hasonlóan kell kinéznie `https://contoso.blob.core.windows.net/b2c`. Zárja be a **Blobok** panelen.
8. A storage-fiók panelen kattintson a **kulcsok** írja le az értékeket, és a **Tárfióknevet** és **elsődleges elérési kulcs** mezőket.

> [!NOTE]
> **Elsődleges hozzáférési kulcs** egy fontos biztonsági hitelesítő adat.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>A segítő eszközre és fájlok letöltésére
Letöltheti a [Azure Blob Storage segítő eszközre és .zip-fájlként fájlok](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) vagy klónozhatja a Githubról:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Ez a tárház tartalmaz egy `sample_templates\wingtip` címtár, például HTML, CSS és képeket tartalmazó. E sablonok való hivatkozáshoz a saját Azure Blob Storage-fiókot szüksége lesz a HTML-fájlok szerkesztéséhez. Nyissa meg `unified.html` és `selfasserted.html` , és cserélje le a példányai `https://localhost` saját tárolót, amelyet az előző lépésekben URL-címével. A HTML-fájlok abszolút elérési útját kell használnia, mivel ebben az esetben a HTML-kódot fog kiszolgálása az internetszolgáltatójuk által az Azure AD-ben az tartományba `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>A minta-fájlok feltöltése
Ugyanabban az adattárban, csomagolja ki `B2CAzureStorageClient.zip` , és futtassa a `B2CAzureStorageClient.exe` fájlt. A program egyszerűen töltse fel a címtárban, a tárfiókhoz megadott összes fájlt, és engedélyezze ezeket a fájlokat a CORS-elérését. Ha a fenti lépéseket követte, a HTML és CSS-fájlok most mutatni a tárfiókhoz. Vegye figyelembe, hogy a tárfiók nevét, amely megelőzi a rész `blob.core.windows.net`; például `contoso`. Ellenőrizheti, hogy a tartalom megfelelően lett feltöltve úgy, hogy hozzáférési `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` egy böngészőben. Is [ http://test-cors.org/ ](http://test-cors.org/) , győződjön meg arról, hogy a tartalom már CORS-támogatással. (Tekintse meg "XHR állapota: 200-as" az eredményben.)

### <a name="customize-your-policy-again"></a>A szabályzat újra testreszabása
Most, hogy a minta tartalom feltöltött saját tárfiók, szerkesztenie kell a regisztrációs szabályzatban arra mutató hivatkozás. Ismételje meg a lépéseket a ["Testreszabhatja az"](#customize-your-policy) saját storage-fiók URL-címekkel ezúttal a fenti szakaszban. Például a helyét a `unified.html` fájl lenne `<url-of-your-container>/wingtip/unified.html`.

Most már használhatja a **Futtatás most** gomb vagy alkalmazását, hajtsa végre újból a szabályzatot. Az eredmény kell néznek ki, szinte ugyanúgy működnek – használt azonos Példa HTML és CSS mindkettőre. Azonban a szabályzatok a saját példányát az Azure Blob Storage most hivatkoznak, és szabadon szerkesztheti, és töltse fel újra, hogy a fájlok meg. A HTML és CSS testreszabásáról további információkért tekintse meg a [fő felhasználói felület testreszabása a cikk](active-directory-b2c-reference-ui-customization.md).

