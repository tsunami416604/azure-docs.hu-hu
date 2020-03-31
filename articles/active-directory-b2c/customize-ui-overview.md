---
title: A kezelőfelület testreszabása
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan szabhatja testre az Azure Active Directory B2C-t használó alkalmazások felhasználói felületét.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 839e13dffc7d15b8cd258dd4b7dda6776223d052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051741"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>A felhasználói felület testreszabása az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) által az ügyfelek számára megjelenített felhasználói felület márkajelzése és testreszabása zökkenőmentes felhasználói élményt nyújt az alkalmazásban. Ezek közé tartozik a regisztráció, a bejelentkezés, a profilszerkesztés és a jelszó alaphelyzetbe állítása. Ez a cikk bemutatja a felhasználói felület (UI) testreszabási módszereit mind a felhasználói folyamatok, mind az egyéni házirendek esetében.

## <a name="ui-customization-in-different-scenarios"></a>A felhasználói felület testreszabása különböző esetekben

Számos módja van az alkalmazás felhasználói felületének testreszabására, amelyek mindegyike különböző forgatókönyvekhez megfelelő.

### <a name="user-flows"></a>Felhasználói folyamatok

Ha [felhasználói folyamatokat](user-flow-overview.md)használ, a felhasználói folyamatlapok megjelenését beépített *lapelrendezési sablonok,* illetve saját HTML- és CSS-kód használatával módosíthatja. Mindkét módszert a cikk későbbi részében tárgyaljuk.

Az [Azure Portal](tutorial-customize-ui.md) segítségével konfigurálhatja a felhasználói felület testreszabását a felhasználói folyamatokhoz.

