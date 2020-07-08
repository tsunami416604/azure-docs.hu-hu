---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80117089"
---
## <a name="use-custom-page-content"></a>Egyéni oldal tartalmának használata

Az oldal felhasználói felületének testreszabási funkciója segítségével testre szabhatja az egyéni szabályzatok megjelenését és működését. Lehetősége van fenntartani az alkalmazása és az Azure AD B2C közötti márkabeli és vizuális egységességet is.

### <a name="how-it-works"></a>Működés

A Azure AD B2C a kódot az ügyfél böngészőjében futtathatja, ha az [erőforrás-megosztást (CORS)](https://www.w3.org/TR/cors/)használja. Futásidőben a tartalom betöltődik a felhasználói folyamat vagy az egyéni házirendben megadott URL-címről. A felhasználói felület minden lapja betölti a tartalmat az adott laphoz megadott URL-címről. Miután betöltötte a tartalmat az URL-címről, a rendszer egyesít egy, a Azure AD B2C által beszúrt HTML-kódrészlettel, majd megjeleníti a lapot az ügyfél számára.

![Egyéni oldal tartalmának margója](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Egyéni HTML-oldal tartalma

Hozzon létre egy HTML-oldalt saját arculatával az egyéni oldal tartalmának kiszolgálásához. Ez a lap lehet statikus `*.html` oldal vagy dinamikus oldal, például .net, Node.js vagy php.

Az egyéni oldal tartalma bármilyen HTML-elemet tartalmazhat, beleértve a CSS-t és a JavaScriptet is, de nem tartalmazhat olyan nem biztonságos elemeket, mint az iframe elemek. Az egyetlen szükséges elem egy div-elem, amely a `id` következőre van beállítva: `api` , például ez `<div id="api"></div>` a html-oldalon.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Az alapértelmezett Azure AD B2C lapok testreszabása

Ahelyett, hogy az egyéni oldal tartalmait a semmiből hozza létre, testreszabhatja az Azure AD B2C's alapértelmezett oldalának tartalmát.

A következő táblázat felsorolja a Azure AD B2C által biztosított alapértelmezett oldal tartalmát. Töltse le a fájlokat, és használja kiindulási pontként a saját egyéni oldalai létrehozásához.

| Alapértelmezett lap | Description | Tartalom definíciójának azonosítója<br/>(csak egyéni házirend) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hiba lap**. Ez az oldal akkor jelenik meg, ha kivételt vagy hibát észlel. | *API. error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Saját maga által érvényesített oldal**. Ezt a fájlt egyéni oldalként használhatja a közösségi fiók regisztrálása lap, a helyi fiók regisztrálása oldal, a helyi fiók bejelentkezési oldala, a jelszó alaphelyzetbe állítása és egyebek. Az űrlap különböző beviteli vezérlőket tartalmazhat, például a következőket: szövegbeviteli mező, jelszó-beviteli mező, választógomb, egyszeres kijelölés legördülő lista és többszörös kijelölés jelölőnégyzet. | *API. localaccountsignin*, API. *localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication oldal**. Ezen a lapon a felhasználók megtekinthetik a telefonszámokat (szöveg vagy hang használatával) a regisztráció vagy a bejelentkezés során. | *API. phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil frissítése lap**. Ez a lap egy űrlapot tartalmaz, amelyet a felhasználók frissíthetnek a profiljuk frissítéséhez. Ez az oldal hasonló a közösségi fiók regisztrációs oldalához, a jelszó-beviteli mezők kivételével. | *API. selfasserted. profileUpdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési oldal**. Ez a lap kezeli a felhasználói regisztrációt és a bejelentkezési folyamatot. A felhasználók a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy a helyi fiókok használatával használhatják a felhasználókat. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Az oldal tartalmának üzemeltetése

Ha saját HTML-és CSS-fájlokat használ a felhasználói felület testreszabásához, a felhasználói felület tartalmát a CORS támogató bármely nyilvánosan elérhető HTTPS-végponton üzemeltetheti. Például: [Azure Blob Storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure app Services](/azure/app-service/), webkiszolgálók, CDNs, AWS S3 vagy fájlmegosztó rendszerek.

## <a name="guidelines-for-using-custom-page-content"></a>Irányelvek egyéni lapok tartalmának használatához

- Használjon abszolút URL-címet, ha olyan külső erőforrásokat is tartalmaz, mint például a média, a CSS és a JavaScript-fájlok a HTML-fájlban.
- Ha a 1.2.0 vagy újabb [verzióját](../articles/active-directory-b2c/page-layout.md) használja, a `data-preload="true"` HTML-címkékben lévő attribútumot hozzáadhatja a CSS és a JavaScript betöltési sorrendjének szabályozásához. A `data-preload=true` -ben az oldal a felhasználó előtt jön létre. Ez az attribútum segít megakadályozni, hogy az oldal "vibrál" legyen a CSS-fájl előzetes betöltésével anélkül, hogy a felhasználóhoz nem tartozó HTML-fájlt kellene megjeleníteni. A következő HTML-kódrészlet a címke használatát mutatja be `data-preload` .
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Javasoljuk, hogy kezdje az alapértelmezett oldal tartalmával, és készítsen rá rá.
- A [felhasználói folyamatokhoz](../articles/active-directory-b2c/user-flow-javascript-overview.md) és az [Egyéni szabályzatokhoz](../articles/active-directory-b2c/javascript-samples.md)is hozzáadhat JavaScript-tartalmat az egyéni tartalomban.
- A támogatott böngésző-verziók a következők:
  - Internet Explorer 11, 10 és Microsoft Edge
  - Korlátozott támogatás az Internet Explorer 9 és 8 rendszerhez
  - Google Chrome 42,0 és újabb verziók
  - Mozilla Firefox 38,0 és újabb verziók
  - Safari iOS és macOS rendszerekhez, 12-es vagy újabb verzió
- Biztonsági korlátozások miatt Azure AD B2C nem támogatja `frame` , `iframe` vagy `form` HTML-elemeket.

## <a name="custom-page-content-walkthrough"></a>Egyéni oldal tartalmi útmutatója

A folyamat áttekintése:

1. Készítse elő az egyéni oldal tartalmának (nyilvánosan elérhető, CORS-kompatibilis HTTPS-végpont) üzemeltetésére szolgáló helyet.
1. Töltse le és szabja testre az alapértelmezett oldal tartalmi fájlját, például: `unified.html` .
1. Közzéteheti az egyéni oldal tartalmát a nyilvánosan elérhető HTTPS-végponton.
1. Állítsa be a CORS a webalkalmazáshoz.
1. Irányítsa a szabályzatot az egyéni házirend-tartalom URI-ra.

### <a name="1-create-your-html-content"></a>1. HTML-tartalom létrehozása

Hozzon létre egy egyéni oldal tartalmát a termék márkaneve nevével a címben.

1. Másolja a következő HTML-kódrészletet. Jól formázott HTML5 egy üres elemmel, amelynek neve *\<div id="api"\>\</div\>* a címkék között található *\<body\>* . Ez az elem azt jelzi, hogy hol kell beszúrni Azure AD B2C tartalmat.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Illessze be a másolt kódrészletet egy szövegszerkesztőbe, majd mentse a fájlt *customize-ui.html*néven.

> [!NOTE]
> A HTML-űrlap elemei a biztonsági korlátozások miatt törlődnek, ha login.microsoftonline.com használ. Ha HTML-űrlap elemeket szeretne használni az egyéni HTML-tartalomban, [használja a b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Azure Blob Storage-fiók létrehozása

Ebben a cikkben az Azure Blob Storage-t használjuk a tartalom üzemeltetéséhez. Dönthet úgy, hogy webkiszolgálón üzemelteti a tartalmat, de engedélyeznie kell a [CORS a webkiszolgálón](https://enable-cors.org/server.html).

A HTML-tartalom blob Storage-ban való üzemeltetéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **központi** menüben válassza az **új**  >  **Storage**  >  **Storage-fiók**lehetőséget.
1. Válasszon egy **előfizetést** a Storage-fiókjához.
1. Hozzon létre egy **erőforráscsoportot** , vagy válasszon ki egy meglévőt.
1. Adja meg a Storage-fiók egyedi **nevét** .
1. Válassza ki a tárolási fiók **földrajzi helyét** .
1. A **telepítési modell** maradhat a **Resource Managerben**.
1. A **teljesítmény** továbbra is **standard**maradhat.
1. Változtassa meg a **fiók típusát** a **blob Storage**-ban.
1. A **replikáció** az **ra-GRS**is maradhat.
1. A **hozzáférési szint** is **megmaradhat**.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a Storage-fiók létrehozásához.
    A telepítés befejezése után a Storage- **fiók** lap automatikusan megnyílik.

#### <a name="21-create-a-container"></a>2,1 tároló létrehozása

Ha nyilvános tárolót szeretne létrehozni a blob Storage-ban, hajtsa végre a következő lépéseket:

1. A bal oldali menüben a **blob Service** alatt válassza a **Blobok**elemet.
1. Válassza a **+ tároló**elemet.
1. A **név**mezőbe írja be a *root*értéket. A név lehet a választott név, például a *contoso*, de ebben a példában az egyszerűség kedvéért használjuk a *root* -t.
1. **Nyilvános hozzáférési szint**esetén válassza a **blob**lehetőséget, majd **az OK gombot**.
1. Válassza ki a **root** elemet az új tároló megnyitásához.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 az egyéni oldal tartalmi fájljainak feltöltése

1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki a **fájl kiválasztása**melletti mappa ikont.
1. Navigáljon a lapra, és válassza ki **customize-ui.html**, amelyet korábban hozott létre az oldal felhasználói felületének testreszabása szakaszban.
1. Ha egy almappában szeretne feltölteni, bontsa ki a **speciális** elemet, és adja meg a mappa nevét a **feltöltés mappába**.
1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki a feltöltött **customize-ui.html** blobot.
1. Az **URL-cím** szövegmező jobb oldalán válassza a **Másolás vágólapra** ikont az URL-cím vágólapra másolásához.
1. A böngészőben nyissa meg a vágólapra másolt URL-címet, és ellenőrizze, hogy a feltöltött blob elérhető-e. Ha nem érhető el, például ha `ResourceNotFound` hibát tapasztal, győződjön meg arról, hogy a tároló hozzáférési típusa **blob**.

### <a name="3-configure-cors"></a>3. a CORS konfigurálása

Az alábbi lépések végrehajtásával konfigurálja a blob Storage-t az idegen eredetű erőforrás-megosztáshoz:

1. A menüben válassza a **CORS**lehetőséget.
1. Az **engedélyezett eredetek**mezőben adja meg a következőt: `https://your-tenant-name.b2clogin.com` . Cserélje le a helyére `your-tenant-name` a Azure ad B2C bérlő nevét. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor használja az összes kisbetűt.
1. Az **engedélyezett módszerek**esetében válassza a mindkettő és a lehetőséget `GET` `OPTIONS` .
1. Az **engedélyezett fejlécek**mezőbe írjon be egy csillagot (*).
1. A közzétett **fejlécek**esetében írjon be egy csillagot (*).
1. A **Max Age**értéknél adja meg a 200 értéket.
1. Kattintson a **Mentés** gombra.

#### <a name="31-test-cors"></a>3,1 teszt CORS

Ellenőrizze, hogy készen áll-e a következő lépések végrehajtásával:

1. Ismételje meg a CORS konfigurálása lépést. Az **engedélyezett eredetek**mezőben adja meg a következőt:`https://www.test-cors.org`
1. Navigáljon a [www.test-CORS.org](https://www.test-cors.org/) 
1. A **távoli URL-cím** mezőben illessze be a HTML-fájl URL-címét. Például: `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Válassza a **küldési kérelem**lehetőséget.
    Ennek az eredménynek kell lennie `XHR status: 200` . 
    Ha hibaüzenetet kap, ellenőrizze, hogy helyesek-e a CORS beállításai. Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy meg kell nyitnia egy privát böngészési munkamenetet a CTRL + SHIFT + P billentyűkombináció lenyomásával.
