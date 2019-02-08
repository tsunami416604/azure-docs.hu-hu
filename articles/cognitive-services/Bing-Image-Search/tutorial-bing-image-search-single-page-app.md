---
title: 'Oktatóanyag: Hozzon létre egy egyoldalas webalkalmazást – a Bing Image Search API'
titleSuffix: Azure cognitive services
description: A Bing Image Search API segítségével jó minőségű, releváns képeket kereshet a weben. Ezzel az oktatóanyaggal egyoldalas webalkalmazást készíthet, amely keresési lekérdezéseket küld az API-nak, és a weblapon belül jeleníti meg az eredményeket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 9/12/2018
ms.author: aahi
ms.openlocfilehash: 1c8e08cdf6238064e6ec2468a00bbc65ec46b675
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880749"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Oktatóanyag: Hozzon létre egy egyoldalas alkalmazást, a Bing Image Search API használatával

A Bing Image Search API segítségével jó minőségű, releváns képeket kereshet a weben. Ezzel az oktatóanyaggal egyoldalas webalkalmazást készíthet, amely keresési lekérdezéseket küld az API-nak, és a weblapon belül jeleníti meg az eredményeket. Ez az oktatóanyag hasonló a Bing Web Searchre vonatkozó [ugyanilyen típusú oktatóanyaghoz](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md).

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:

> [!div class="checklist"]
> * Bing Image Search API-hívás indítása a JavaScriptben
> * A keresési eredmények javítása keresési beállítások használatával
> * Keresési eredmények megjelenítése lapozással
> * API-előfizetési kulcs és Bing ügyfélazonosító lekérése és kezelése

