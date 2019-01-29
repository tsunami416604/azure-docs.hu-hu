---
title: 'Oktatóanyag: A Bing Video Search egyoldalas alkalmazás készítése'
titlesuffix: Azure Cognitive Services
description: Azt ismerteti, hogyan használható a Bing Video Search API egy egyoldalas webalkalmazásban.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 11/01/2017
ms.author: rosh
ms.openlocfilehash: 5661cab24d3c5ada401f416a3d39b1a787094b5c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175111"
---
# <a name="tutorial-single-page-video-search-app"></a>Oktatóanyag: Videókeresési egyoldalas alkalmazás
A Bing Video Search API kikeresi az interneten a keresési lekérdezésnek megfelelő videótalálatokat. Ebben az oktatóanyagban létrehozunk egy egyoldalas webalkalmazást, amely a Bing Search API-t használja a keresési eredmények megjelenítéséhez az oldalon. Az alkalmazás HTML-, CSS- és JavaScript-összetevőkből áll.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> A lap alján található JSON- és HTTP-fejlécek a JSON-válasz és a HTTP-kérés adatait mutatják, ha rájuk kattint. Ezek a részletek hasznosak lehetnek, ha meg szeretne ismerkedni a szolgáltatással.

![nyers JSON- és HTTP-eredmények](./media/json-http-raw-results.png)

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:
> [!div class="checklist"]
> * Bing Video Search API-hívás indítása a JavaScriptben
> * Keresési beállítások továbbítása a Bing Search API-nak
> * Videótalálatok megjelenítése vagy a megjelenítés kiterjesztése weblapokra, hírekre vagy képekre
> * Az elmúlt 24 órára, az elmúlt hétre vagy elmúlt hónapra korlátozódó, vagy minden elérhető időtartományt magában foglaló időkeret
> * Keresési eredmények közötti lapozás
> * A Bing-ügyfélazonosító és az API előfizetői azonosító kezelése
> * Az esetlegesen előforduló problémák kezelése

Az oktatóanyag oldala teljesen önálló; nem használ semmilyen külső keretrendszert, stíluslapot vagy képfájlt. Egyedül széles körben támogatott JavaScript nyelvi funkciókat használ, és az összes nagyobb webböngésző aktuális verziójával kompatibilis.

Ebben az oktatóanyagban a forráskódnak csak egyes részeit fogjuk megtárgyalni. A teljes [forráskód itt érhető el](tutorial-bing-video-search-single-page-app-source.md). A példa futtatásához másolja a forráskódot egy szövegszerkesztőbe, és mentse el `bing.html` néven.

## <a name="app-components"></a>Alkalmazás-összetevők
Mint minden egyoldalas webalkalmazás, ez az oktatóalkalmazás is három részből áll:

> [!div class="checklist"]
> * HTML – Meghatározza az oldal szerkezetét és tartalmát
> * CSS – Meghatározza az oldal megjelenését
> * JavaScript – Meghatározza az oldal viselkedését

A HTML és a CSS nagy része hagyományos, ezért az oktatóanyag részeként nem vesszük át őket. A HTML tartalmazza a keresési űrlapot, amelyen a felhasználó megad egy lekérdezést, és kiválasztja a keresési beállításokat. Az űrlap a JavaScripthez van csatlakoztatva, amely végrehajtja a keresést a `<form>` címke `onsubmit` attribútumával:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
Az `onsubmit` kezelő `false` értéket ad vissza, ami megakadályozza az űrlap elküldését a kiszolgálóra. A JavaScript-kód begyűjti a szükséges adatokat az űrlapból, és végrehajtja a keresést.

A HTML azokat a részlegeket (HTML `<div>` címkéket) is tartalmazza, amelyekben a keresési eredmények megjelennek.

## <a name="managing-subscription-key"></a>Előfizetői azonosító kezelése

Annak érdekében, hogy a Bing Search API előfizetői azonosítóját ki lehessen hagyni a kódból, a böngésző állandó tárolójában tároljuk az azonosítót. Mielőtt az azonosítót eltárolnánk, elkérjük a felhasználó azonosítóját. Ha az azonosítót később elutasítja az API, érvénytelenítjük a tárolt azonosítót, ezért a felhasználótól újra el kell kérnünk az övét.

