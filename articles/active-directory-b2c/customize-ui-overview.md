---
title: A kezelőfelület testreszabása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a Azure Active Directory B2Ct használó alkalmazásai felhasználói felületét.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d737d010f323a5d5b230091ad07ba530d25d6e51
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949406"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C felhasználói felületének testreszabása

A Azure Active Directory B2C (Azure AD B2C) felhasználói felületének védjegyezése és testreszabása az ügyfelek számára a zökkenőmentes felhasználói élményt nyújtja az alkalmazásában. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a profil szerkesztése és a jelszó-visszaállítás. Ez a cikk bemutatja a felhasználói felület (UI) testreszabásának módszereit a felhasználói folyamatok és az egyéni házirendek esetében.

## <a name="ui-customization-in-different-scenarios"></a>Felhasználói felület testreszabása különböző forgatókönyvekben

A felhasználó felhasználói FELÜLETét többféleképpen testreszabhatja az alkalmazásban, amelyek mindegyike különböző forgatókönyvekhez megfelelő.

### <a name="user-flows"></a>Felhasználói folyamatok

Ha [felhasználói folyamatokat](active-directory-b2c-reference-policies.md)használ, megváltoztathatja a felhasználói folyamatok lapjainak megjelenését a beépített lapelrendezés- *sablonokkal*, illetve a saját HTML-és CSS-fájl használatával. A cikk későbbi részében mindkét módszert tárgyaljuk.

A [Azure Portal](tutorial-customize-ui.md) használatával konfigurálhatja a felhasználói folyamatok felhasználói felületének testreszabását.

### <a name="custom-policies"></a>Egyéni szabályzatok

Ha [egyéni házirendeket](active-directory-b2c-overview-custom.md) használ a regisztrációhoz vagy a bejelentkezéshez, a jelszó-visszaállításhoz vagy a profil szerkesztéséhez az alkalmazásban, [a felhasználói felület testreszabásához használjon házirend-fájlokat](active-directory-b2c-ui-customization-custom.md).

Ha az ügyfél döntése alapján dinamikus tartalmat kell megadnia, használjon olyan egyéni házirendeket, amelyek az [oldal tartalmát dinamikusan módosíthatják](active-directory-b2c-ui-customization-custom-dynamic.md) , a lekérdezési karakterláncban elküldett paraméterektől függően. Megváltoztathatja például a háttérképet a Azure AD B2C regisztrációs vagy bejelentkezési oldalon egy olyan paraméter alapján, amelyet a webes vagy a mobil alkalmazásból továbbít.

### <a name="javascript"></a>JavaScript

Az ügyféloldali JavaScript-kódokat engedélyezheti a [felhasználói folyamatokban](user-flow-javascript-overview.md) és az [Egyéni házirendekben](page-layout.md)is.

### <a name="sign-in-only-ui-customization"></a>Csak bejelentkezési felhasználói felület testreszabása

Ha csak a bejelentkezést látja el, a jelszó alaphelyzetbe állítása és a hitelesítő e-mail-címe mellett az [Azure ad bejelentkezési oldalán](../active-directory/fundamentals/customize-branding.md)használt testreszabási lépéseket is használhatja.

Ha az ügyfelek a bejelentkezés előtt megpróbálják szerkeszteni a profiljaikat, a rendszer átirányítja egy olyan oldalra, amelyet testreszabhat az Azure AD bejelentkezési oldalának testreszabásához használt lépések használatával.

## <a name="page-layout-templates"></a>Lapelrendezés-sablonok

A felhasználói folyamatok több beépített sablont is biztosítanak, amelyek segítségével professzionális arculatot biztosíthat a felhasználói élményhez. Ezek az elrendezési sablonok a saját testreszabásához is kiindulási pontként szolgálhatnak.

A bal oldali menü **Testreszabás** **területén válassza a lapelrendezések lehetőséget** , majd a **sablon**lehetőséget.

![Sablon kiválasztása legördülő menü a Azure Portal felhasználói folyamat lapján](media/customize-ui-overview/template-selection.png)

