---
title: Tudnivalók a felhasználói felület testreszabása az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory B2C-t használó alkalmazások számára a felhasználói felület testreszabása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cec9e5bfea16e5ba11f8cd7324acf15188fb907a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634061"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Tudnivalók az Azure Active Directory B2C felhasználói felület testreszabása

Lehetővé teszi, hogy saját arculat és testre szabhatja a felhasználói felület (UI), amely az Azure Active Directory (Azure AD) B2C arra szolgál, az alkalmazások fontos zökkenőmentes élményt biztosít az ügyfélnek. Ezek a tapasztalatok közé tartozik a regisztrációs, bejelentkezési, profil szerkesztésére és jelszó alaphelyzetbe állítása. Ez a cikk segítséget nyújt az alkalmazások a felhasználói felület testreszabása.

Esetén ezek a tapasztalatok, attól függően, az igényeinek, hogy a felhasználói felület testreszabása az alkalmazás különböző módon. Példa:

- Ha használ [beépített szabályzatok](active-directory-b2c-reference-policies.md) regisztrálási vagy bejelentkezési, a jelszó alaphelyzetbe állítása, illetve profilszerkesztést funkciókat az alkalmazásban, használja a [a felhasználói felület testreszabása az Azure Portalon](tutorial-customize-ui.md).
- Ha Ön biztosítani a bejelentkezési csak, a hozzájuk tartozó jelszó-visszaállítási oldalra, és ellenőrzési e-mailt küld, használja a testreszabási lépéseket, amelyeket egy [az Azure AD bejelentkezési oldal](../active-directory/fundamentals/customize-branding.md).
- Ha az ügyfelek kipróbálják, szerkesztheti a profilját, mielőtt bejelentkeznek, a rendszer átirányítja egy oldal, testre szabhatja az Azure AD bejelentkezési oldal testreszabása használt lépésekben.
- Használata [egyéni szabályzatok](active-directory-b2c-overview-custom.md) adnia regisztrálási vagy bejelentkezési, a jelszót alaphelyzetbe állítása, illetve -profilszerkesztést az alkalmazásban, használhatja [házirendfájljait testreszabni a felhasználói Felületet](active-directory-b2c-ui-customization-custom.md).
- Ha meg kell adnia egy ügyfél döntés a dinamikus tartalmak, használhatja a [egyéni szabályzatot, amely módosíthatja lapon tartalom](active-directory-b2c-ui-customization-custom-dynamic.md) függően egy lekérdezési karakterláncban elküldött paraméter. Például a háttérkép, az Azure AD B2C-vel regisztrálási vagy bejelentkezési oldalon megváltozik, egy paramétert, amely adja át a webes vagy mobilalkalmazás alapján.

