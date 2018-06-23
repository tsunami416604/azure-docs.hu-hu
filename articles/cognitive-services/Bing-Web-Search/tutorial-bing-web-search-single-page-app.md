---
title: Bing webes keresés egyoldalas webalkalmazást |} Microsoft Docs
description: Bemutatja, hogyan használja a webes Bing keresési API egy egyoldalas webalkalmazást.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349019"
---
# <a name="tutorial-single-page-web-app"></a>Oktatóanyag: Egyoldalas webalkalmazást

A webes Bing keresési API lehetővé teszi a weben, és szerezze be a különböző típusú megfelelő, egy lekérdezés eredményét. Az oktatóanyag azt, hogy a Bing webes keresési API-t használja a keresési eredmények megtekintése céljából egyoldalas-webalkalmazás létrehozása az oldalon jobbra. Az alkalmazás HTML, CSS és JavaScript összetevőket tartalmazza.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> A JSON és a HTTP-fejléceket, a lap alján a JSON-válasz és a HTTP-kérelem információk kattintáskor felfedése. Ezek az adatok akkor hasznos, ha a szolgáltatás fel.

Az oktatóanyag alkalmazás bemutatja, hogyan:

> [!div class="checklist"]
> * Végezze el a Bing webes keresési API-hívás JavaScript
> * Keresési beállítások át a webes Bing keresési API
> * Webes, a hírek, a lemezkép és a videó keresési eredmények megjelenítése
> * A keresési eredmények lap
> * A Bing ügyfél azonosítója és API előfizetés kulcs kezelése
> * Előforduló hibák kezelésének

Az oktatóprogram lap nincs teljes mértékben; bármely külső keretrendszerek, stíluslapok vagy képfájlok még nem használ. Csak széles körben támogatott JavaScript nyelvi funkciókat használ, és minden nagyobb webböngésző az aktuális verzióival működik.

Ebben az oktatóanyagban arról lesz szó a forráskód csak a kijelölt részei. A teljes forráskód nem érhető el [külön oldalon](tutorial-bing-web-search-single-page-app-source.md). Másolja és illessze be a kódot egy szövegszerkesztőbe, és mentse a fájt `bing.html`.

## <a name="app-components"></a>Alkalmazások összetevői

Bármely egyoldalas webalkalmazást, mint az oktatóanyag alkalmazás három rész tartalmazza:

> [!div class="checklist"]
> * HTML - struktúra és a lap tartalma határozza meg
> * CSS - határozza meg az oldal megjelenítési módja
> * JavaScript - meghatározza az oldal viselkedését

Ez az oktatóanyag nem fedi le a HTML- vagy CSS többségét, részletesen, mivel ezek egyszerű.

A HTML-KÓDBAN a keresés, amelyben a felhasználó beírja egy lekérdezést, majd keresési beállítások tartalmazza. Az űrlap csatlakozik-e a JavaScript, amely végrehajtja a Keresés a `<form>` címke `onsubmit` attribútum:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

A `onsubmit` kezelő azt `false`, amely az űrlap tartja a kiszolgáló nem továbbíthatók. A JavaScript-kód ténylegesen végzi a munka a szükséges információk begyűjtése az űrlap és a keresés végrehajtása.

A HTML is tartalmaz, a részlegek (HTML `<div>` címkék) hol jelenjenek meg a keresési eredmények között.

## <a name="managing-subscription-key"></a>Előfizetés kulcs kezelése

Kívánja kerülni a Bing keresési API-előfizetés kulcs szerepeljenek a kódot, használjuk a böngésző állandó tároló tárolja a kulcsot. Ha nem kulcs, azt meg kell adni a felhasználói kulcs, és későbbi használatra tárolja. Ha a kulcs később elutasította az API-t, azt érvénytelenné válnak a tárolt kulcs, a program ismét kéri a felhasználó.

Meghatároztuk `storeValue` és `retrieveValue` függvények, amelyek használja a `localStorage` objektum (Ha a böngésző támogatja azt), vagy egy cookie-t. A `getSubscriptionKey()` függvény használja ezeket a funkciókat tárolásához és lekéréséhez a felhasználói kulcsot.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

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