Ezután válasszon ki egy sablont a listából. Íme néhány példa az egyes sablonok bejelentkezési lapjaira:

| Ocean Blue | Szürke pala | Hagyományos |
|:-:|:-:|:-:|
|![Példa a bejelentkezési oldalon megjelenített Ocean Blue-sablonra](media/customize-ui-overview/template-ocean-blue.png)|![Példa a regisztrálási bejelentkezési oldalon megjelenített szürke sablonra](media/customize-ui-overview/template-slate-gray.png)|![Példa a regisztrálási bejelentkezési oldalon megjelenített klasszikus sablonra](media/customize-ui-overview/template-classic.png)|

Ha kiválaszt egy sablont, a rendszer a kiválasztott elrendezést alkalmazza a felhasználói folyamat összes lapjára, és az egyes lapok URI-ja látható az **Egyéni lap URI** mezőjében.

## <a name="custom-html-and-css"></a>Egyéni HTML és CSS

Azure AD B2C az ügyfél böngészőjében kódot futtat az eltérő [eredetű erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/)nevű megközelítés használatával.

Futásidőben a tartalom betöltődik a felhasználói folyamatba vagy az egyéni házirendbe megadott URL-címről. A felhasználói felület minden lapja betölti a tartalmat az adott laphoz megadott URL-címről. Miután betöltötte a tartalmat az URL-címről, a rendszer egyesít egy, a Azure AD B2C által beszúrt HTML-kódrészlettel, majd megjeleníti a lapot az ügyfél számára.

A felhasználói felület testreszabásához a saját HTML-és CSS-fájljainak használata előtt tekintse át a következő útmutatót:

- Azure AD B2C **egyesít** HTML-tartalmat az oldalaiba. Ne másolja, és próbálja meg módosítani a Azure AD B2C által biztosított alapértelmezett tartalmat. A legjobb megoldás, ha a HTML-tartalmakat a semmiből hozza létre, és az alapértelmezett tartalmat használja hivatkozásként.
- A **JavaScript** a [felhasználói folyamatokhoz](user-flow-javascript-overview.md) és az [Egyéni házirendekhez](javascript-samples.md)is megadható egyéni tartalomban.
- A támogatott **böngésző-verziók** a következők:
  - Internet Explorer 11, 10 és Microsoft Edge
  - Korlátozott támogatás az Internet Explorer 9 és 8 rendszerhez
  - Google Chrome 42,0 és újabb verziók
  - Mozilla Firefox 38,0 és újabb verziók
- Ne tartalmazzon **űrlap-címkéket** a HTML-kódban. Az űrlap címkéi nem akadályozzák a Azure AD B2C által befecskendezett HTML által generált POST műveleteket.

### <a name="where-do-i-store-ui-content"></a>Hol tárolhatom a felhasználói felület tartalmát?

Ha saját HTML-és CSS-fájlokat használ a felhasználói felület testreszabásához, a felhasználói felület tartalmát bármely nyilvánosan elérhető HTTPS-végponton futtathatja, amely támogatja a CORS. Ilyen például az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), a webkiszolgálók, a CDNs, az AWS S3 vagy a fájlmegosztó rendszerek.

A lényeg az, hogy a tartalmat egy nyilvánosan elérhető HTTPS-végponton üzemelteti, amelyen engedélyezve van a CORS. A tartalomban való megadásakor abszolút URL-címet kell használnia.

## <a name="get-started-with-custom-html-and-css"></a>Ismerkedés az egyéni HTML-és CSS-sel

A saját HTML és CSS használatának első lépései a felhasználói élmény oldalain az alábbi irányelvek alapján.

- Hozzon létre jól formázott HTML-tartalmat egy üres `<div id="api"></div>` elemmel, amely valahol a `<body>`található. Ez az elem a Azure AD B2C tartalmának beszúrt helyét jelöli. Az alábbi példa egy minimális oldalt mutat be:

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

