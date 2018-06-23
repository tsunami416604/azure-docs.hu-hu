---
title: Bing entitás keresési egyoldalas webalkalmazást |} Microsoft Docs
description: Bemutatja, hogyan használja az entitás Bing keresési API egy egyoldalas webalkalmazást.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/08/2017
ms.author: v-jerkin
ms.openlocfilehash: 91c60913cd806baf100e5511cbf59299bf9a84f0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349231"
---
# <a name="tutorial-single-page-web-app"></a>Oktatóanyag: Egyoldalas webalkalmazást

Az entitás Bing keresési API lehetővé teszi, hogy kapcsolatos információ a webes keresés *entitások* és *helyezi.* Típusú eredményt, vagy mindkettőt egy adott lekérdezésre kérheti. Helyek és a szervezetek a definíciók alább.

|||
|-|-|
|Entitások|Az ismert személyek, helyek és a Keresés név alapján, amit|
|Helyek|Éttermekben, a szállodák és más helyi vállalatok számára, hogy a Keresés név alapján *vagy* típusa (olasz éttermekben) szerint|

Az oktatóanyag azt, hogy a Bing entitás keresési API-t használja a keresési eredmények megtekintése céljából egyoldalas-webalkalmazás létrehozása az oldalon jobbra. Az alkalmazás HTML, CSS és JavaScript összetevőket tartalmazza.

Az API lehetővé teszi a hely szerint eredmények rangsorolására. A mobilalkalmazásban kérje meg az eszköz a saját helyéhez. A webes alkalmazás, használhatja a `getPosition()` függvény. De a hívás csak biztonságos környezetben működik, és nem is biztosít a pontos helyet. Is a felhasználó is szeretne keresni entitások nem a saját közelében.

Az alkalmazás ezért felszólítja a Bing Maps szolgáltatás szélességi és hosszúsági beszerzése egy felhasználó által megadott helyről. A felhasználó megadhatja egy jellegzetes ("terület tű") vagy egy teljes vagy részleges címet ("New York City") nevét, és a Bing térképek API biztosítja a koordináták százalékosan.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> A JSON és a HTTP-fejléceket, a lap alján a JSON-válasz és a HTTP-kérelem információk kattintáskor felfedése. Ezek az adatok akkor hasznos, ha a szolgáltatás fel.

Az oktatóanyag alkalmazás bemutatja, hogyan:

> [!div class="checklist"]
> * Végezze el a Bing entitás keresési API-hívás JavaScript
> * Hajtsa végre a Bing Maps `locationQuery` a JavaScript API-hívás
> * Keresési beállítások átadása az API-hívások
> * Keresési eredmények megjelenítése
> * A Bing ügyfél azonosítója és API előfizetés kulcsok kezelése
> * Bármilyen felmerülő hibák kezelése

Az oktatóprogram lap nincs teljes mértékben; bármely külső keretrendszerek, stíluslapok vagy képfájlok még nem használ. Csak széles körben támogatott JavaScript nyelvi funkciókat használ, és minden nagyobb webböngésző az aktuális verzióival működik.

Ebben az oktatóanyagban arról lesz szó a forráskód csak a kijelölt részei. A teljes forráskód nem érhető el [külön oldalon](tutorial-bing-entities-search-single-page-app-source.md). Másolja és illessze be a kódot egy szövegszerkesztőbe, és mentse a fájt `bing.html`.

> [!NOTE]
> Oktatóprogram alapvetően hasonlít, a a [egyoldalas Bing webes keresés app oktatóanyag](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), de csak az entitás keresési eredmények foglalkozik.

## <a name="app-components"></a>Alkalmazások összetevői

Bármely egyoldalas webalkalmazást, mint az oktatóanyag alkalmazás három rész tartalmazza:

> [!div class="checklist"]
> * HTML - struktúra és a lap tartalma határozza meg
> * CSS - határozza meg az oldal megjelenítési módja
> * JavaScript - meghatározza az oldal viselkedését

Ez az oktatóanyag nem fedi le a HTML- vagy CSS többségét, részletesen, mivel ezek egyszerű.

A HTML-KÓDBAN a keresés, amelyben a felhasználó beírja egy lekérdezést, majd keresési beállítások tartalmazza. Az űrlap csatlakozik-e a JavaScript, amely végrehajtja a Keresés a `<form>` címke `onsubmit` attribútum:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

A `onsubmit` kezelő azt `false`, amely az űrlap tartja a kiszolgáló nem továbbíthatók. A JavaScript-kód ténylegesen végzi a munka a szükséges információk begyűjtése az űrlap és a keresés végrehajtása.