Az Azure AD B2C az ügyfél böngészőjében kódja fut, és egy modern néven ismert megközelítés [eltérő eredetű erőforrások megosztása (CORS)](http://www.w3.org/TR/cors/). Futásidőben a tartalom betöltése a házirendben megadott URL-címről. Különböző URL-eket különböző oldalain adhatja meg. Tartalom betöltését a URL-CÍMÉT, miután egy HTML-részlet beszúrni az Azure AD B2C-ből, és ezután megjelenik, az ügyfél és az egyesítés.

A Kezdés előtt tekintse át a következő útmutatást:

- Az Azure AD B2C egyesíti az oldal HTML-tartalmakat. Ne másolja, és próbálja meg módosítani az alapértelmezett tartalom, amely az Azure AD B2C-t biztosít. Célszerű hozhat létre a teljesen új HTML-tartalmakat, és használja az alapértelmezett tartalom hivatkozásként van listázva.
- Biztonsági okokból nem engedélyezett a tartalomban lévő JavaScript szükséges.
- Támogatott böngésző verziók a következők: 
    - Az Internet Explorer 11., 10-es és a peremhálózaton
    - Az Internet Explorer 9 és 8 korlátozott támogatása
    - Google Chrome 42.0 vagy újabb verzió
    - Mozilla Firefox 38.0 vagy újabb verzió
- Győződjön meg arról, hogy nem adja meg űrlap címkék a HTML-ben, mivel ez rontja a POST műveletek az Azure AD B2C-ből az injektált HTML által generált.

## <a name="where-do-i-store-ui-content"></a>Hol tárolja a felhasználói felület tartalmat?

A tartalom bárhonnan, például a felhasználói felület üzemeltethető [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md), webalkalmazás-kiszolgálók, CDN, az AWS S3, vagy a megosztási fájlrendszerek. A fontos, hogy Ön üzemeltető egy nyilvánosan elérhető HTTPS-végpontokat CORS-támogatással rendelkező tartalmát. A tartalomban lévő megadásakor egy abszolút URL-címet kell használnia.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

A felhasználói felület testreszabása a következők elvégzését:

- Hozzon létre egy üres tartalom megfelelően formázott HTML `<div id="api"></div>` elem található valahol a `<body>`. A elem jelek, ahol az Azure AD B2C-tartalom egészül ki. Az alábbi példa egy minimális lapját jeleníti meg:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- A tartalmakat a HTTPS-végpont állomás (a CORS engedélyezve). Mindkét LEKÉRÉSE, és a beállítások kérési metódusainak engedélyezni kell a CORS konfigurálása során.
- A CSS segítségével stílus az Azure AD B2C-vel szúr be a lap felhasználói felületi elemeket. Az alábbi példa bemutatja egy egyszerű CSS-fájlt, amely a regisztrációs injektált HTML-elemek beállításait is tartalmazza:

    ```css 
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Hozzon létre, vagy az Ön által létrehozott tartalom használatára a szabályzatot.

A következő táblázat felsorolja az Azure AD B2C-vel egyesít HTML-töredékek a `<div id="api"></div>` elem található a tartalmat.

| Beszúrt lap | A HTML-leírás |
| ------------- | ------------------- |
| Identitás-szolgáltató kiválasztása | Gombok listája tartalmazza identitásszolgáltatókat, amelyek az ügyfél során regisztrálási vagy bejelentkezési közül választhat. Ezekre a gombokra közé tartozik a közösségi Identitásszolgáltatók, például Facebook, Google vagy a helyi fiókok (e-mail-cím vagy a felhasználó neve alapján). |
| Helyi fiók regisztrálási | Egy e-mail-cím vagy felhasználónév alapján helyi fiók regisztrációs űrlapot tartalmaz. Az űrlap különböző bemeneti vezérlőelemekből, például a bemeneti szövegmezőt, bejegyzés mezőben, választógomb, egyszeri kiválasztásos legördülő listák és többszörös kiválasztási jelölőnégyzetet is tartalmazhat. |
| Közösségi fiók regisztrációs | Előfordulhat, hogy jelennek meg, amikor regisztrál egy közösségi identitásszolgáltatót, mint a Facebook, Google vagy a meglévő fiók használata. Ha további információt kell gyűjteni a regisztrációs űrlap segítségével ügyfél használható. |
| Egyesített regisztrálási vagy bejelentkezési | Mind előfizetési, és jelentkezzen be az ügyfeleink, akik közösségi Identitásszolgáltatók, például Facebook, Google vagy a helyi fiókok is kezeli. |
| Multi-Factor Authentication | Ügyfelek regisztrálási vagy bejelentkezési során ellenőrizni tudja a telefonszámokat, (a szöveges vagy szóbeli). |
| Hiba | Tájékoztatást nyújt az ügyfélnek. |


## <a name="how-do-i-localize-content"></a>Hogyan tartalom honosítása?

A HTML-tartalom engedélyezésével a honosítás [nyelvi testreszabás](active-directory-b2c-reference-language-customization.md) az Azure AD B2C-bérlőben. Ez a funkció lehetővé teszi, hogy az Azure AD B2C-vel továbbítja az Open ID Connect paraméter `ui-locales` a végponthoz. A webtartalom-kiszolgáló a paraméter használatával adja meg a HTML-lapok nyelvét.

A használt területi beállítása alapján különböző helyen is kéri le tartalmat. A CORS-kompatibilis végpont beállíthatja a mappastruktúrát gazdagép tartalom bizonyos nyelveken. Ha a helyettesítő karaktert tartalmazó értéket {kulturális környezet: RFC5646}, felhívjuk a megfelelőt. Például az egyéni oldal URI-t a következőhöz hasonló lehet `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Az oldal francia nyelvű kiindulásként tartalom betöltése `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Példák

Példák testreszabási, töltse le, majd tekintse át a [sablonfájlokat minta](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>További lépések

- Beépített szabályzatokat használ, ha az oktatóanyag a felhasználói felület testreszabása elkezdheti: [az alkalmazásait az Azure Active Directory B2C, a felhasználói felület testreszabása](tutorial-customize-ui.md).
- Ha egyéni szabályzatokat használ, az a cikk a felhasználói felület testreszabása elkezdheti: [egyéni szabályzat használata az Azure Active Directory B2C az alkalmazás a felhasználói felület testreszabása](active-directory-b2c-ui-customization-custom.md).