A HTML `form` címke `onsubmit` hívások a `bingWebSearch` függvény által visszaadott a keresési eredmények között. `bingWebSearch` használja a `getSubscriptionKey` minden egyes lekérdezés hitelesítéséhez. Ahogy az az előző definíció `getSubscriptionKey` bekéri a felhasználótól a kulcsot, ha a kulcs nem lett megadva. A kulcsot majd tárolja a folyamatos használata az alkalmazás.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása

![[A Bing webes keresés]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

A HTML-űrlaphoz, a következő nevű elemeket tartalmazza:

| | |
|-|-|
| `where` | A legördülő menüből a kereséshez használt piaci (a hely és a nyelvi) kiválasztásához. |
| `query` | A szövegmezőbe írja be a keresőkifejezést használandó. |
| `what` | Adott típusú eredmények elősegítő jelölőnégyzeteket. Például lemezképet, előléptetni növeli a képek prioritása. |
| `when` | A Keresés a legutóbbi naponta, hetente vagy havonta opcionálisan korlátozó legördülő menü. |
| `safe` | A jelölőnégyzet, amely azt jelzi, hogy Bing tartozó biztonságos keresés funkció segítségével "felnőtt" eredményeket szűrheti. |
| `count` | Rejtett mező. Keresési eredmények vissza az egyes kérelmek száma. Módosítsa a laponként kevesebb vagy további eredmények megtekintése céljából. |
| `offset` | Rejtett mező. A kérelem; az első keresési eredmény eltolása a lapozáshoz használt. Lesz visszaállítva `0` az új kérelemhez. |

> [!NOTE]
> Bing webes keresés kínál számos további lekérdezési paramétereket. Itt használunk csak néhány őket.

A JavaScript függvény `bingSearchOptions()` alakítja át ezeket a mezőket a Bing keresési API által megkívánt formátumban.

```javascript
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
        options.push("promote=" + what.join(","));
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

Ha bármelyik a **előléptetés** vannak megjelölve az jelölőnégyzeteket, azt is hozzáadhat egy `answerCount` a lekérdezési paramétert. `answerCount` használata esetén szükséges a `promote` paraméter. A Microsoft egyszerűen állítsa `9` (a webes a Bing keresési API által támogatott eredménytípusai száma) Győződjön meg arról kapunk eredményt típusú maximális száma.

> [!NOTE]
> Előléptetni egy eredménytípus nem *garantálni* , hogy a keresési eredmények tartalmazza-e az adott típusú eredményt. Ahelyett, hogy az előléptetés növeli az ilyen típusú eredmények a szokásos rangsorolási relatív prioritása. Adott típusú eredmények kereséseket korlátozásához használja a `responseFilter` lekérdezésparaméter, vagy hívja a pontosabb végpont például a Bing kép keresés vagy Bing hírek.

Is küldünk `textDecoration` és `textFormat` lekérdezési paramétert okozhat a kívánt keresőkifejezést a kell félkövérrel a keresési eredmények között. Ezek az értékek szoftveresen kötött a parancsfájlban.

## <a name="performing-the-request"></a>A kérelem végrehajtása

A lekérdezés, a beállítások karakterlánc és az API-kulcsot a `BingWebSearch` függvény egy `XMLHttpRequest` küldhető a vonatkozó kérelem a keresést a Bing-végpont objektum.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

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
        return;
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
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
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
> A sikeres HTTP-kérelmek does *nem* feltétlenül jelenti azt, hogy a keresési szolgáltatás sikeresen befejeződött. Ha hiba lép fel a keresési művelet, webes a Bing keresési API nem 200-as HTTP - állapotkódot adja vissza, és tartalmazza a hiba adatait a JSON-NÁ válaszul. Emellett a kérelmező sebessége korlátozott, ha a az API-t egy üres választ ad vissza.

Nagy részét a kódot is az előző funkciók hibakezelés van kijelölve. Hibák fordulhatnak elő a következő szakaszokban:

|Fázis|A lehetséges hibák|Kezeli|
|-|-|-|
|Épület JavaScript request objektumon|Érvénytelen URL|`try`/`catch` letiltása|
|A kérést|Hálózati hiba, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|A keresés végrehajtása|Érvénytelen kérelemben érvénytelen JSON sebességhatárok|a tesztek `load` eseménykezelő|

Hibák hívásával kezeli `renderErrorMessage()` a hibával kapcsolatos ismert bármely adatokkal. Ha a válasz a teljes gauntlet hiba teszten megfelelt, nevezzük `renderSearchResults()` a keresési eredmények megjelennek a lapon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése

A webes Bing keresési API [eredmények megjelenítése a megadott sorrendben kell](useanddisplayrequirements.md). Az API-t a válaszok különböző típusú térhetnek vissza, mert nincs elég a legfelső szintű iterációt `WebPages` a JSON-NÁ válaszul gyűjtemény és az eredmények megjelenítéséhez. (Ha azt szeretné, hogy az eredmények csak egyféle, használja a `responseFilter` lekérdezési paramétert vagy egy másik Bing keresési végpont.)

Ehelyett használjuk a `rankingResponse` a keresési eredmények megjelenítése az eredmények rendezéséhez. Ez az objektum elemekre hivatkozik a `WebPages` `News`, `Images`, és/vagy `Videos` gyűjteményeket, vagy egyéb legfelső szintű válasz gyűjteményében a JSON-választ.

`rankingResponse` a keresési eredmények kijelölt legfeljebb három gyűjtemények tartalmazhat `pole`, `mainline`, és `sidebar`. 

`pole`, ha telepítve van, a legfontosabb keresési eredményt, és észrevehető üzenetnek kell megjelennie. `mainline` a keresési eredmények tömeges hivatkozik. Mainline eredmények üzenetnek kell megjelennie után azonnal `pole` (vagy első, ha `pole` nincs jelen). 

Végül. `sidebar` kiegészítő keresési eredmények hivatkozik. Gyakran ezekkel az eredményekkel történik a kapcsolódó keresések vagy képeket. Ha lehetséges ezekkel az eredményekkel kell megjelennie a tényleges oldalsávon. Képernyő korlátok adja meg a oldalsávon praktikus (például a mobil eszköz), ha azok üzenetnek kell megjelennie a `mainline` eredmények.

Minden eleme egy `rankingResponse` gyűjtemény két különböző, de egyenértékű, módon hivatkozik a tényleges keresési eredmény tételszámának.

| | |
|-|-|
|`id`|A `id` URL-címet a következőképpen néz, de a hivatkozások nem használható. A `id` rangsorolási eredményt megegyezik a `id` vagy keresési eredmény található cikk egy válasz gyűjteményt *vagy* egy teljes válasz gyűjteményt (például `Images`).
|`answerType`, `resultIndex`|A `answerType` hivatkozik a legfelső szintű válasz gyűjteményt, amely tartalmazza az eredmény (például `WebPages`). A `resultIndex` hivatkozik, az eredmény index adott gyűjteményen belül. Ha `resultIndex` van megadva, a rangsorolási eredménye hivatkozik a teljes gyűjteményt.

> [!NOTE]
> Ez a kijelző, a keresési válasz további információkért lásd: [dimenziószáma eredmények](rank-results.md).

Bármelyik módszert a hivatkozott keresési eredmény elem keresése legkényelmesebben, az alkalmazás használhat. Az oktatóanyag kód használjuk a `answerType` és `resultIndex` egyes eredmények kereséséhez.

Végül tekintse meg a függvény az idő `renderSearchResults()`. Ez a funkció elemein végiglépkedve a három `rankingResponse` gyűjteményeket, amelyek megfelelnek a három szakasz a keresési eredmények. Minden szakaszhoz nevezzük `renderResultsItems()` az adott eredmények megjelenítéséhez.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` viszont elemein végiglépkedve mindegyik elemek `rankingResponse` szakaszban, a keresési eredmény használatával minden rangsorolási eredmény leképezve a `answerType` és `resultIndex` mezők, és a megfelelő megjelenítési függvényt, az eredmény HTML létrehozásához. 

Ha `resultIndex` nincs megadva az adott rangsorolási elem `renderResultsItems()` elemein végiglépkedve minden eredmény az adott típusú és az egyes elemekhez tartozó megjelenítési függvény. 

Mindkét módszer esetén az eredményül kapott HTML bekerülnek a megfelelő `<div>` elemet a lapon.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Eredmény elemek megjelenítése

A JavaScript code egy objektum `searchItemRenderers`, tartalmazó *jelentéselemeket:* függvény alapján, amelyek HTML minden milyen keresési eredmények.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> Az oktatóanyag alkalmazás jelentéselemeket weblapok, híreket, képek, videók és kapcsolódó keresések rendelkezik. Saját alkalmazás jelentéselemeket akkor fordulhat elő, eredményeket, számítások, javaslat, entitásokat, időzónák és meghatározások tartalmazhatnak bármilyen típusú szüksége van.

A Megjelenítés függvények csak fogadja el a `item` paraméter: a JavaScript-objektum, amely egyetlen keresési eredmény jelöl. Mások alkalmas további paraméterek, amely eltérően a különböző környezetek elemek megjelenítéséhez használható. (Ezt az információt nem használó leképező nem kell a paraméterek elfogadásához.)

A környezet argumentumai a következők:

| | |
|-|-|
|`section`|Az eredmények szakaszban (`pole`, `mainline`, vagy `sidebar`) elem megjelenik a.
|`index`<br>`count`|Érhető el, ha a `rankingResponse` elem beállítással megadhatja, hogy egy adott gyűjtemény minden eredmény megjelenik; `undefined` más módon. Ezek a paraméterek az adatgyűjtési és -elemek összesített száma belül elem indexe kapni a gyűjteményben. Ezt az információt number az eredményeket, az első vagy utolsó eredmény különböző HTML készítése, és így tovább is.|

Az oktatóanyag alkalmazásban mind a `images` és `relatedSearches` jelentéselemeket a környezet argumentumokat használni ahhoz, hogy a HTML-KÓDBAN akkor generál. A következőkben részletes bemutatása a `images` leképező:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

A kép megjelenítő funkciót:

> [!div class="checklist"]
> * Kiszámítja a miniatűr képméret (width változik, amíg magasság rögzített, 60 képpont).
> * Szúrja be a HTML-KÓDBAN, amely megelőzi a kép eredmény attól függően, hogy a környezetben.
> * A HTML buildek `<a>` a képet tartalmazó lapra mutató kódelem.
> * A HTML buildek `<img>` címkén belül, hogy a kép miniatűr megjelenítéséhez. 

A kép leképező használja a `section` és `index` változók másképp attól függően, hogy hol szerepelnek eredmények megtekintése céljából. Sortörés (`<br>` címke) között kép eredmények az oldalsávon egészül ki, hogy az oldalsávon képek oszlop jeleníti meg. A más szakaszok, az első kép eredménye `(index === 0)` előtt egy `<p>` címke. A miniatűrök egyéb tompa találkoznak egymást és sortörés következik a böngészőablakban igény szerint.

A miniatűrök méretének is szerepel a `<img>` címke és a `h` és `w` mezők a Miniatűr URL-címben. A [Bing miniatűr szolgáltatás](resize-and-crop-thumbnails.md) kézbesíti a miniatűr nézete pontosan ez a méret. A `title` és `alt` attribútumok (szöveges leírása a kép) össze a lemezkép operációs rendszerének nevét és az állomásnevet, az URL-címben.

Lemezképek módon jelenik meg itt a mainline keresési eredmények között.

![[A Bing kép eredmények]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>Persisting ügyfél-azonosító

A Bing keresési API-k válaszainak tartalmazhat egy `X-MSEdge-ClientID` fejlécet tartalmazta, amely vissza az API-t egymást követő kéréseket kell küldeni. Ha több Bing keresési API-k vannak használatban, az azonos ügyfél-Azonosítót kell használható azokat, ha lehetséges.

Így a `X-MSEdge-ClientID` fejléc lehetővé teszi, hogy a Bing API-k az összes felhasználó keresések, amely két fontos előnnyel jár.

Először lehetővé teszi a Bing keresőmotor keresések található eredmények, amelyek jobban megfeleljenek a felhasználói környezet túli alkalmazása. Ha a felhasználó a korábban keresést kihajózás kapcsolódó feltételek, például egy újabb keresse meg a "csomó" Előfordulhat, hogy lehetőleg vonatkozó adatokat ad vissza csomó kihajózás használt.

Második a Bing véletlenszerűen jelölje ki felhasználók számára, hogy mielőtt széles körben elérhető új szolgáltatások. Így az azonos ügyfél-Azonosítót minden egyes kérelemmel biztosítja, hogy a felhasználók, akik egy szolgáltatást lát mindig választotta látja. Az ügyfél-azonosító nélkül a felhasználó jelenhet meg a szolgáltatás megjelennek és eltűnnek, látszólag véletlenszerű, a keresési eredmények között.

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
> [Visual keresési mobilalkalmazás oktatóanyag](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Bing webes keresési API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
