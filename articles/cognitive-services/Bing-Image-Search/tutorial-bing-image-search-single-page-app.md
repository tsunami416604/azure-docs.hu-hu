---
title: Bing kép keresési egyoldalas webalkalmazást |} Microsoft Docs
description: Bemutatja, hogyan használja a kép Bing keresési API egy egyoldalas webalkalmazást.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349018"
---
# <a name="tutorial-single-page-web-app"></a>Oktatóanyag: Egyoldalas webalkalmazást

A kép Bing keresési API lehetővé teszi a weben és lemezkép eredményeket a keresési lekérdezés kapcsolódik. Az oktatóanyag azt, hogy a Bing kép keresési API-t használja a keresési eredmények megtekintése céljából egyoldalas-webalkalmazás létrehozása az oldalon jobbra. Az alkalmazás HTML, CSS és JavaScript összetevőket tartalmazza.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> A JSON és a HTTP-fejléceket, a lap alján a JSON-válasz és a HTTP-kérelem információk kattintáskor felfedése. Ezek az adatok akkor hasznos, ha a szolgáltatás fel.

Az oktatóanyag alkalmazás bemutatja, hogyan:

> [!div class="checklist"]
> * Végezze el a Bing kép keresési API-hívás JavaScript
> * Keresési beállítások átadása a kép Bing keresési API
> * Keresési eredmények megjelenítése
> * A keresési eredmények lap
> * A Bing ügyfél azonosítója és API előfizetés kulcs kezelése
> * Előforduló hibák kezelésének

Az oktatóprogram lap nincs teljes mértékben; bármely külső keretrendszerek, stíluslapok vagy képfájlok még nem használ. Csak széles körben támogatott JavaScript nyelvi funkciókat használ, és minden nagyobb webböngésző az aktuális verzióival működik.

Ebben az oktatóanyagban arról lesz szó a forráskód csak a kijelölt részei. A teljes forráskód nem érhető el [külön oldalon](tutorial-bing-image-search-single-page-app-source.md). Másolja és illessze be a kódot egy szövegszerkesztőbe, és mentse a fájt `bing.html`.

> [!NOTE]
> Ez az oktatóanyag alapvetően hasonlít, a a [egyoldalas Bing webes keresés app oktatóanyag](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), kép keresési eredmények csak foglalkozik, de.

## <a name="app-components"></a>Alkalmazások összetevői

Bármely egyoldalas webalkalmazást, mint az oktatóanyag alkalmazás három rész tartalmazza:

> [!div class="checklist"]
> * HTML - struktúra és a lap tartalma határozza meg
> * CSS - határozza meg az oldal megjelenítési módja
> * JavaScript - meghatározza az oldal viselkedését

Ez az oktatóanyag nem fedi le a HTML- vagy CSS többségét, részletesen, mivel ezek egyszerű.

A HTML-KÓDBAN a keresés, amelyben a felhasználó beírja egy lekérdezést, majd keresési beállítások tartalmazza. Az űrlap csatlakozik-e a JavaScript, amely végrehajtja a Keresés a `<form>` címke `onsubmit` attribútum:

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

A `onsubmit` kezelő azt `false`, amely az űrlap tartja a kiszolgáló nem továbbíthatók. A JavaScript-kód ténylegesen végzi a munka a szükséges információk begyűjtése az űrlap és a keresés végrehajtása.

A HTML is tartalmaz, a részlegek (HTML `<div>` címkék) hol jelenjenek meg a keresési eredmények között.

## <a name="managing-subscription-key"></a>Előfizetés kulcs kezelése

Kívánja kerülni a Bing keresési API-előfizetés kulcs szerepeljenek a kódot, használjuk a böngésző állandó tároló tárolja a kulcsot. Ha nem kulcs, azt meg kell adni a felhasználói kulcs, és későbbi használatra tárolja. Ha a kulcs később elutasította az API-t, azt érvénytelenné válnak a tárolt kulcs így a felhasználó újra.

Meghatároztuk `storeValue` és `retrieveValue` függvények, amelyek használja a `localStorage` objektum (Ha a böngésző támogatja azt), vagy egy cookie-t. A `getSubscriptionKey()` függvény használja ezeket a funkciókat tárolásához és lekéréséhez a felhasználói kulcsot.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
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

A HTML `<form>` címke `onsubmit` hívások a `bingWebSearch` függvény által visszaadott a keresési eredmények között. `bingWebSearch` használja a `getSubscriptionKey` minden egyes lekérdezés hitelesítéséhez. Ahogy az az előző definíció `getSubscriptionKey` bekéri a felhasználótól a kulcsot, ha a kulcs nem lett megadva. A kulcsot majd tárolja a folyamatos használata az alkalmazás.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása

