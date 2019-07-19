---
title: Tudnivalók a felhasználói felület testreszabásáról a Azure Active Directory B2Cban | Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a Azure Active Directory B2Ct használó alkalmazásai felhasználói felületét.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 13ae1b74acbcab8d623c24d6a7b8d7e1355b80e8
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227156"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Tudnivalók a felhasználói felület testreszabásáról Azure Active Directory B2C

Az Azure Active Directory (Azure AD) B2C által az alkalmazásokhoz használt felhasználói felület (UI) márkájának és testreszabásának lehetősége fontos, hogy zökkenőmentes felhasználói élményt biztosítson ügyfeleinek. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a profil szerkesztése és a jelszó-visszaállítás. Ez a cikk az alkalmazások felhasználói felületének testreszabását segítő információkat tartalmaz.

Az Ön igényeitől függően az alkalmazás felhasználói felületét többféleképpen testreszabhatja. Példa:

- Ha [felhasználói folyamatokat](active-directory-b2c-reference-policies.md) használ a regisztrációhoz, a bejelentkezéshez, a jelszó-visszaállításhoz vagy a profil-szerkesztési élményekhez az alkalmazásban, akkor a [Azure Portal használatával TESTRESZABHATJA a felhasználói felületet](tutorial-customize-ui.md).
- Ha v2 felhasználói folyamatot használ, az [oldalelrendezés sablonnal](#page-layout-templates) megváltoztathatja a felhasználói folyamatok lapjainak megjelenését további testreszabás nélkül. Például a felhasználói folyamat összes lapjára alkalmazhatja az Ocean Blue vagy a pala szürke témákat.
- Ha csak a bejelentkezést, a hozzá tartozó jelszó-visszaállítási lapot és a hitelesítő e-maileket használja, akkor ugyanazokat a testreszabási lépéseket kell használnia, amelyek az [Azure ad bejelentkezési oldalához](../active-directory/fundamentals/customize-branding.md)használatosak.
- Ha az ügyfelek a bejelentkezés előtt megpróbálják szerkeszteni a profiljaikat, a rendszer átirányítja egy olyan oldalra, amelyet az Azure AD bejelentkezési oldalának testreszabásához használt lépések használatával testreszab.
- Ha [egyéni házirendeket](active-directory-b2c-overview-custom.md) használ a regisztrációhoz vagy a bejelentkezéshez, a jelszó-visszaállításhoz vagy a profil szerkesztéséhez az alkalmazásban, akkor [a felhasználói felület testreszabásához házirend-fájlokat](active-directory-b2c-ui-customization-custom.md)használ.
- Ha az ügyfél döntése alapján dinamikus tartalmat kell megadnia, olyan egyéni házirendeket használhat, amelyek a lekérdezési karakterláncban elküldett paraméterektől függően módosíthatják az [oldal tartalmát](active-directory-b2c-ui-customization-custom-dynamic.md) . Például a Azure AD B2C regisztrációs vagy bejelentkezési oldalon megjelenő háttérkép megváltozik a webes vagy mobil alkalmazástól kapott paraméter alapján.
- A JavaScript ügyféloldali kódokat a Azure AD B2C [felhasználói folyamatokban](user-flow-javascript-overview.md) vagy [Egyéni házirendekben](page-layout.md)engedélyezheti.

A Azure AD B2C kódot futtat az ügyfél böngészőjében, és egy modern megközelítést használ az eltérő [eredetű erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/)néven. Futásidőben a tartalom betöltődik a felhasználói folyamatokban vagy házirendekben megadott URL-címről. Különböző URL-címeket adhat meg különböző lapokhoz. Miután betöltötte a tartalmat az URL-címről, a rendszer egyesít egy, a Azure AD B2Cból beszúrt HTML-kódrészlettel, majd megjeleníti az ügyfelet.

Ha saját HTML-és CSS-fájlokat használ a felhasználói felület testreszabásához, a Kezdés előtt tekintse át a következő útmutatót:

- Azure AD B2C egyesít HTML-tartalmat az oldalaiba. Ne másolja, és próbálja meg módosítani a Azure AD B2C által biztosított alapértelmezett tartalmat. A legjobb megoldás, ha a HTML-tartalmakat a semmiből hozza létre, és az alapértelmezett tartalmat használja hivatkozásként.
- A JavaScript mostantól az egyéni tartalomban is szerepelhet.
- A támogatott böngésző-verziók a következők:
    - Internet Explorer 11, 10 és Microsoft Edge
    - Korlátozott támogatás az Internet Explorer 9 és 8 rendszerhez
    - Google Chrome 42,0 és újabb verziók
    - Mozilla Firefox 38,0 és újabb verziók
- Győződjön meg arról, hogy nem tartalmazza az űrlap címkéit a HTML-kódban, mert az a befecskendezett HTML által generált POST műveletekkel ütközik Azure AD B2C.

## <a name="page-layout-templates"></a>Lapelrendezés-sablonok

A v2 felhasználói folyamatok esetében olyan előre megtervezett sablont választhat, amely lehetővé teszi, hogy az alapértelmezett lapok jobban megtekintsenek, és a saját testreszabásának megfelelő módon szolgálnak.

A bal oldali menüben a **Testreszabás**területen válassza a **lapelrendezések**elemet. Ezután válassza a **sablon (előzetes verzió)** lehetőséget.

![Sablon kiválasztása legördülő menü a Azure Portal felhasználói folyamat lapján](media/customize-ui-overview/template.png)

Válasszon ki egy sablont a listából. Az **Ocean Blue** sablon például a következő elrendezést alkalmazza a felhasználói folyamatok lapjaira:

![Példa a bejelentkezési oldalon megjelenített Ocean Blue-sablonra](media/customize-ui-overview/ocean-blue.png)

Ha kiválaszt egy sablont, a rendszer a kiválasztott elrendezést alkalmazza a felhasználói folyamat összes lapjára, és az egyes lapok URI-ja látható az **Egyéni lap URI** mezőjében.

## <a name="where-do-i-store-ui-content"></a>Hol tárolhatom a felhasználói felület tartalmát?

Ha saját HTML-és CSS-fájlokat használ a felhasználói felület testreszabásához, bárhol tárolhatja a felhasználói felület tartalmát, például az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ban, a webkiszolgálókon, a CDNs, az AWS S3-ban vagy a fájlmegosztást használó rendszerekben. A lényeg az, hogy a tartalmat egy nyilvánosan elérhető HTTPS-végponton üzemelteti, amelyen engedélyezve van a CORS. A tartalomban való megadásakor abszolút URL-címet kell használnia.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

A következő műveleteket végezheti el a felhasználói felület testreszabásához:

- Hozzon létre jól formázott HTML-tartalmat egy `<div id="api"></div>` olyan üres elemmel, amely `<body>`valahol a-ben található. Ez az elem a Azure AD B2C tartalmának beszúrt helyét jelöli. Az alábbi példa egy minimális oldalt mutat be:

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

- Tartalom üzemeltetése HTTPS-végponton (CORS engedélyezett). A CORS konfigurálásakor engedélyezni kell a GET és a OPTIONs metódust is.
- A CSS használatával a Azure AD B2C beszúrható felhasználói felületi elemeket alakíthatja át az oldalára. Az alábbi példa egy egyszerű CSS-fájlt mutat be, amely a regisztrációs beszúrt HTML-elemek beállításait is tartalmazza:

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

- Hozzon létre vagy szerkesszen egy szabályzatot a létrehozott tartalom használatához.

A következő táblázat felsorolja azokat a HTML-töredékeket, amelyeket Azure ad B2C egyesít `<div id="api"></div>` a tartalomban található elembe.

| Beszúrt oldal | HTML leírása |
| ------------- | ------------------- |
| Identitás-szolgáltató kiválasztása | Azon identitás-szolgáltatók gombjainak listáját tartalmazza, amelyeket az ügyfél a regisztráció vagy a bejelentkezés során választhat. Ezek a gombok olyan közösségi identitás-szolgáltatók, mint például a Facebook, a Google vagy a helyi fiókok (e-mail-cím vagy Felhasználónév alapján). |
| Helyi fiók regisztrálása | Egy űrlapot tartalmaz a helyi fiók regisztrálásához egy e-mail-cím vagy egy Felhasználónév alapján. Az űrlap különböző beviteli vezérlőket tartalmazhat, mint például a szövegbeviteli mező, a jelszó-beviteli mező, a választógomb, az egyszeres kijelölés legördülő lista és a többszörös kijelölés jelölőnégyzet. |
| Közösségi fiók regisztrálása | Akkor jelenhet meg, ha egy közösségi identitás-szolgáltató, például a Facebook vagy a Google egy meglévő fiókjának használatával regisztrál. Akkor használatos, ha az ügyféltől további információkat kell gyűjteni a regisztrációs űrlap használatával. |
| Egyesített regisztráció vagy bejelentkezés | A olyan ügyfelek regisztrációját és bejelentkezését kezeli, akik használhatják a közösségi identitás-szolgáltatókat, például a Facebookot, a Google-t vagy a helyi fiókokat. |
| Multi-Factor Authentication | Az ügyfelek a regisztrálás vagy a bejelentkezés során ellenőrizhetik a telefonszámokat (szöveg vagy hang használatával). |
| Hiba | Információt nyújt az ügyfélnek. |


## <a name="how-do-i-localize-content"></a>Hogyan a tartalom honosítása?

A HTML-tartalmat honosíthatja a Azure AD B2C bérlő [nyelvi testreszabásainak](active-directory-b2c-reference-language-customization.md) engedélyezésével. A funkció engedélyezése lehetővé teszi Azure ad B2C számára, hogy továbbítsa az `ui-locales` Open ID kapcsolódási paramétert a végpontnak. A Content Server ezt a paramétert használhatja a nyelvspecifikus HTML-lapok megadásához.

A tartalmat a használt területi beállítás alapján különböző helyekről lehet kihúzni. A CORS-kompatibilis végponton beállíthatja, hogy a rendszer egy adott nyelvhez tartozó tartalmat működtessen. Ha a (z) {Culture: RFC5646} helyettesítő karaktert használja, akkor meg kell hívnia a megfelelő értéket. Előfordulhat például, hogy az egyéni oldal URI-ja `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`hasonlít. A lapot francia nyelven is betöltheti, ha tartalmat húz a következőből:`https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Példák

A testreszabási példákhoz töltse le és tekintse át ezeket a [sablonfájl-fájlokat](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>További lépések

- Ha felhasználói folyamatokat használ, megkezdheti a felhasználói felület testreszabását az oktatóanyag segítségével: [Testreszabhatja alkalmazásai felhasználói felületét Azure Active Directory B2Cban](tutorial-customize-ui.md).
- Ha egyéni házirendeket használ, megkezdheti a felhasználói felület testreszabását a cikk használatával: [Testre szabhatja az alkalmazás felhasználói felületét az Azure Active Directory B2C-ban található egyéni házirend használatával](active-directory-b2c-ui-customization-custom.md).

