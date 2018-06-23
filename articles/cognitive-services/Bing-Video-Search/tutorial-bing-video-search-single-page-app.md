---
title: Bing egyoldalas videó fájlkeresés alkalmazás |} Microsoft Docs
description: A videó Bing keresési API használatát egy egyoldalas webalkalmazást a ismerteti.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 11/01/2017
ms.author: v-gedod
ms.openlocfilehash: 55f662721e007e03c8f43f19d8b905e755cfe1d8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349030"
---
# <a name="tutorial-single-page-video-search-app"></a>Oktatóanyag: Egyoldalas videó fájlkeresés alkalmazás
Videó Bing keresési API lehetővé teszi a weben, és eredményt videó vonatkozó keresési lekérdezés. Az oktatóanyag azt a lapon a keresési eredmények megjelenítendő a Bing keresési API-t használó egyoldalas-webalkalmazás létrehozása. Az alkalmazás HTML, CSS és JavaScript összetevőket tartalmazza.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> A JSON és a HTTP-fejlécek kattintáskor a lap alján a JSON-válasz és a HTTP-kérelem információk megjelenítése. Ezen adatok akkor lehet hasznos, ha a szolgáltatás fel.

![JSON-NÁ, HTTP nyers eredmények](./media/json-http-raw-results.png)

Ez az oktatóanyag alkalmazás bemutatja, hogyan:
> [!div class="checklist"]
> * Végezze el a Bing videó keresési API-hívás JavaScript
> * Keresési beállítások átadása a Bing keresési API
> * Videó keresési eredmények megjelenítéséhez, vagy választhatóan a weblapokat, híreket és képek
> * Keresési időkereteket a 24 órát, a múlt hét, hónap vagy az összes rendelkezésre álló idő
> * A keresési eredmények lap
> * A Bing ügyfél azonosítója és API előfizetés kulcs kezelése
> * Előforduló hibák kezelésének

Az oktatóprogram lap nincs teljes mértékben; bármely külső keretrendszerek, a stíluslapok, vagy a képfájlok nem használ. Csak széles körben támogatott JavaScript nyelvi funkciókat használ, és minden nagyobb webböngésző az aktuális verzióival működik.

Ebben az oktatóanyagban arról lesz szó a forráskód kijelölt részei. A teljes [forráskód](tutorial-bing-video-search-single-page-app-source.md) érhető el. A példa futtatásához, másolja és illessze be a kódot egy szövegszerkesztőbe, és mentse a fájt `bing.html`.

## <a name="app-components"></a>Alkalmazások összetevői
Bármely egyoldalas webalkalmazást, mint ez az oktatóanyag alkalmazás három rész tartalmazza:

> [!div class="checklist"]
> * HTML - struktúra és a lap tartalma határozza meg
> * CSS - határozza meg az oldal megjelenítési módja
> * JavaScript - meghatározza az oldal viselkedését

A HTML-és CSS hagyományos, így az útmutató nem tárgyalja azt. A HTML-KÓDBAN a keresés, amelyben a felhasználó beírja egy lekérdezést, majd keresési beállítások tartalmazza. Az űrlap JavaScript, amelyet a keresési használatával kapcsolódik a `onsubmit` attribútuma a `<form>` címke:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
A `onsubmit` kezelő azt `false`, amely az űrlap tartja a kiszolgáló nem továbbíthatók. A JavaScript-kód végzi a munka a szükséges információk begyűjtése az űrlap és a keresés végrehajtása.

A HTML is tartalmaz, a részlegek (HTML `<div>` címkék) hol jelenjenek meg a keresési eredmények között.

## <a name="managing-subscription-key"></a>Előfizetés kulcs kezelése

Kívánja kerülni a Bing keresési API-előfizetés kulcs szerepeljenek a kódot, használjuk a böngésző állandó tároló tárolja a kulcsot. A kulcsot tárolják, mielőtt azt a felhasználói kulcs kérni. Ha a kulcs később elutasította az API-t, azt érvénytelenné válnak a tárolt kulcs, a program ismét kéri a felhasználó.