![[A Bing kép keresési űrlap]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

A HTML-űrlaphoz, az alábbi funkciókat tartalmazza:

| | |
|-|-|
|`where`|A legördülő menüből a kereséshez használt piaci (a hely és a nyelvi) kiválasztásához.|
|`query`|A szövegmezőbe írja be a keresőkifejezést használandó.|
|`aspect`|A tényleges képek arányát kiválasztásának választógombokkal: nagyjából szögletes, számos, vagy magasságát.|
|`color`|Szín vagy színes, vagy egy elsődleges választja ki.
|`when`|A Keresés a legutóbbi naponta, hetente vagy havonta opcionálisan korlátozó legördülő menü.|
|`safe`|A jelölőnégyzet, amely azt jelzi, hogy Bing tartozó biztonságos keresés funkció segítségével "felnőtt" eredményeket szűrheti.|
|`count`|Rejtett mező. Keresési eredmények vissza az egyes kérelmek száma. Módosítsa a laponként kevesebb vagy további eredmények megtekintése céljából.|
|`offset`|Rejtett mező. A kérelem; az első keresési eredmény eltolása a lapozáshoz használt. Lesz visszaállítva `0` az új kérelemhez.|
|`nextoffset`|Rejtett mező. A keresési eredmény fogadását követően a mező értékének van beállítva a `nextOffset` a válaszban. Ez a mező használatával elkerülhető az egymást átfedő eredmények egymást követő lapokon.|
|`stack`|Rejtett mező. JSON-kódolású az eltolások listáját az előző keresési eredményeit, lépjen vissza az előző lapokra oldalát.|

> [!NOTE]
> Bing kép keresési kínál számos további lekérdezési paramétereket. Itt használunk csak néhány őket.

A JavaScript függvény `bingSearchOptions()` alakítja át ezeket a mezőket a Bing keresési API által megkívánt formátumban részleges lekérdezési karakterláncként.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Például lehet a biztonságos Keresés funkciót `strict`, `moderate`, vagy `off`, a `moderate` az alapértelmezett alatt. De az űrlap használ a jelölőnégyzet, amelynek csak kétállapotú. A JavaScript-kód alakítja át, ezt a beállítást az egyik `strict` vagy `off` (nem vesszük `moderate`).

## <a name="performing-the-request"></a>A kérelem végrehajtása

A lekérdezés, a beállítások karakterlánc és az API-kulcsot a `BingImageSearch` függvény egy `XMLHttpRequest` objektum gombra a kérelem a Bing kép keresési végpontnak.

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
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
    request.addEventListener("load", handleBingResponse);
    
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

A HTTP-kérés sikeres befejezését követően a JavaScript-hívásokat az `load` eseménykezelő, a `handleBingResponse()` függvény kezelni egy sikeres HTTP GET kérést az API-t. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> A sikeres HTTP-kérelmek does *nem* feltétlenül jelenti azt, hogy a keresési szolgáltatás sikeresen befejeződött. Ha hiba lép fel a keresési művelet, a lemezkép Bing keresési API nem 200-as HTTP - állapotkódot adja vissza, és tartalmazza a hiba adatait a JSON-NÁ válaszul. Emellett a kérelmező sebessége korlátozott, ha a az API-t egy üres választ ad vissza.

Nagy részét a kódot is az előző funkciók hibakezelés van kijelölve. Hibák fordulhatnak elő a következő szakaszokban:

|Fázis|A lehetséges hibák|Kezeli|
|-|-|-|
|Épület JavaScript request objektumon|Érvénytelen URL|`try`/`catch` letiltása|
|A kérést|Hálózati hiba, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|A keresés végrehajtása|Érvénytelen kérelemben érvénytelen JSON sebességhatárok|a tesztek `load` eseménykezelő|

Hibák hívásával kezeli `renderErrorMessage()` a hibával kapcsolatos ismert bármely adatokkal. Ha a válasz a teljes gauntlet hiba teszten megfelelt, nevezzük `renderSearchResults()` a keresési eredmények megjelennek a lapon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése

A keresési eredmények megjelenítése a fő funkciója `renderSearchResults()`. Ez a funkció a JSON a Bing kép keresési szolgáltatás által visszaadott vesz igénybe, és ez a beállítás a képeket és a kapcsolódó keres, ha van ilyen.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

A fő rendszerkép találatok vissza, a legfelső szintű `value` a JSON-NÁ válaszul objektum. Azt adja meg azokat a függvény `renderImageResults()`, amely telepítéseket őket, és egy külön függvényt HTML egyes elemek megjelenítéséhez. Az eredményül kapott HTML küld vissza `renderSearchResults()`, ahol azt bekerülnek a `results` osztás a lapon.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

A kép Bing keresési API legfeljebb négy különböző típusú kapcsolódó saját legfelső szintű objektum eredményeket ad vissza. Ezek a következők:

|||
|-|-|
|`pivotSuggestions`|Lecseréli az eredeti search pivot szót egy másik lekérdezések. Például ha a "red virág", lehet, hogy a pivot szót "red", és előfordulhat, hogy a pivot javaslat "sárga virág."|
|`queryExpansions`|Azok a lekérdezések, amelyek az eredeti keresés szűkítéséhez további feltételek hozzáadásával. Ha a "Microsoft Surface", a lekérdezés bővítése lehet például "Microsoft Surface Pro."|
|`relatedSearches`|Az eredeti keresési megadott más felhasználók is megadott lekérdezések. Például ha a "Mount Rudolf", a kapcsolódó keresés lehet "Mt. Saint Helens."|
|`similarTerms`|A hasonló jelentése van az eredeti keresési lekérdezések. Ha a "kismacskák", egy hasonló kifejezés lehet például "írni."|

A korábban már látott `renderSearchResults()`, azt csak leképezési a `relatedItems` vonatkozó javaslatokat és a hely a kapott a lap nézetállapotán oldalsávon hivatkozásokat tartalmaz.

## <a name="rendering-result-items"></a>Eredmény elemek megjelenítése

A JavaScript code egy objektum `searchItemRenderers`, tartalmazó *jelentéselemeket:* függvény alapján, amelyek HTML minden milyen keresési eredmények.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

A megjelenítő függvény előfordulhat, hogy fogadja el a következő paraméterekkel:

| | |
|-|-|
|`item`|A JavaScript objektumra, amely a elem tulajdonságai, például az URL-CÍMÉT és leírását.|
|`index`|Az index, az eredmény elem a gyűjteményben.|
|`count`|A keresési eredmény cikk gyűjtemény elemeinek száma.|

A `index` és `count` paraméterek képes használni a el a szám, speciális HTML-KÓDBAN a elején vagy végén egy gyűjteményhez, a sortörések beszúrása elemek, bizonyos számú után, és így tovább. Ha egy leképező nem kell ezt a funkciót, azt nem kell a két paraméterek elfogadásához.

A következőkben részletes bemutatása a `images` leképező:

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

A kép megjelenítő funkciót:

> [!div class="checklist"]
> * Kiszámítja a miniatűr képméret (szélesség változik, legalább 120 képpont, amíg magasság van rögzítve 90 képpont).
> * A HTML buildek `<img>` címkén belül, hogy a kép miniatűr megjelenítéséhez. 
> * A HTML buildek `<a>` címkéket, amelyek a lemezkép és az azt tartalmazó lap.
> * A leírást, információkat jelenít meg a lemezkép és a helyet, mert nem hoz létre.

Teszteljük a `index` változó beszúrni egy `<p>` címke előtt az első kép eredményt. A miniatűrök egyéb tompa találkoznak egymást és sortörés következik a böngészőablakban igény szerint.

A miniatűrök méretének is szerepel a `<img>` címke és a `h` és `w` mezők a Miniatűr URL-címben. A [Bing miniatűr szolgáltatás](resize-and-crop-thumbnails.md) kézbesíti a miniatűr nézete pontosan ez a méret.

## <a name="persisting-client-id"></a>Persisting ügyfél-azonosító

A Bing keresési API-k válaszainak tartalmazhat egy `X-MSEdge-ClientID` fejlécet tartalmazta, amely vissza az API-t egymást követő kéréseket kell küldeni. Ha több Bing keresési API-k vannak használatban, az azonos ügyfél-Azonosítót kell használható azokat, ha lehetséges.

Így a `X-MSEdge-ClientID` fejléc lehetővé teszi, hogy a Bing API-k az összes felhasználó keresések, amely két fontos előnnyel jár.

Először lehetővé teszi a Bing keresőmotor keresések található eredmények, amelyek jobban megfeleljenek a felhasználói környezet túli alkalmazása. Ha a felhasználó a korábban keresést kihajózás kapcsolódó feltételek, például egy újabb keresse meg a "csomó" Előfordulhat, hogy lehetőleg vonatkozó adatokat ad vissza csomó kihajózás használt.

Második a Bing véletlenszerűen jelölje ki felhasználók számára, hogy mielőtt széles körben elérhető új szolgáltatások. Így az azonos ügyfél-Azonosítót minden egyes kérelemmel biztosítja, hogy a felhasználók, tekintse meg a szolgáltatás mindig választotta látja. Az ügyfél-azonosító nélkül a felhasználó jelenhet meg a szolgáltatás megjelennek és eltűnnek, látszólag véletlenszerű, a keresési eredmények között.

Böngésző biztonsági házirendek (CORS) előfordulhat, hogy a `X-MSEdge-ClientID` rendelkezésre állása, a JavaScript fejléc. Ez a korlátozás következik be, amikor a keresési válasz van egy eltérő eredetű az oldalról, amely azt kéri. Éles környezetben eleget kell tennie ezt a házirendet, amelyet a weblap megegyező tartományban lévő API-hívás kiszolgálóoldali parancsfájl üzemeltetnie. Mivel a parancsfájlt a weblapként azonos forrásból a `X-MSEdge-ClientID` fejléc JavaScript számára elérhető lesz.

> [!NOTE]
> Webes alkalmazás éles végre kell hajtania a kérelem kiszolgálóoldali ennek ellenére is. Ellenkező esetben a Bing keresési API-kulcsot kell szerepelnie a weblap, amelyen érhető el számára, akik megtekinti az adatforrás. Az API előfizetés kulcs, még akkor is, kérelmeire jogosulatlan felek, ezért fontos, hogy a kulcs alatt minden használatának számlázása.

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
> [Bing kép keresési API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