- Tartalom üzemeltetése HTTPS-végponton (CORS engedélyezett). A CORS konfigurálásakor engedélyezni kell a GET és a OPTIONs metódust is.
- Hozzon létre vagy szerkesszen egy felhasználói folyamatot vagy egyéni szabályzatot a létrehozott tartalom használatához.

### <a name="html-fragments-from-azure-ad-b2c"></a>HTML-töredékek Azure AD B2C

A következő táblázat felsorolja azokat a HTML-töredékeket, amelyeket Azure AD B2C egyesít a tartalomban található `<div id="api"></div>` elembe.

| Beszúrt oldal | HTML leírása |
| ------------- | ------------------- |
| Identitás-szolgáltató kiválasztása | Azon identitás-szolgáltatók gombjainak listáját tartalmazza, amelyeket az ügyfél a regisztráció vagy a bejelentkezés során választhat. Ezek a gombok olyan közösségi identitás-szolgáltatók, mint például a Facebook, a Google vagy a helyi fiókok (e-mail-cím vagy Felhasználónév alapján). |
| Helyi fiók regisztrálása | Egy űrlapot tartalmaz a helyi fiók regisztrálásához egy e-mail-cím vagy egy Felhasználónév alapján. Az űrlap különböző beviteli vezérlőket tartalmazhat, mint például a szövegbeviteli mező, a jelszó-beviteli mező, a választógomb, az egyszeres kijelölés legördülő lista és a többszörös kijelölés jelölőnégyzet. |
| Közösségi fiók regisztrálása | Akkor jelenhet meg, ha egy közösségi identitás-szolgáltató, például a Facebook vagy a Google egy meglévő fiókjának használatával regisztrál. Akkor használatos, ha az ügyféltől további információkat kell gyűjteni a regisztrációs űrlap használatával. |
| Egyesített regisztráció vagy bejelentkezés | A olyan ügyfelek regisztrációját és bejelentkezését kezeli, akik használhatják a közösségi identitás-szolgáltatókat, például a Facebookot, a Google-t vagy a helyi fiókokat. |
| Többtényezős hitelesítés | Az ügyfelek a regisztrálás vagy a bejelentkezés során ellenőrizhetik a telefonszámokat (szöveg vagy hang használatával). |
| Hiba | Információt nyújt az ügyfélnek. |

## <a name="localize-content"></a>Tartalom honosítása

A HTML-tartalmat honosíthatja a Azure AD B2C bérlő [nyelvi testreszabásainak](active-directory-b2c-reference-language-customization.md) engedélyezésével. A funkció engedélyezése lehetővé teszi, hogy Azure AD B2C továbbítsa az OpenID Connect paramétert `ui-locales` a végponthoz. A Content Server ezt a paramétert használhatja a nyelvspecifikus HTML-lapok megadásához.

A tartalmat a használt területi beállítás alapján különböző helyekről lehet kihúzni. A CORS-kompatibilis végponton beállíthatja, hogy a rendszer egy adott nyelvhez tartozó tartalmat működtessen. Ha a helyettesítő karaktert `{Culture:RFC5646}`használja, hívja meg a megfelelőt.

Az egyéni oldal URI-ja például az alábbihoz hasonló lehet:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

A lapot francia nyelven is betöltheti, ha tartalmat húz a következő helyről:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Példák

A [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) adattárban több minta sablonfájl is megtalálható a githubon.

A sablonok HTML-és CSS-fájljai a [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) könyvtárban találhatók.

## <a name="next-steps"></a>Következő lépések

- Ha **felhasználói folyamatokat**használ, megkezdheti a felhasználói felület testreszabását az oktatóanyag segítségével:

    [Testreszabhatja alkalmazásai felhasználói felületét Azure Active Directory B2Cban](tutorial-customize-ui.md).
- Ha **egyéni házirendeket**használ, megkezdheti a felhasználói felület testreszabását a cikk használatával:

    [Testre szabhatja az alkalmazás felhasználói felületét az Azure Active Directory B2C-ban található egyéni házirend használatával](active-directory-b2c-ui-customization-custom.md).