Meghatároztuk `storeValue` és `retrieveValue` függvények, amelyek használja a `localStorage` (nem minden böngésző támogatja) objektum vagy egy cookie-t. A `getSubscriptionKey()` függvény használja ezeket a funkciókat tárolásához és lekéréséhez a felhasználói kulcsot.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
A HTML `<form>` címke `onsubmit` hívások a `bingWebSearch` függvény által visszaadott a keresési eredmények között. `bingWebSearch` használja a `getSubscriptionKey()` minden egyes lekérdezés hitelesítéséhez. Ahogy az az előző definíció `getSubscriptionKey` bekéri a felhasználótól a kulcsot, ha a kulcs nem lett megadva. A kulcsot majd tárolja a folyamatos használata az alkalmazás.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása
Az alábbi ábrán látható, a lekérdezés szöveges mezőbe, majd a keresés meghatározó beállítások.

![Bing hírek keresési beállítások](media/video-search-options.png)

A HTML-űrlaphoz, a következő nevű elemeket tartalmazza:

|Elem|Leírás|
|-|-|
| `where` | A legördülő menüből a kereséshez használt piaci (a hely és a nyelvi) kiválasztásához. |
| `query` | A szövegmezőbe írja be a keresőkifejezést. |
| `modules` | Az eredmények eredményeit, illetve a kapcsolódó videók adott modulok elősegítő jelölőnégyzeteket. |
| `when` | A Keresés a legutóbbi naponta, hetente vagy havonta opcionálisan korlátozó legördülő menü. |
| `safe` | A jelölőnégyzet, amely azt jelzi, hogy "felnőtt" eredményeket szűrheti a Bing biztonságos keresés funkció segítségével. |
| `count` | Rejtett mező. Keresési eredmények vissza az egyes kérelmek száma. Módosítsa a laponként kevesebb vagy további eredmények megtekintése céljából. |
| `offset`|  Rejtett mező. A kérelem; az első keresési eredmény eltolása a lapozáshoz használt. Lesz visszaállítva `0` az új kérelemhez. |

> [!NOTE]
> Bing webes keresés kínál más lekérdezési paramétereket. Csak néhány őket használunk.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Például a `SafeSearch` tényleges API-hívásban paraméter lehet `strict`, `moderate`, vagy `off`, a `moderate` az alapértelmezett alatt. Az űrlap – számára, azonban a jelölőnégyzet, amelynek csak kétállapotú használja. A JavaScript-kód alakítja át, ezt a beállítást az egyik `strict` vagy `off` (`moderate` nem használatos).

## <a name="performing-the-request"></a>A kérelem végrehajtása
A lekérdezés, a beállítások karakterlánc és az API-kulcsot a `BingWebSearch` függvény egy `XMLHttpRequest` objektum gombra a kérelem a Bing keresési végpontnak.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleOnLoad);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
A HTTP-kérelem sikeres befejezését követően, a JavaScript-hívásokat a `load` eseménykezelő, `handleOnLoad()`, kezelni egy sikeres HTTP GET kérést az API-t. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Ha hiba lép fel a keresési művelet, a Bing hírek Search API nem 200-as HTTP - állapotkódot adja vissza, és tartalmazza a hiba adatait a JSON-NÁ válaszul. Emellett a kérelmező sebessége korlátozott, ha a az API-t egy üres választ ad vissza.
A sikeres HTTP-kérelmek does *nem* feltétlenül jelenti azt, hogy a keresési szolgáltatás sikeresen befejeződött. 

Nagy részét a kódot is az előző funkciók hibakezelés van kijelölve. Hibák fordulhatnak elő a következő szakaszokban:

|Fázis|A lehetséges hibák|Kezeli|
|-|-|-|
|A JavaScript request objektumon felépítése|Érvénytelen URL|`try`/`catch` letiltása|
|A kérést|Hálózati hiba, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|A keresés végrehajtása|Érvénytelen kérelemben érvénytelen JSON sebességhatárok|a tesztek `load` eseménykezelő|