A keresés két fázisban történik. Először Ha a felhasználó megadott helyre korlátozás, a Bing Maps lekérdezés történik koordináták alakítja. Ehhez a lekérdezéshez a visszahívás majd másolattól a Bing entitás keresési lekérdezés.

A HTML is tartalmaz, a részlegek (HTML `<div>` címkék) hol jelenjenek meg a keresési eredmények között.

## <a name="managing-subscription-keys"></a>Előfizetés kulcsok kezelése

> [!NOTE]
> Az alkalmazás használatához előfizetés kulcsok a Bing keresési API-t és a Bing térképek API-t. Használhatja a [Próbakulcs Bing keresési](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) és egy [alapvető a Bing Maps kulcs](https://www.microsoft.com/maps/create-a-bing-maps-key).

Ne kelljen felvenni a Bing keresési és a Bing térképek API előfizetés kulcsok a kódot, hogy használjuk a böngésző állandó tároló tárolja őket. Vagy a kulcs nem tárolt, ha azt az kérni, és későbbi használatra tárolja. Ha a kulcs később elutasította az API-t, azt érvénytelenné válnak a tárolt kulcs, a felhasználónak kapcsolatba azt követően a következő keresési.

Meghatároztuk `storeValue` és `retrieveValue` függvények, amelyek használja a `localStorage` objektum (Ha a böngésző támogatja azt), vagy egy cookie-t. A `getSubscriptionKey()` függvény használja ezeket a funkciókat tárolásához és lekéréséhez a felhasználói kulcsot.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "http://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

A HTML `<body>` címke tartalmaz egy `onload` attribútum, amely behívja `getSearchSubscriptionKey()` és `getMapsSubscriptionKey()` Ha a lap betöltődik. Az adott hívások osztja ki az azonnal kérni a felhasználót, a kulcsok, ha azok nem még adott őket.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása

![[A Bing entitás keresési űrlap]](media/entity-search-spa-form.png)

A HTML-űrlaphoz, az alábbi funkciókat tartalmazza:

| | |
|-|-|
|`where`|A legördülő menüből a kereséshez használt piaci (a hely és a nyelvi) kiválasztásához.|
|`query`|A szövegmezőbe írja be a keresőkifejezést használandó.|
|`safe`|A jelölőnégyzet, amely azt jelzi, hogy biztonságos keresés be van kapcsolva ("felnőtt" keresési eredmények korlátozása)|
|`what`|Keresse meg az entitások vagy helyek a menü.|
|`mapquery`|A szövegmező, amelyben a felhasználó megadhat egy teljes vagy részleges cím, egy jellegzetes, a Bing entitás visszatérési több megfelelő találatok segítségével stb.|

> [!NOTE]
> Helyek eredményei jelenleg csak az Egyesült Államokban érhető el. A `where` és `what` menün kellene végiglépkednie tartozik: Ez a korlátozás érvényesítését. Választhat egy nem amerikai piaci Forráshelyek kiválasztása a a `what` menü `what` semmit változik. Ha egy nem amerikai piaci kiválasztása a helyek választja a `where` menü `where` vált, az USA.

A JavaScript függvény `bingSearchOptions()` alakítja ezeket a mezőket egy részleges lekérdezési karakterlánc a Bing keresési API-hoz.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Például lehet a biztonságos Keresés funkciót `strict`, `moderate`, vagy `off`, a `moderate` az alapértelmezett alatt. De az űrlap használ a jelölőnégyzet, amelynek csak kétállapotú. A JavaScript-kód alakítja át, ezt a beállítást az egyik `strict` vagy `off` (nem vesszük `moderate`).

A `mapquery` mező a nem kezelt `bingSearchOptions()` mert nem a Bing entitás keresés a Bing Maps hely lekérdezés használja.

## <a name="obtaining-a-location"></a>Egy hely beszerzése

A Bing térképek API kínál a [ `locationQuery` metódus](//msdn.microsoft.com/library/ff701711.aspx), amelyek használatával a szélességi található, és a földrajzi hosszúság értéke annak a helynek a felhasználó megadja. E koordináták majd továbbítódnak a Bing entitás keresési API-t a felhasználó kérésére. A keresési eredmények rangsorolhatja entitásokat és a helyek, amely megközelíti a megadott helyen.

Nem lehet hozzáférni a Bing térképek API használatával egy beállítás `XMLHttpRequest` lekérdezni a webalkalmazásban, mert a szolgáltatás nem támogatja az eltérő eredetű lekérdezések. Szerencsére támogatja a JSONP (a "P" kell "hosszúságra"). A rendszer a JSONP választ, egy szokásos JSON-válasz függvényhívás burkolva. A kérelem által beszúrása egy `<script>` címke a dokumentumhoz. (A parancsfájlok betöltése nincs böngésző biztonsági házirend.)

A `bingMapsLocate()` függvény hoz létre, és szúrja be a `<script>` címke a lekérdezéshez. A `jsonp=bingMapsCallback` szegmens a lekérdezési karakterláncot a függvény hívása a válasz nevét adja meg.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Ha a Bing térképek API nem válaszol, a `bingMapsCallBack()` függvény soha nem hívja. Általában ez azt jelenti, hogy `bingEntitySearch()` nem nevezik, és az entitás keresési eredmények nem jelennek meg. Ebben az esetben elkerülése érdekében `bingMapsLocate()` is beállít egy időzítőt, amely hívja `bingEntitySearch()` öt másodperc múlva. Az entitás keresés végrehajtása kétszer elkerülése érdekében a visszahívási függvény nincs logikát.

Ha a lekérdezés befejeződött, a `bingMapsCallback()` függvény hívása esetén, mert a kért.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Szélességi és hosszúsági, valamint a Bing entitás keresési lekérdezés igényli egy *radius* azt jelzi, hogy a Tartózkodásihely-adatok pontosságát. A radius használatával kiszámításához azt a *határolókeret* a Bing Maps válaszban megadott. A határolókeret téglalap, amely körbeveszi a teljes helyre. Ha a felhasználó megadja például `NYC`, az eredmény New York Város és egy határolókeret, amely magában foglalja a város nagyjából központi koordinátáit tartalmazza. 

Azt először ki kell számítani az egyes funkcióval határolókeret négy pontjának elsődleges koordinátáit távolságra `haversineDistance()` (nincs ábrázolva). A radius a legnagyobb ezek négy távolság használják azt. A minimális radius kilométer. Ezt az értéket is használja a alapértelmezés szerint, ha nincs határolókeret áll rendelkezésre a válaszban.

A koordináták és a RADIUS-nyertek, majd nevezzük `bingEntitySearch()` a tényleges keresés végrehajtásához.

## <a name="performing-the-search"></a>A keresés végrehajtása

A lekérdezés, illetve egy helyet, beállítások karakterláncra és az API-kulcsot adott a `BingEntitySearch()` függvény a Bing entitás keresési kérést.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> A sikeres HTTP-kérelmek does *nem* feltétlenül jelenti azt, hogy a keresési szolgáltatás sikeresen befejeződött. Ha hiba lép fel a keresési művelet, entitás a Bing keresési API nem 200-as HTTP - állapotkódot adja vissza, és tartalmazza a hiba adatait a JSON-NÁ válaszul. Emellett a kérelmező sebessége korlátozott, ha a az API-t egy üres választ ad vissza.

Nagy részét a kódot is az előző funkciók hibakezelés van kijelölve. Hibák fordulhatnak elő a következő szakaszokban:

|Fázis|A lehetséges hibák|Kezeli|
|-|-|-|
|Épület JavaScript request objektumon|Érvénytelen URL|`try`/`catch` letiltása|
|A kérést|Hálózati hiba, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|A keresés végrehajtása|Érvénytelen kérelemben érvénytelen JSON sebességhatárok|a tesztek `load` eseménykezelő|

Hibák hívásával kezeli `renderErrorMessage()` a hibával kapcsolatos ismert bármely adatokkal. Ha a válasz a teljes gauntlet hiba teszten megfelelt, nevezzük `renderSearchResults()` a keresési eredmények megjelennek a lapon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése

Az entitás Bing keresési API [eredmények megjelenítése a megadott sorrendben kell](use-display-requirements.md). Az API-t válaszok két különböző típusú térhetnek vissza, mert nincs elég a legfelső szintű iterációt `Entities` vagy `Places` a JSON-NÁ válaszul gyűjtemény és az eredmények megjelenítéséhez. (Ha azt szeretné, hogy csak egy típusú eredményt, használja a `responseFilter` lekérdezésparaméter.)

Ehelyett használjuk a `rankingResponse` gyűjtemény a keresési eredmények megjelenítése az eredmények rendezéséhez. Ez az objektum elemekre hivatkozik a `Entitiess` és/vagy `Places` gyűjtemények.

`rankingResponse` a keresési eredmények kijelölt legfeljebb három gyűjtemények tartalmazhat `pole`, `mainline`, és `sidebar`. 

`pole`, ha telepítve van, a legfontosabb keresési eredményt, és észrevehető üzenetnek kell megjelennie. `mainline` a keresési eredmények tömeges hivatkozik. Mainline eredmények üzenetnek kell megjelennie után azonnal `pole` (vagy első, ha `pole` nincs jelen). 

Végül. `sidebar` kiegészítő keresési eredmények hivatkozik. Egy tényleges oldalsávon vagy egyszerűen a mainline eredmények után megjelenhet. Azt választotta az utóbbi az oktatóanyag alkalmazás.

Minden eleme egy `rankingResponse` gyűjtemény két különböző, de egyenértékű, módon hivatkozik a tényleges keresési eredmény tételszámának.

| | |
|-|-|
|`id`|A `id` URL-címet a következőképpen néz, de a hivatkozások nem használható. A `id` rangsorolási eredményt megegyezik a `id` vagy keresési eredmény található cikk egy válasz gyűjteményt *vagy* egy teljes válasz gyűjteményt (például `Entities`).
|`answerType`<br>`resultIndex`|A `answerType` hivatkozik a legfelső szintű válasz gyűjteményt, amely tartalmazza az eredmény (például `Entities`). A `resultIndex` hivatkozik, az eredmény index adott gyűjteményen belül. Ha `resultIndex` van megadva, a rangsorolási eredménye hivatkozik a teljes gyűjteményt.

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

    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Eredmény elemek megjelenítése

A JavaScript code egy objektum `searchItemRenderers`, tartalmazó *jelentéselemeket:* függvény alapján, amelyek HTML minden milyen keresési eredmények.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

A megjelenítő függvény előfordulhat, hogy fogadja el a következő paraméterekkel:

| | |
|-|-|
|`item`|A JavaScript objektumra, amely a elem tulajdonságai, például az URL-CÍMÉT és leírását.|
|`index`|Az index, az eredmény elem a gyűjteményben.|
|`count`|A keresési eredmény cikk gyűjtemény elemeinek száma.|

A `index` és `count` paraméterek képes használni a el a szám, speciális HTML-KÓDBAN a elején vagy végén egy gyűjteményhez, a sortörések beszúrása elemek, bizonyos számú után, és így tovább. Ha egy leképező nem kell ezt a funkciót, azt nem kell a két paraméterek elfogadásához. Tulajdonképpen azt nem használhatók a megjelenítő az oktatóanyag alkalmazásnak.

A következőkben részletes bemutatása a `entities` leképező:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Az entitás megjelenítő funkciót:

> [!div class="checklist"]
> * A HTML buildek `<img>` megjelenítéséhez a kép miniatűrjét, ha van ilyen címke. 
> * A HTML buildek `<a>` a képet tartalmazó lapra mutató kódelem.
> * A leírást, információkat jelenít meg a lemezkép és a helyet, mert nem hoz létre.
> * Magában foglalja az entitás besorolás a megjelenítési mutatók használatával, ha van ilyen.
> * Az entitás kapcsolatban további információkat a Bing keresési mutató hivatkozást tartalmaz.
> * Bármely adatforrások által igényelt licencelési vagy attribútumára információit jeleníti meg.

## <a name="persisting-client-id"></a>Persisting ügyfél-azonosító

A Bing keresési API-k válaszainak tartalmazhat egy `X-MSEdge-ClientID` fejlécet tartalmazta, amely vissza az API-t egymást követő kéréseket kell küldeni. Ha több Bing keresési API-k vannak használatban, az azonos ügyfél-Azonosítót kell használható azokat, ha lehetséges.

Így a `X-MSEdge-ClientID` fejléc lehetővé teszi, hogy a Bing API-k az összes felhasználó keresések, amely két fontos előnnyel jár.

Először lehetővé teszi a Bing keresőmotor keresések található eredmények, amelyek jobban megfeleljenek a felhasználói környezet túli alkalmazása. Ha a felhasználó korábban már rendelkezik keresett kihajózás kapcsolatos feltételeket, például "tárolók" későbbi keresése előfordulhat, hogy lehetőleg vonatkozó adatokat ad vissza egy VitorlásHajó rögzítéséhez helyek.

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
> [Bing entitás keresési API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [A Bing térképek API-JÁNAK dokumentációja](//msdn.microsoft.com/library/dd877180.aspx)