Az oktatóanyaghoz tartozó teljes forráskód elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/) legújabb verziója.
* A Node.js-hez készült [Express.js](https://expressjs.com/) keretrendszer. A GitHub-minta információs fájl telepítési utasításokat a forráskódja számára érhetők el.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Felhasználó előfizetési kulcsainak kezelése és tárolása

Ez az alkalmazás webböngészők állandó tárolójában helyezi el az API-előfizetési kulcsokat. Ha nincs tárolt kulcs, akkor a weboldal a felhasználótól kéri a kulcs megadását, amelyet eltárol a későbbi használathoz. Ha az API később visszautasítja a kulcsot, az alkalmazás eltávolítja azt a tárolóból.


Definiálja a `storeValue` és a `retrieveValue` függvényeket, amelyek vagy a `localStorage` objektumot használják (amennyiben a böngésző ezt támogatja), vagy cookie-kat.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

A `getSubscriptionKey()` függvény a `retrieveValue` használatával kísérli meg egy korábban tárolt kulcs lekérését. Ha ilyen nem található, akkor a felhasználótól kéri a kulcs megadását, és a `storeValue` használatával tárolja azt.

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

Egy HTML `<form>` címke, az `onsubmit` meghívja a `bingWebSearch` függvényt, hogy visszaadja a keresési eredményeket. A `bingWebSearch` minden lekérdezés hitelesítéséhez a `getSubscriptionKey` függvényt használja. Ahogy az előző definíció is mutatja, a `getSubscriptionKey` elkéri a felhasználótól az azonosítót, ha az még nem lett megadva. A rendszer ezután eltárolja az azonosítót az alkalmazás által további használatra.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Keresési kérések küldése

Ez az alkalmazás egy HTML `<form>` használatával kezdeményezi a felhasználói keresési kérések küldését, az `onsubmit` attribútummal meghívva a `newBingImageSearch()` függvényt.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Az `onsubmit` kezelő alapértelmezésként `false` értéket ad vissza, ami megakadályozza az űrlap elküldését.

## <a name="select-search-options"></a>Keresési beállítások kiválasztása

![[Bing Image Search űrlap]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

A Bing Image Search API több [szűrőlekérdezési paramétert](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#filter-query-parameters) kínál a találatok szűkítéséhez és szűréséhez. Ebben az alkalmazásban a HTML-űrlap az alábbi paraméteres beállításokat használja és jeleníti meg:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Legördülő menü a kereséshez használt piac (hely és nyelv) kiválasztásához.                                                                                             |
| `query`      | Szövegmező a keresőkifejezések megadásához.                                                                                                                                 |
| `aspect`     | Választógombok a képtalálatok képarányának (nagyjából négyzetes, széles vagy magas) kiválasztásához.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Legördülő menü a keresés opcionális, az elmúlt napra, hétre vagy hónapra történő korlátozásához.                                                                                          |
| `safe`       | Jelölőnégyzet, amellyel megadható, hogy a Bing SafeSearch funkciója kiszűrje-e a „felnőtteknek szánt” tartalmakat.                                                                                      |
| `count`      | Rejtett mező. A kérésenként visszaadandó keresési eredmények száma. A módosításával oldalanként kevesebb vagy több keresési eredmény jeleníthető meg.                                                            |
| `offset`     | Rejtett mező. Az első keresési eredmény eltolása a kérésben; a lapozást segíti elő. Új kérésnél `0` értékre van visszaállítva.                                                           |
| `nextoffset` | Rejtett mező. Keresési eredmény fogadásakor ez a mező a válaszbeli `nextOffset` értékre van beállítva. Ennek a mezőnek a használatával akadályozható meg a találatok átfedése az egymást követő lapokon. |
| `stack`      | Rejtett mező. A keresési eredmények előző oldalainak eltolásait tároló, JSON-kódolású lista a korábbi oldalakra való visszalapozáshoz.                                                      |

A `bingSearchOptions()` függvény az alkalmazás API-kéréseiben használható részleges lekérdezési sztringgé alakítja ezeket a beállításokat.  

```javascript
// Build query options from the HTML form
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

## <a name="performing-the-request"></a>A kérés végrehajtása

A keresési lekérdezés, a beállítási sztring és az API-kulcs használatával a `BingImageSearch()` függvény egy XMLHttpRequest objektumot használ arra, hogy kérést intézzen a Bing Image Search-végponthoz.


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

A HTTP-kérés sikeres befejezése esetén a JavaScript a `handleBingResponse()` „betöltési” eseménykezelőt hívja meg a sikeres HTTP GET kérés kezelésére.

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
> Sikeres HTTP-kérések is tartalmazhatnak sikertelen keresési információt. Ha a keresési művelet során hiba merül fel, a Bing Image Search API egy nem 200-as értékű HTTP-állapotkódot és a hibára vonatkozó információkat ad vissza a JSON-válaszban. Továbbá, ha a kérés sebessége korlátozva volt, az API üres választ fog visszaadni.

## <a name="display-the-search-results"></a>A keresési eredmények megjelenítése

A keresési eredményeket a `renderSearchResults()` függvény jeleníti meg, amely a Bing Image Search szolgáltatás által visszaadott JSON birtokában meghívja a megfelelő renderelő függvényt a képtalálatokhoz és a kapcsolódó keresésekhez.

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

A képtalálatokat a JSON-válaszon belüli legfelső szintű `value` objektum tartalmazza. Ezek a `renderImageResults()` függvénynek lesznek átadva, amely a találatokat sorra véve minden elemet HTML-re konvertál.

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

A Bing Image Search API négyféle keresési javaslatot adhat vissza a felhasználó keresési élményének támogatására, mindegyiket a saját legfelső szintű objektumában:

| Javaslat         | Leírás                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Lekérdezések, amelyek az eredeti keresés egyik lecserélhető szavát egy másikra cserélik. Ha például a „piros virágok” kifejezésre keres, a „piros” egy lecserélhető szó, a „sárga virágok” pedig egy alternatív javaslat. |
| `queryExpansions`  | Lekérdezések, amelyek további kifejezések hozzáadásával szűkítik az eredeti keresést. Ha például a „Microsoft Surface” kifejezésre keres, a lekérdezés egyik lehetséges kibővítése a „Microsoft Surface Pro”.                                   |
| `relatedSearches`  | Az eredeti keresőkifejezéssel is próbálkozó felhasználók további lekérdezései. Ha például a „Mount Rainier” kifejezésre keres, egy lehetséges kapcsolódó keresés lehet a „Mt. Saint Helens”.                       |
| `similarTerms`     | Lekérdezések, amelyeknek az eredeti kereséshez hasonló jelentésük van. Ha például a „cicák” kifejezésre keres, a „cuki” az egyik ilyen hasonló értelmű keresőszó.                                                                   |

Az alkalmazás csak a `relatedItems` javaslatait rendereli, a kapott hivatkozásokat pedig a lap oldalsávjában helyezi el.

## <a name="rendering-search-results"></a>Keresési eredmények renderelése

Ebben az alkalmazásban a `searchItemRenderers` objektum renderelő függvényeket tartalmaz, amelyek minden típusú keresési eredményhez létrehoznak egy HTML-t.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Ezek a renderelő függvény a következő paramétereket fogadják el:

| Paraméter         | Leírás                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | A JavaScript-objektum, amely az elem tulajdonságait tartalmazza, például az URL-címét és a leírását. |
| `index` | Az eredményelem indexe a saját gyűjteményén belül.                                          |
| `count` | Az eredményelem gyűjteményében található elemek száma.                                  |

Az `index` és a `count` paraméter az eredmények számozására, gyűjtemények számára generált HTML-ekhez, és a tartalom rendszerezésére van felhasználva. Konkrétan:

* Kiszámítja a kép miniatűrjének méretét (a szélesség változó, de legalább 120 képpont, a magasság mindig 90).
* Létrehozza az `<img>` HTML-címkét a képminiatűr megjelenítéséhez.
* Létrehozza az `<a>` HTML-címkéket, amelyek a képre és a képet tartalmazó oldalra hivatkoznak.
* Létrehozza a leírást, amely információkat jelenít meg a képről és a képet tartalmazó oldalról.

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

A miniatűr `height` és `width` tulajdonságát az `<img>` címke, illetve a miniatűr URL-címének `h` és `w` mezője is használja. A Bing ezáltal pontosan ekkora méretű [miniatűrt](resize-and-crop-thumbnails.md) tud visszaadni.

## <a name="persisting-client-id"></a>Ügyfél-azonosító megőrzése

A Bing Search API-k válaszai tartalmazhatnak egy `X-MSEdge-ClientID` fejlécet, amelyet egymást követő kérésekkel vissza kell küldeni az API-nak. Ha több Bing Search API-t is használ, mindegyikhez ugyanazt az ügyfél-azonosítót használja, ha lehetséges.

Az `X-MSEdge-ClientID` fejléc megadása lehetővé teszi, hogy a Bing API-k egymáshoz társítsák a felhasználó összes keresését, ami több szempontból is hasznos.

Egyrészt lehetővé teszi, hogy a Bing keresőmotorja korábbi kontextusokat is alkalmazzon a keresésekhez olyan találatok megjelenítése érdekében, amelyek jobban megfelelnek a felhasználó igényeinek. Ha például a felhasználó korábban vitorlázáshoz kapcsolódó kifejezésekre keresett rá, egy későbbi keresés a „csomó” kifejezésre nagy valószínűséggel a vitorlázásban használt csomókkal kapcsolatos információkat fog eredményezni.

Másrészt a Bing véletlenszerűen kiválaszthat felhasználókat, hogy új funkciókat próbálhassanak ki, mielőtt azok széles körben elérhetővé válnának. Ha minden kéréshez ugyanaz az ügyfél-azonosító van megadva, akkor azok a felhasználók, akik ki lettek választva egy funkció használatára, mindig látják azt. Az ügyfél-azonosító nélkül a felhasználó azt tapasztalhatja, hogy egy funkció látszólag véletlenszerűen hol megjelenik, hol eltűnik a keresési eredményeknél.

A böngészők biztonsági szabályzatai (CORS) megakadályozhatják, hogy a JavaScript hozzáférjen az `X-MSEdge-ClientID` fejléchez. Ez a korlátozás akkor léphet életbe, ha a keresési válasz eredete különbözik az azt lekérő oldalétól. Éles környezetben egy olyan kiszolgálóoldali szkript futtatásával oldhatja fel a szabályzat okozta korlátozást, amely a weboldaléval megegyező tartományból hívja meg az API-t. Mivel a szkript eredete megegyezik a weboldaléval, az `X-MSEdge-ClientID` fejléc elérhető lesz a JavaScript számára.

> [!NOTE]
> Éles webalkalmazásban a kérést ettől függetlenül is kiszolgálói oldalról érdemes végrehajtani. Ellenkező esetben a weboldalnak tartalmaznia kell a Bing Search API-kulcsot, ahol a forrást megtekintők is hozzáférhetnek. Az API előfizetési kulcsával történő összes használatért Ön fizet, még az illetéktelen felek által létrehozott kérésekért is, ezért fontos, hogy a kulcsot ne tegye elérhetővé.

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
> [Képadatok kinyerése a Bing Image Search API használatával](tutorial-image-post.md)

## <a name="see-also"></a>Lásd még

* [Bing Image Search API – referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