Hibák hívásával kezeli `renderErrorMessage()` a hibával kapcsolatos ismert bármely adatokkal. Ha a válasz a teljes gauntlet hiba teszten megfelelt, nevezzük `renderSearchResults()` a keresési eredmények megjelennek a lapon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése
A keresési eredmények megjelenítése a fő funkciója `renderSearchResults()`. Ez a funkció a JSON a Bing hírek keresési szolgáltatás által visszaadott veszi, és ez a beállítás a hírek és a kapcsolódó keres, ha van ilyen.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the resuts to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
A keresési eredmények vissza, a legfelső szintű `value` a JSON-NÁ válaszul objektum. Azt adja meg azokat a függvény `renderResultsItems()`, amely megismétli a rajtuk keresztül, és HTML egyes elemek megjelenítéséhez a függvényt. Az eredményül kapott HTML küld vissza `renderSearchResults()`, ahol azt bekerülnek a `results` osztás a lapon.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

A Bing hírek Search API legfeljebb négy különböző típusú kapcsolódó saját legfelső szintű objektum eredményeket ad vissza. Ezek a következők:

|Kapcsolat|Leírás|
|-|-|
|`pivotSuggestions`|Lecseréli az eredeti search pivot szót egy másik lekérdezések. Például ha a "red virág", lehet, hogy a pivot szót "red", és előfordulhat, hogy a pivot javaslat "sárga virág."|
|`queryExpansions`|Azok a lekérdezések, amelyek az eredeti keresés szűkítéséhez további feltételek hozzáadásával. Ha a "Microsoft Surface", a lekérdezés bővítése lehet például "Microsoft Surface Pro."|
|`relatedSearches`|Az eredeti keresési megadott más felhasználók is megadott lekérdezések. Például ha a "Mount Rudolf", a kapcsolódó keresés lehet "Mt. Saint Helens."|
|`similarTerms`|A hasonló jelentése van az eredeti keresési lekérdezések. Ha a "iskolákat", egy hasonló kifejezés lehet például "oktatási."|

A korábban már látott `renderSearchResults()`, azt csak leképezési a `relatedItems` vonatkozó javaslatokat és a hely a kapott a lap nézetállapotán oldalsávon hivatkozásokat tartalmaz.

## <a name="rendering-result-items"></a>Eredmény elemek megjelenítése

A a JavaScript-kód az objektum `searchItemRenderers`, is tartalmaz *jelentéselemeket:* függvény alapján, amelyek HTML minden milyen keresési eredmények. A videó keresés lapon csak használ `videos`. Tekintse meg a többi oktatóanyag különféle típusú jelentéselemeket.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Egy megjelenítő függvény is fogadja el a következő paraméterekkel:

|Paraméter|Leírás|
|-|-|
|`item`| A JavaScript objektumra, amely a elem tulajdonságai, például az URL-CÍMÉT és leírását.|
|`index`| Az index, az eredmény elem a gyűjteményben.|
|`count`| A keresési eredmény cikk gyűjtemény elemeinek száma.|

A `index` és `count` paraméterek képes használni a el a szám, speciális HTML-KÓDBAN a elején vagy végén egy gyűjteményhez, a sortörések beszúrása elemek, bizonyos számú után, és így tovább. Ha egy leképező nem kell ezt a funkciót, azt nem kell a két paraméterek elfogadásához.

A `video` leképező látható a következő javascript-kivonata. Minden eredmény sem használja a videók végpont, típusú `Videos`. A `searchItemRenderers` jelennek meg a következő kódszegmensben arra a helyre.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

A megjelenítő funkciót:
> [!div class="checklist"]
> * Létrehoz egy bekezdéscímkén, úgy, hogy hozzárendel a `images` osztály, és leküldéses értesítések, a html-tömbhöz.
> * Kiszámítja a miniatűr képméret (width rögzített, 60 képpont magasság számított arányosan).
> * A HTML buildek `<img>` címkén belül, hogy a kép miniatűr megjelenítéséhez. 
> * A HTML buildek `<a>` címkéket, amelyek a lemezkép és az azt tartalmazó lap.
> * A leírást, információkat jelenít meg a lemezkép és a helyet, mert nem hoz létre.

