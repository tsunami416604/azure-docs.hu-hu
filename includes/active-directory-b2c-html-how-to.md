---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117089"
---
## <a name="use-custom-page-content"></a>Egyéni oldaltartalom használata

A lap felhasználói felületének testreszabási funkciójával testreszabhatja az egyéni házirendek megjelenését és hangulatát. Lehetősége van fenntartani az alkalmazása és az Azure AD B2C közötti márkabeli és vizuális egységességet is.

### <a name="how-it-works"></a>Működés

Az Azure AD B2C az ügyfél böngészőjében futtatja a kódot az [Originok közötti erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/)használatával. Futásidőben a tartalom a felhasználói folyamatban vagy az egyéni házirendben megadott URL-címről töltődik be. A felhasználói élmény minden oldala az adott oldalhoz megadott URL-címről tölti be a tartalmát. Miután a tartalom betöltése az URL-címről, az Azure AD B2C által beszúrt HTML-töredékkel egyesül, majd a lap megjelenik az ügyfél számára.

![Egyéni oldaltartalom-margó](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Egyéni HTML-laptartalom

Hozzon létre egy HTML-lapot saját márkajelzéssel, hogy az egyéni oldaltartalmat jelenítse meg. Ez az oldal `*.html` lehet statikus oldal, vagy dinamikus lap, mint a .NET, Node.js vagy PHP.

Az egyéni laptartalom tartalmazhat html elemeket, beleértve a CSS-t és a JavaScriptet, de nem tartalmazhat nem biztonságos elemeket, például iframe-eket. Az egyetlen szükséges elem egy `id` div `api`elem , `<div id="api"></div>` például ez a HTML oldalon.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Az alapértelmezett Azure AD B2C-lapok testreszabása

Ahelyett, hogy az egyéni lap tartalmát a semmiből, testreszabhatja az Azure AD B2C alapértelmezett laptartalmát.

Az alábbi táblázat az Azure AD B2C által biztosított alapértelmezett laptartalmat sorolja fel. Töltse le a fájlokat, és használja őket kiindulási pontként a saját egyéni lapok létrehozásához.

| Alapértelmezett lap | Leírás | Tartalomdefiníció azonosítója<br/>(csak egyéni házirend) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **A hibalap**. Ez a lap akkor jelenik meg, ha kivétel vagy hiba lép fel. | *api.error* |
| [önérvényesítő.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Önérvényesített oldal**. Ezt a fájlt egyéni laptartalomként használhatja a közösségi fiókok raklapjához, a helyi fiókregisztrációs laphoz, a helyi fiók bejelentkezési lapjához, a jelszó alaphelyzetbe állításához stb. Az űrlap különböző beviteli vezérlőket tartalmazhat, például szövegbeviteli mezőt, jelszóbeviteli mezőt, választógombot, egyszeres legördülő mezőket és többszörös jelölőnégyzeteket. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Többtényezős hitelesítési lap**. Ezen az oldalon a felhasználók a regisztráció vagy a bejelentkezés során ellenőrizhetik telefonszámukat (szöveges vagy hangalapú) telefonszámukat. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profilfrissítési lap**. Ez a lap olyan űrlapot tartalmaz, amelyhez a felhasználók hozzáférhetnek a profilfrissítéshez. Ez az oldal hasonló a közösségi fiók regisztrációs lapjához, kivéve a jelszóbeviteli mezőket. | *api.selfasserted.profileupdate* |
| [egyesített.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési oldal**. Ez az oldal kezeli a felhasználói regisztrációs és bejelentkezési folyamatot. A felhasználók használhatnak vállalati identitásszolgáltatókat, közösségi identitásszolgáltatókat, például a Facebookot vagy a Google+-t, vagy helyi fiókokat. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Az oldal tartalmának üzemeltetése

Ha saját HTML- és CSS-fájljait használja a felhasználói felület testreszabásához, a felhasználói felület tartalmát a CORS-t támogató nyilvánosan elérhető HTTPS-végpontokon is üzemeltetje. Például [az Azure Blob storage](../articles/storage/blobs/storage-blobs-introduction.md), [az Azure App Services](/azure/app-service/), webkiszolgálók, CDN-ek, AWS S3 vagy fájlmegosztó rendszerek.

## <a name="guidelines-for-using-custom-page-content"></a>Az egyéni oldaltartalom használatának irányelvei

- Abszolút URL-címet használjon, ha külső erőforrásokat, például adathordozót, CSS- és JavaScript-fájlokat foglal bele a HTML-fájlba.
- Az 1.2.0-s és újabb [verziójú lapelrendezés imázsának](../articles/active-directory-b2c/page-layout.md) használatával hozzáadhatja az attribútumot a `data-preload="true"` HTML-címkékhez a CSS és a JavaScript betöltési sorrendjének szabályozásához. A `data-preload=true`lehetőséggel az oldal a felhasználó számára való bemutatás előtt épül fel. Ez az attribútum segít megakadályozni, hogy az oldal "villódzzon" a CSS fájl előtöltésével, anélkül, hogy az UN-styled HTML megjelenne a felhasználó számára. A következő HTML-kódrészlet a `data-preload` címke használatát mutatja.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Azt javasoljuk, hogy kezdje az alapértelmezett oldaltartalommal, és építsen a tetejére.
- A JavaScriptet az egyéni tartalomba is beillesztheti a [felhasználói folyamatokhoz](../articles/active-directory-b2c/user-flow-javascript-overview.md) és az [egyéni házirendekhez.](../articles/active-directory-b2c/javascript-samples.md)
- A böngésző támogatott verziói a következők:
  - Internet Explorer 11, 10 és Microsoft Edge
  - Korlátozott támogatás az Internet Explorer 9 és 8 böngészőhöz
  - Google Chrome 42.0 és újabb
  - Mozilla Firefox 38.0 és újabb
  - Safari iOS és macOS rendszerhez, 12-es és újabb verzió
- Biztonsági korlátozások miatt az Azure AD B2C `frame` `iframe`nem `form` támogatja a , vagy HTML-elemeket.

## <a name="custom-page-content-walkthrough"></a>Egyéni oldaltartalom-forgatókönyv

Íme egy áttekintés a folyamatról:

1. Készítsen elő egy helyet az egyéni oldaltartalom (nyilvánosan elérhető, CORS-kompatibilis HTTPS-végpont) üzemeltetéséhez.
1. Az alapértelmezett laptartalom-fájl letöltése `unified.html`és testreszabása, például .
1. Tegye közzé az egyéni lap tartalmát a nyilvánosan elérhető HTTPS-végpont.
1. Állítsa be a több forrásból származó erőforrás-megosztást (CORS) a webalkalmazáshoz.
1. Irányítsa a házirendet az egyéni házirend-tartalom URI-ra.

### <a name="1-create-your-html-content"></a>1. A HTML-tartalom létrehozása

Hozzon létre egy egyéni oldaltartalmat a termék márkanevével a címben.

1. Másolja a következő HTML-kódrészletet. Ez jól formázott HTML5 egy üres elem nevű * \<div\>\<id ="api" / div\> * található a * \<test\> * címkéket. Ez az elem azt jelzi, hogy hol kell beszúrni az Azure AD B2C-tartalmat.

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

1. Illessze be a másolt kódrészletet egy szövegszerkesztőbe, majd mentse a fájlt *testreszabó-ui.html*néven.

> [!NOTE]
> A HTML-űrlapelemek biztonsági korlátozások miatt törlődnek, ha login.microsoftonline.com használ. Ha HTML-űrlapelemeket szeretne használni az egyéni HTML-tartalomban, [használja a b2clogin.com.](../articles/active-directory-b2c/b2clogin.md)

### <a name="2-create-an-azure-blob-storage-account"></a>2. Hozzon létre egy Azure Blob-tárfiókot

Ebben a cikkben az Azure Blob storage-t használjuk a tartalom tárolásához. Választhatja azt is, hogy a tartalmat webkiszolgálón szeretné-e üzemeltetni, de engedélyeznie kell [a CORS-t a webkiszolgálón.](https://enable-cors.org/server.html)

Ha a HTML-tartalmat a Blob storage-ban szeretné tárolni, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A **Központ** menüben válassza az **Új** > **tárterület-fiók****Storage** > lehetőséget.
1. Válasszon **egy előfizetést** a tárfiókhoz.
1. Hozzon létre **egy erőforráscsoportot,** vagy jelöljön ki egy meglévőt.
1. Adjon meg egy egyedi **nevet** a tárfiókhoz.
1. Válassza ki a **tárfiók földrajzi helyét.**
1. **A telepítési modell** maradhat **az Erőforrás-kezelő**.
1. **A teljesítmény** továbbra is **szabványos**maradhat.
1. A **Fiók-természet** módosítása **Blob storage-ra.**
1. **A replikáció** maradhat **RA-GRS**.
1. **A hozzáférési szint** továbbra is **gyakori.**
1. Válassza **a Véleményezés + létrehozás** lehetőséget a tárfiók létrehozásához.
    A központi telepítés befejezése után a **Tárfiók** lap automatikusan megnyílik.

#### <a name="21-create-a-container"></a>2.1 Tároló létrehozása

Ha nyilvános tárolót szeretne létrehozni a Blob storage-ban, hajtsa végre az alábbi lépéseket:

1. A bal oldali menü **Blob szolgáltatás** csoportjában válassza a **Blobok**lehetőséget.
1. Válassza a **+Container lehetőséget.**
1. A **Név mezőbe**írja be a gyökér *beírását.* A név lehet az Ön által választott név, például *contoso*, de ebben a példában az egyszerűség kedvéért *gyökeret* használunk.
1. **A nyilvános hozzáférési szint**esetén válassza a **Blob**lehetőséget, majd az **OK lehetőséget.**
1. Válassza ki a **root** az új tároló megnyitásához.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Egyéni oldaltartalom-fájlok feltöltése

1. Válassza a **Feltöltés** lehetőséget.
1. Jelölje ki a **Fájl kijelölése**című mappaikont.
1. Keresse meg és válassza ki a **testreszabó-ui.html**fájlt, amelyet korábban létrehozott az Oldal felhasználói felületének testreszabása szakaszban.
1. Ha almappába szeretne feltölteni, **bontsa** ki a Speciális elemet, és írja be a mappa nevét a **Feltöltés mappába**mezőbe.
1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki a feltöltött **customize-ui.html** blobot.
1. Az **URL-cím** szövegmezőjének jobb oldalán jelölje ki a **Másolás vágólapra** ikont, ha az URL-címet a vágólapra szeretné másolni.
1. A webböngészőben keresse meg a másolt URL-címet, és ellenőrizze, hogy a feltöltött blob elérhető-e. Ha nem érhető el, például `ResourceNotFound` ha hibát észlel, győződjön meg arról, hogy a tároló-hozzáférési típus **blobra**van állítva.

### <a name="3-configure-cors"></a>3. Cors konfigurálása

Konfigurálja a Blob-tárolót az eredetközi erőforrások megosztásához az alábbi lépések végrehajtásával:

1. A menüben válassza a **CORS**lehetőséget.
1. Az **Engedélyezett eredetek**mezőbe írja be a be írt `https://your-tenant-name.b2clogin.com`értéket. Cserélje `your-tenant-name` le az Azure AD B2C-bérlő nevét. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor használja az összes kisbetűt.
1. Az **Engedélyezett módszerek csoportban**válassza a és `GET` a `OPTIONS`lehetőséget.
1. **Az Engedélyezett fejlécek**mezőbe írjon be csillagot (*).
1. **A Kitett fejlécek**mezőbe írjon be egy csillagot (*).
1. A **maximális életkor**mezőbe írja be a 200 értéket.
1. Kattintson a **Mentés** gombra.

#### <a name="31-test-cors"></a>3.1 Teszt CORS

Ellenőrizze, hogy készen áll-e a következő lépések végrehajtásával:

1. Ismételje meg a CORS konfigurálása lépést. Az **Engedélyezett eredet mezőbe**írja be a`https://www.test-cors.org`
1. Navigálás [a www.test-cors.org](https://www.test-cors.org/) 
1. A **Távoli URL-cím** mezőbe illessze be a HTML-fájl URL-címét. Például: `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Válassza **a Küldési kérelem lehetőséget.**
    Az eredmény `XHR status: 200`kell . 
    Ha hibaüzenetet kap, győződjön meg arról, hogy a CORS-beállítások helyesek. Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy meg kell nyitnia egy privát böngészési munkamenetet a Ctrl+Shift+P billentyűkombináció lenyomásával.