Meghatározzuk a `storeValue` és `retrieveValue` függvényeket, amelyek vagy a `localStorage` objektumot használják (amelyet nem minden böngésző támogat), vagy egy cookie-t. A `getSubscriptionKey()` függvény a felhasználó azonosítójának tárolására és lekérésére használja ezeket a függvényeket.

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
Egy HTML `<form>` címke, az `onsubmit` meghívja a `bingWebSearch` függvényt, hogy visszaadja a keresési eredményeket. A `bingWebSearch` minden lekérdezés hitelesítéséhez a `getSubscriptionKey()` függvényt használja. Ahogy az előző definíció is mutatja, a `getSubscriptionKey` elkéri a felhasználótól az azonosítót, ha az még nem lett megadva. A rendszer ezután eltárolja az azonosítót az alkalmazás által további használatra.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása
A következő ábrán a lekérdezési szövegmező és a keresést meghatározó beállítások láthatók.

![A Bing News Search beállításai](media/video-search-options.png)

A HTML-űrlap a következő nevű elemeket tartalmazza:

|Elem|Leírás|
|-|-|
| `where` | Legördülő menü a kereséshez használt piac (hely és nyelv) kiválasztásához. |
| `query` | Szövegmező a keresőkifejezések megadásához. |
| `modules` | Jelölőnégyzetek az eredmények bizonyos moduljainak, minden eredménynek vagy a kapcsolódó videóknak az előléptetéséhez. |
| `when` | Legördülő menü a keresés opcionális, az elmúlt napra, hétre vagy hónapra történő korlátozásához. |
| `safe` | Jelölőnégyzet, amellyel megadható, hogy a Bing SafeSearch funkció kiszűrje-e a „felnőtteknek szánt” tartalmakat. |
| `count` | Rejtett mező. A kérésenként visszaadandó keresési eredmények száma. A módosításával oldalanként kevesebb vagy több keresési eredmény jeleníthető meg. |
| `offset`|  Rejtett mező. Az első keresési eredmény eltolása a kérésben; a lapozást segíti elő. Új kérésnél `0` értékre van visszaállítva. |

> [!NOTE]
> A Bing Web Search más lekérdezési paramétereket is kínál, de mi ezek közül csak néhányat használunk.

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

A `SafeSearch` paraméter értéke például egy tényleges API-hívásban `strict`, `moderate` vagy `off` lehet. Ezek közül a `moderate` az alapértelmezett. A mi űrlapunk viszont egy jelölőnégyzetet használ, amelynek csak két állapota van. A JavaScript-kód ezt a beállítást `strict` vagy `off` értékké alakítja (a `moderate` nem használatos).

## <a name="performing-the-request"></a>A kérés végrehajtása
A lekérdezés, a beállítási sztring és az API-kulcs ismeretében a `BingWebSearch` függvény egy `XMLHttpRequest` objektumot használ arra, hogy kérést intézzen a Bing Search-végponthoz.

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
A HTTP-kérés sikeres befejezése esetén a JavaScript meghívja a `load` `handleOnLoad()` eseménykezelőjét, hogy kezeljen egy, az API-hoz intézett sikeres HTTP GET kérést. 

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
> Ha a keresési műveletben hiba merül fel, a Bing News Search API visszaad egy nem 200-as értékű HTTP-állapotkódot, és belefoglalja a hibára vonatkozó információkat a JSON-válaszba. Továbbá, ha a kérés sebessége korlátozva volt, az API egy üres választ ad vissza.
Egy sikeres HTTP-kérés *nem* feltétlenül jelenti azt, hogy maga a keresés is sikeres volt. 

Az előző két függvény kódjainak nagy része a hibakezelésért felel. A következő fázisoknál léphetnek fel hibák:

|Fázis|Lehetséges hiba vagy hibák|Kezelő|
|-|-|-|
|A JavaScript-kérésobjektum létrehozása|Érvénytelen URL-cím|`try`/`catch` blokk|
|Kérés végrehajtása|Hálózati hibák, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|Keresés végrehajtása|Érvénytelen kérés, érvénytelen JSON, sebességkorlátok|tesztek a `load` eseménykezelőben|