A miniatűrök méretének is szerepel a `<img>` címke és a `h` és `w` mezők a Miniatűr URL-címben. A [Bing miniatűr szolgáltatás](resize-and-crop-thumbnails.md) kézbesíti a miniatűr nézete pontosan ez a méret.

## <a name="persisting-client-id"></a>Persisting ügyfél-azonosító
A Bing keresési API-k válaszainak tartalmazhat egy `X-MSEdge-ClientID` fejlécet tartalmazta, amely vissza az API-t egymást követő kéréseket kell küldeni. Ha több Bing keresési API-k vannak használatban, az azonos ügyfél-Azonosítót kell használható azokat, ha lehetséges.

Így a `X-MSEdge-ClientID` fejléc lehetővé teszi, hogy a Bing API-k az összes felhasználó keresések, amely két fontos előnnyel jár.

Először lehetővé teszi a Bing keresőmotor keresések található eredmények, amelyek jobban megfeleljenek a felhasználói környezet túli alkalmazása. Ha a felhasználó a korábban keresést kihajózás kapcsolódó feltételek, például egy újabb keresse meg a "csomó" Előfordulhat, hogy lehetőleg vonatkozó adatokat ad vissza csomó kihajózás használt.

Második a Bing véletlenszerűen jelölje ki felhasználók számára, hogy mielőtt széles körben elérhető új szolgáltatások. Így az azonos ügyfél-Azonosítót minden egyes kérelemmel biztosítja, hogy a felhasználók, akik jelenik meg a szolgáltatás mindig látja. Az ügyfél-azonosító nélkül a felhasználó jelenhet meg a szolgáltatás megjelennek és eltűnnek, látszólag véletlenszerű, a keresési eredmények között.

Böngésző biztonsági házirendek (CORS) előfordulhat, hogy a `X-MSEdge-ClientID` rendelkezésre állása, a JavaScript fejléc. Ez a korlátozás következik be, amikor a keresési válasz van egy eltérő eredetű az oldalról, amely azt kéri. Éles környezetben eleget kell tennie ezt a házirendet, amelyet a weblap megegyező tartományban lévő API-hívás kiszolgálóoldali parancsfájl üzemeltetnie. Mivel a parancsfájlt a weblapként azonos forrásból a `X-MSEdge-ClientID` fejléc JavaScript számára elérhető lesz.

> [!NOTE]
> Webes alkalmazás éles végre kell hajtania a kérelem kiszolgálóoldali. Ellenkező esetben a Bing keresési API-kulcsot kell szerepelnie a weblap, amelyen érhető el számára, akik megtekinti az adatforrás. Az API előfizetés kulcs, még akkor is, kérelmeire jogosulatlan felek, ezért fontos, hogy a kulcs alatt minden használatának számlázása.

Fejlesztési célokra szolgál hogy a Bing webes keresési API-kérelem a CORS-proxyn keresztül. Ilyen proxy válaszát rendelkezik egy `Access-Control-Expose-Headers` fejléc adott whitelists válaszfejlécek és elérhetővé válnak a JavaScript nyelvvel.

Akkor is könnyen lehetővé teszi az oktatóanyag alkalmazásnak, az ügyfél eléréséhez Tevékenységazonosító fejlécet a CORS-proxyt telepíteni szeretné. Ha még nem rendelkezik, először [telepítse a Node.js-](https://nodejs.org/en/download/). Majd adja ki a következő parancsot a parancssorba:

    npm install -g cors-proxy-server

Ezután módosítsa a Bing keresést a következőben szereplő végpontnál: a HTML-fájlt:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végezetül indítsa el a CORS-proxy a következő paranccsal:

    cors-proxy-server

A parancssori ablakban nyitva hagyja az oktatóanyag alkalmazás; használatakor az ablak bezárása leállítja a proxy. A bővíthető HTTP-fejlécek című szakaszt a keresési eredmények között, a most már megtekintheti a `X-MSEdge-ClientID` fejléc (többek között), és győződjön meg arról, hogy minden kérelem esetén.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Bing videó keresési API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)