> [!TIP]
> Ha csak a felhasználói folyamatoldalak szalagcímemblékét, háttérképét és háttérszínét szeretné módosítani, próbálkozzon a cikk későbbi részében ismertetett [Vállalati márkajelzés (előzetes verzió)](#company-branding-preview) funkcióval.

### <a name="custom-policies"></a>Egyéni szabályzatok

Ha [egyéni házirendeket](custom-policy-overview.md) használ a regisztráció vagy a bejelentkezés, a jelszó-visszaállítás vagy a profilszerkesztés biztosításához az alkalmazásban, a házirendfájlok segítségével [testreszabhatja a felhasználói felületet.](custom-policy-ui-customization.md)

Ha dinamikus tartalmat kell megadnia az ügyfél döntése alapján, használjon olyan egyéni házirendeket, amelyek [dinamikusan módosíthatják az oldal tartalmát](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) a lekérdezési karakterláncban küldött paramétertől függően. Módosíthatja például a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési lapon a webről vagy mobilalkalmazásból átadott paraméter alapján.

### <a name="javascript"></a>JavaScript

Az ügyféloldali JavaScript-kódot felhasználói [folyamatokban](user-flow-javascript-overview.md) és [egyéni házirendekben](page-layout.md)is engedélyezheti.

### <a name="sign-in-only-ui-customization"></a>Csak a bejelentkezési felhasználói felület testreszabása

Ha csak bejelentkezést biztosít, valamint a hozzá tartozó jelszó-visszaállítási lapot és az ellenőrző e-maileket, kövesse ugyanazokat a testreszabási lépéseket, amelyeket az [Azure AD bejelentkezési lapjához](../active-directory/fundamentals/customize-branding.md)használnak.

Ha az ügyfelek a bejelentkezés előtt megpróbálják a profiljukat, a rendszer átirányítja őket egy olyan lapra, amelyet az Azure AD bejelentkezési lap testreszabásához használt lépések keltesz.

## <a name="page-layout-templates"></a>Lapelrendezési sablonok

A felhasználói folyamatok számos beépített sablont biztosítanak, amelyek közül választhat, hogy professzionális megjelenést adjon a felhasználói élmény oldalaknak. Ezek az elrendezéssablonok a saját testreszabásának kiindulópontjaként is szolgálhatnak.

A bal oldali menü **Testreszabás parancsában** válassza a **Lapelrendezések,** majd a **Sablon**lehetőséget.

![Az Azure Portal felhasználói folyamatlapjának sablonkiválasztási legördülő legördülő menüje](media/customize-ui-overview/template-selection.png)

Ezután válasszon ki egy sablont a listából. Íme néhány példa az egyes sablonok bejelentkezési lapjaira:

| Óceán kék | Palaszürke | Klasszikus |
|:-:|:-:|:-:|
|![Példa a bejelentkezési bejelentkezési oldalon megjelenített Ocean Blue sablonra](media/customize-ui-overview/template-ocean-blue.png)|![Példa a bejelentkezési bejelentkezési oldalon megjelenített Palaszürke sablonra](media/customize-ui-overview/template-slate-gray.png)|![Példa a regisztrációs bejelentkezési oldalon megjelenített klasszikus sablonra](media/customize-ui-overview/template-classic.png)|

Amikor kiválaszt egy sablont, a kijelölt elrendezés a felhasználói folyamat minden oldalára vonatkozik, és az egyes lapok URI-ja látható az **Egyéni oldal URI** mezőjében.

## <a name="custom-html-and-css"></a>Egyéni HTML és CSS

Ha saját házirend-elrendezést szeretne megtervezni a testreszabott HTML- és CSS-kóddal, ezt úgy teheti meg, hogy a házirendben szereplő elrendezésnevek mindegyikéhez átkapcsolja az "Egyéni oldaltartalom használata" gombot. Kérjük, kövesse az alábbi utasításokat az egyéni elrendezés konfigurációk:

Az Azure AD B2C az ügyfél böngészőjében futtatja a kódot az [Eredetközi erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/)nevű megközelítés használatával.

Futásidőben a tartalom a felhasználói folyamatban vagy az egyéni házirendben megadott URL-címről töltődik be. A felhasználói élmény minden oldala az adott oldalhoz megadott URL-címről tölti be a tartalmát. Miután a tartalom betöltése az URL-címről, az Azure AD B2C által beszúrt HTML-töredékkel egyesül, majd a lap megjelenik az ügyfél számára.

A felhasználói felület testreszabásához a saját HTML- és CSS-fájlok használata előtt tekintse át az alábbi útmutatást:

- Az Azure AD B2C **egyesíti a** HTML-tartalmat a lapokba. Ne másolja, és próbálja meg módosítani az Azure AD B2C által biztosított alapértelmezett tartalmat. A legjobb, ha a HTML-tartalmat a semmiből hozza létre, és az alapértelmezett tartalmat használja referenciaként.
- **A JavaScript** mind a felhasználói [folyamatok,](user-flow-javascript-overview.md) mind az egyéni házirendek egyéni tartalmai között szerepelhet. [custom policies](javascript-samples.md)
- A böngésző támogatott **verziói** a következők:
  - Internet Explorer 11, 10 és Microsoft Edge
  - Korlátozott támogatás az Internet Explorer 9 és 8 böngészőhöz
  - Google Chrome 42.0 és újabb
  - Mozilla Firefox 38.0 és újabb
  - Safari iOS és macOS rendszerhez, 12-es és újabb verzió
- Ne vegyen fel **űrlapcímkéket** a HTML-kódba. Űrlapcímkék zavarják az Azure AD B2C által bevitt HTML által létrehozott POST-műveleteket.

### <a name="where-do-i-store-ui-content"></a>Hol tárolhatom a felhasználói felület tartalmát?

Ha saját HTML- és CSS-fájljait használja a felhasználói felület testreszabásához, a felhasználói felület tartalmát bármely nyilvánosan elérhető HTTPS-végponton, amely támogatja a CORS-t, üzemeltetheti. Például [Az Azure Blob storage](../storage/blobs/storage-blobs-introduction.md), webkiszolgálók, CDN-ek, AWS S3 vagy fájlmegosztó rendszerek.

A lényeg az, hogy a tartalom egy nyilvánosan elérhető HTTPS-végponton található, amelyen a CORS engedélyezve van. Abszolút URL-címet kell használnia, amikor megadja azt a tartalomban.

## <a name="get-started-with-custom-html-and-css"></a>Ismerkedés az egyéni HTML- és CSS-sel

Az alábbi irányelvek betartásával ismerkedje meg saját HTML- és CSS-fájljával a felhasználói élményben használt oldalakon.

- Hozzon létre jól formázott `<div id="api"></div>` HTML-tartalmat `<body>`egy üres elemmel, amely valahol a ban található. Ez az elem jelzi, ahol az Azure AD B2C tartalom van beszúrva. A következő példa egy minimális oldalt mutat be:

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

- A CSS használatával stilírozhatja az Azure AD B2C által az oldalra beszúrt felhasználói felületi elemeket. A következő példa egy egyszerű CSS-fájlt mutat be, amely a befecskendezett HTML-elemek regisztrációs beállításait is tartalmazza:

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

- A tartalom https-végponton való üzemeltetése (cors engedélyezett). A CORS konfigurálásakor mind a GET, mind a OPTIONS kérésmetódust engedélyezni kell.
- Hozzon létre vagy szerkesztsen egy felhasználói folyamatot vagy egyéni házirendet a létrehozott tartalom használatához.

### <a name="html-fragments-from-azure-ad-b2c"></a>HTML-töredékek az Azure AD B2C-ből

Az alábbi táblázat felsorolja azokat a HTML-töredékeket, `<div id="api"></div>` amelyeket az Azure AD B2C egyesít a tartalomban található elembe.

| Beszúrt oldal | A HTML leírása |
| ------------- | ------------------- |
| Identitásszolgáltató kiválasztása | Az identitásszolgáltatók gombjainak listáját tartalmazza, amelyek közül az ügyfél választhat a regisztráció vagy a bejelentkezés során. Ezek a gombok olyan közösségi identitásszolgáltatókat tartalmaznak, mint a Facebook, a Google vagy a helyi fiókok (e-mail cím vagy felhasználónév alapján). |
| Helyi fiókra való feliratkozás | A helyi fiókra való feliratkozáshoz egy e-mail cím vagy felhasználónév alapján készült űrlapot tartalmaz. Az űrlap különböző beviteli vezérlőket tartalmazhat, például szövegbeviteli mezőt, jelszóbeviteli mezőt, választógombot, egyszeres legördülő mezőket és többszörös jelölőnégyzeteket. |
| Közösségi fiókra való feliratkozás | Akkor jelenhet meg, ha egy meglévő fiókkal regisztrál egy közösségi identitásszolgáltatótól, például a Facebooktól vagy a Google-tól. Akkor használatos, ha további információkat kell gyűjteni az ügyféltől egy regisztrációs űrlap segítségével. |
| Egyesített regisztráció vagy bejelentkezés | Kezeli a regisztrációt és az ügyfelek bejelentkezését, akik használhatják a közösségi identitásszolgáltatókat, például a Facebookot, a Google-t vagy a helyi fiókokat. |
| Multi-Factor Authentication | Az ügyfelek a regisztráció vagy a bejelentkezés során ellenőrizhetik telefonszámukat (szöveges vagy hangalapú) telefonszámukat. |
| Hiba | Hibainformációt nyújt a vevőnek. |

## <a name="company-branding-preview"></a>Vállalati márkajelzés (előzetes verzió)

Az Azure Active Directory [Company márkajelzésével](../active-directory/fundamentals/customize-branding.md)szalagcímem, háttérkép és háttérszín segítségével testreszabhatja a felhasználói folyamatlapjait.

A felhasználói folyamatlapok testreszabásához először konfigurálja a vállalati márkajelzést az Azure Active Directoryban, majd engedélyezze azt a felhasználói folyamatok lapelrendezésében az Azure AD B2C-ben.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Vállalati arculat konfigurálása

Először a banner embléma, a háttérkép és a háttérszín beállításával a **vállalat márkajelzésén**belül.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. A **Kezelés csoportban**válassza **a Vállalati márkajelzés lehetőséget.**
1. Kövesse a [Márkajelzés hozzáadása a szervezet Azure Active Directory bejelentkezési lapjára című](../active-directory/fundamentals/customize-branding.md)lépéseit.

Az Azure AD B2C-ben a vállalati márkajelzés konfigurálásakor tartsa szem előtt ezeket a dolgokat:

* Az Azure AD B2C-ben a vállalati márkajelzés jelenleg a **háttérképre**, **a szalagcímemre**és a **háttérszín** testreszabására korlátozódik. A vállalati márkajelzési ablaktábla többi tulajdonsága, például a **Speciális beállítások**nem *támogatott.*
* A felhasználói folyamat oldalain a háttérszín a háttérkép betöltése előtt jelenik meg. Javasoljuk, hogy válasszon olyan háttérszínt, amely szorosan illeszkedik a háttérkép színeihez a simább betöltésérdekében.
* A szalagcím embléma megjelenik a felhasználóknak küldött ellenőrző e-mailekben, amikor regisztrációs felhasználói folyamatot kezdeményeznek.

### <a name="enable-branding-in-user-flow-pages"></a>Márkajelzés engedélyezése a felhasználói folyamatoldalakon

Miután konfigurálta a vállalati márkajelzést, engedélyezze azt a felhasználói folyamatokban.

1. Az Azure Portal bal oldali menüjében válassza az **Azure AD B2C lehetőséget.**
1. A **Házirendek**csoportban válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek engedélyezni szeretné a vállalati márkajelzést. A vállalati márkajelzés **nem támogatott** a Bejelentkezés *i 1-es* és *a Profil szerkesztési v1* felhasználói folyamattípusok esetében.
1. A **Testreszabás csoportban**válassza a **Lapelrendezések**lehetőséget, majd válassza ki a márkázni kívánt elrendezést. Válassza például **az Egyesített regisztráció vagy a bejelentkezési lapot.**
1. A **Lapelrendezés verziójához (előzetes verzió)** válassza az **1.2.0-s** vagy újabb verziót.
1. Kattintson a **Mentés** gombra.

Ha a felhasználói folyamat összes oldalát márkára szeretné katti, állítsa be a felhasználói folyamat minden egyes oldalelrendezéséhez tartozó lapelrendezési verziót.

![Lapelrendezés kiválasztása az Azure AD B2C-ben az Azure Portalon](media/customize-ui-overview/portal-02-page-layout-select.png)

Ez a megjegyzésekkel ellátva egy egyéni szalagcím emblémát és háttérképet mutat a *Regisztráció és bejelentkezés* felhasználói folyamatlapon, amely az Ocean Blue sablont használja:

![Az Azure AD B2C által kiszolgált, márkajelzéssel ellátott regisztrációs/bejelentkezési oldal](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Vállalati márkajelzési eszközök használata egyéni HTML-ben

Ha a vállalati márkajelzési kellékeket egyéni HTML-kódban szeretné használni, adja hozzá a következő címkéket a `<div id="api">` címkén kívül:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A kép forráshelyébe, hogy a háttérkép és banner logó. Az Egyéni [HTML- és CSS-használat](#get-started-with-custom-html-and-css) szakaszban leírtak szerint css-osztályok használatával stilírozza és helyezze el az eszközöket az oldalon.

## <a name="localize-content"></a>Tartalom honosítása

A HTML-tartalom honosítása az Azure AD B2C-bérlő nyelvi [testreszabásának](user-flow-language-customization.md) engedélyezésével. A funkció engedélyezése lehetővé teszi, hogy az Azure AD B2C továbbítsa az OpenID Connect paramétert `ui-locales` a végpontra. A tartalomkiszolgáló ezzel a paraméterrel nyelvspecifikus HTML-lapokat adhat meg.

A tartalom a használt területi beállítás alapján különböző helyekről is lehúzható. A CORS-kompatibilis végponton beállíthat egy mappastruktúrát az adott nyelvek tartalmának tárolására. A megfelelőt fogja hívni, ha a `{Culture:RFC5646}`helyettesítő karakter értékét használja.

Az egyéni oldal URI-ja például így nézhet ki:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Az oldalt franciául töltheti be, ha tartalmat húz ki a következő ből:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Példák

A [GitHub B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) tárházában több mintasablon-fájlt is találhat.

A sablonokban lévő HTML- és CSS-fájlok a [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) könyvtárban találhatók.

## <a name="next-steps"></a>További lépések

- Ha **felhasználói folyamatokat**használ, az oktatóanyaggal elkezdheti testre szabni a felhasználói felületet:

    [Az Azure Active Directory B2C-ben testreszabhatja az alkalmazások felhasználói felületét.](tutorial-customize-ui.md)
- Ha **egyéni házirendeket**használ, a következő cikkel kezdheti el a felhasználói felület testreszabását:

    [Az alkalmazás felhasználói felületének testreszabása az Azure Active Directory B2C egyéni házirendjével.](custom-policy-ui-customization.md)