A hibák kezelése a `renderErrorMessage()` meghívásával történik a hibával kapcsolatos ismert részletek megadásával. Ha a válasz hibatesztek teljes skáláját továbbítja, meghívjuk a `renderSearchResults()` függvényt, hogy megjelenítse a keresési eredményeket az oldalon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése
A keresési eredmények megjelenítésére használatos fő függvény a `renderSearchResults()`. Ez a függvény a Bing News Search szolgáltatás által visszaadott JSON segítségével rendereli a hírtalálatokat és a kapcsolódó kereséseket, ha vannak.

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
A JSON-válasz a keresési eredményeket a legfelső szintű `value` objektumként adja vissza. Mi elküldjük őket a `renderResultsItems()` függvénynek, amely végighalad rajtuk, és meghív egy függvényt az egyes elemek HTML-be történő rendereléséhez. A kapott HTML vissza lesz adva a `renderSearchResults()` függvénynek, amelyben a lap `results` részlegébe lesz beszúrva.

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

A Bing News Search API legfeljebb négy különböző típusú kapcsolódó eredményt ad vissza, mindegyiket a saját legfelső szintű objektumában. Ezek a következők:

|Kapcsolat típusa|Leírás|
|-|-|
|`pivotSuggestions`|Lekérdezések, amelyek az eredeti keresés egyik lecserélhető szavát egy másikra cserélik. Ha például a „piros virágok” kifejezésre keres, a „piros” egy lecserélhető szó, a „sárga virágok” pedig egy alternatív javaslat.|
|`queryExpansions`|Lekérdezések, amelyek további kifejezések hozzáadásával szűkítik az eredeti keresést. Ha például a „Microsoft Surface” kifejezésre keres, a lekérdezés egyik lehetséges kibővítése a „Microsoft Surface Pro”.|
|`relatedSearches`|Az eredeti keresőkifejezéssel is próbálkozó felhasználók további lekérdezései. Ha például a „Mount Rainier” kifejezésre keres, egy lehetséges kapcsolódó keresés lehet a „Mt. Saint Helens”.|
|`similarTerms`|Lekérdezések, amelyeknek az eredeti kereséshez hasonló jelentésük van. Ha például az „iskolák” kifejezésre keres, az „oktatás” az egyik ilyen hasonló értelmű keresőszó.|

Ahogy a `renderSearchResults()` függvényben láthatta, csak a `relatedItems` javaslatokat rendereljük, és a kapott hivatkozásokat a lap oldalsávjában helyezzük el.

## <a name="rendering-result-items"></a>Eredményelemek renderelése

A JavaScript-kódban a `searchItemRenderers` objektum *leképezőket* is tartalmazhat, olyan függvényeket, amelyek minden típusú keresési eredményhez létrehoznak egy HTML-t. A videós találatok oldala csak a `videos` elemet használja. A leképezők különféle típusait más oktatóanyagok ismertetik.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Egy leképező függvény a következő paramétereket fogadja el:

|Paraméter|Leírás|
|-|-|
|`item`| A JavaScript-objektum, amely az elem tulajdonságait tartalmazza, például az URL-címét és a leírását.|
|`index`| Az eredményelem indexe a saját gyűjteményén belül.|
|`count`| Az eredményelem gyűjteményében található elemek száma.|

Az `index` és `count` paraméterek használhatók a találatok megszámozására, egy gyűjtemény elején vagy végén egy speciális HTML létrehozására, egy bizonyos számú elem utáni sortörés beszúrására és így tovább. Ha egy leképezőnek nincs szüksége erre a funkcióra, akkor nem kell elfogadnia ezt a két paramétert.

A következő JavaScript-kivonatban a `video` leképező látható. A Videók végpont használatakor minden találat `Videos` típusú lesz. A következő kódszakaszban a `searchItemRenderers` leképezők láthatók.

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

A leképező függvény:
> [!div class="checklist"]
> * Létrehoz egy bekezdéscímkét, hozzárendeli az `images` osztályhoz, és leküldéssel továbbítja a HTML-tömbnek.
> * Kiszámítja a képminiatűr méretét (a szélesség 60 képpontban rögzített, a magasságot ezzel arányosan számítja ki).
> * Létrehozza az `<img>` HTML-címkét a képminiatűr megjelenítéséhez. 
> * Létrehozza az `<a>` HTML-címkéket, amelyek a képre és a képet tartalmazó oldalra hivatkoznak.
> * Létrehozza a leírást, amely információkat jelenít meg a képről és a képet tartalmazó oldalról.

A miniatűr méretét az `<img>` címke, illetve a miniatűr URL-címének `h` és `w` mezője is használja. A [Bing miniatűr-szolgáltatása](resize-and-crop-thumbnails.md) ezután egy pontosan ekkora méretű miniatűrt állít elő.

## <a name="persisting-client-id"></a>Ügyfél-azonosító megőrzése
A Bing Search API-k válaszai tartalmazhatnak egy `X-MSEdge-ClientID` fejlécet, amelyet egymást követő kérésekkel vissza kell küldeni az API-nak. Ha több Bing Search API-t is használ, mindegyikhez ugyanazt az ügyfél-azonosítót használja, ha lehetséges.

Az `X-MSEdge-ClientID` fejléc megadása lehetővé teszi, hogy a Bing API-k egymáshoz társítsák a felhasználó összes keresését, amelynek két fontos előnye van.

Egyrészt lehetővé teszi, hogy a Bing keresőmotorja korábbi kontextusokat is alkalmazzon a keresésekhez olyan találatok megjelenítése érdekében, amelyek jobban megfelelnek a felhasználó igényeinek. Ha például a felhasználó korábban vitorlázáshoz kapcsolódó kifejezésekre keresett rá, egy későbbi keresés a „csomó” kifejezésre nagy valószínűséggel a vitorlázásban használt csomókkal kapcsolatos információkat fog eredményezni.

Másrészt a Bing véletlenszerűen kiválaszthat felhasználókat, hogy új funkciókat próbálhassanak ki, mielőtt azok széles körben elérhetővé válnának. Ha minden kéréshez ugyanazt az ügyfél-azonosítót adja meg, akkor azok a felhasználók, akiknek megjelenik a funkció, mindig látni fogják. Az ügyfél-azonosító nélkül a felhasználó azt tapasztalhatja, hogy egy funkció látszólag véletlenszerűen hol megjelenik, hol eltűnik a keresési eredményeknél.

A böngészők biztonsági szabályzatai (CORS) megakadályozhatják, hogy a JavaScript hozzáférjen az `X-MSEdge-ClientID` fejléchez. Ez a korlátozás akkor léphet életbe, ha a keresési válasz eredete különbözik az azt lekérő oldalétól. Éles környezetben egy olyan kiszolgálóoldali szkript futtatásával oldhatja fel a szabályzat okozta korlátozást, amely a weboldaléval megegyező tartományból hívja meg az API-t. Mivel a szkript eredete megegyezik a weboldaléval, az `X-MSEdge-ClientID` fejléc elérhető lesz a JavaScript számára.

> [!NOTE]
> Éles webalkalmazásban kiszolgálói oldalról hajtsa végre a kérést. Ellenkező esetben a weboldalnak tartalmaznia kell a Bing Search API-kulcsot, ahol a forrást megtekintők is hozzáférhetnek. Az API előfizetési kulcsával történő összes használatért Ön fizet, még az illetéktelen felek által létrehozott kérésekért is, ezért fontos, hogy a kulcsot ne tegye elérhetővé.

Fejlesztési célokból a Bing Web Search API-kérést egy CORS-proxyn keresztül is végrehajthatja. Az ilyen proxyk válasza rendelkezik egy `Access-Control-Expose-Headers` fejléccel, amely engedélyezési listára teszi a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

CORS-proxyt könnyedén telepíthet annak érdekében, hogy oktatóalkalmazásunk hozzáférhessen az ügyfél-azonosító fejlécéhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután hajtsa végre egy parancsablakban a következő parancsot:

    npm install -g cors-proxy-server

Következő lépésként írja át a Bing Web Search-végpontot a HTML-fájlban a következőre:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Bing Video Search API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)
