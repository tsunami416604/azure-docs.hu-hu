---
title: 'Oktatóanyag: A Bing Spell Check API használatával helyesírás ellenőrizze eredmények beolvasása'
titlesuffix: Azure Cognitive Services
description: Bemutatja a Bing Spell Check használatát.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: tutorial
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: 4bb558c05a2bade3a9e3dd01976d4cd9bf24cb98
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160763"
---
# <a name="tutorial-build-a-web-page-spell-check-client"></a>Oktatóanyag: A weblap helyesírás-ellenőrzés ügyfél létrehozása

Ebben az oktatóanyagban létrehozunk egy weboldalt, amely lehetővé teszi a felhasználók számára a Bing Spell Check API lekérdezését.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Egyszerű lekérdezés intézése a Bing Spell Check API-hoz
> - Lekérdezési eredmények megjelenítése

## <a name="prerequisites"></a>Előfeltételek

Annak érdekében, hogy követni tudja az oktatóanyagot, szüksége lesz egy előfizetői azonosítóra a Bing Spell Check API-hoz. Ha nem rendelkezik ilyennel, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

## <a name="create-a-new-web-page"></a>Új weboldal létrehozása

Nyisson meg egy szövegszerkesztőt. Hozzon létre egy új fájlt, például spellcheck.html néven.

## <a name="add-html-header"></a>HTML-fejléc hozzáadása

Adja hozzá a HTML-fejléc adatait, majd kezdje el a szkriptszakaszt az alábbiak szerint.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8"> 
    <title>Bing Spell Check</title>

<style type="text/css">
    html, body, div, p, h1, h2 {font-family: Verdana, "Lucida Sans", sans-serif;}

    html, body, div, p  {font-weight: normal;}
    h1, h2 {font-weight: bold;}
    sup {font-weight: normal;}

    html, body, div, p  {font-size: 12px;}
    h1 {font-size: 20px;}
    h2 {font-size: 16px;}
    h1, h2 {clear: left;}

    img#logo {float: right;
</style>

<script type="text/javascript">
```

## <a name="getsubscriptionkey-function"></a>getSubscriptionKey függvény

A getSubscriptionKey függvény a Bing Spell Check API kulcsát adja vissza. A függvény a kulcsot a helyi tárolóból (vagyis egy cookie-ból) kéri le, vagy szükség esetén a felhasználótól kéri el.

Kezdje el a getSubscriptionKey függvényt, és deklarálja a cookie nevét az alábbiak szerint.

```html
getSubscriptionKey = function() {

    var COOKIE = "bing-spell-check-api-key";   // name used to store API key in key/value storage
```

A findCookie segítő függvény a megadott cookie értékét adja vissza, ha pedig a cookie nem található, akkor egy üres sztringet.

```html
    function findCookie(name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) {
                return keyvalue[1];
            }
        }
        return "";
        }
```

A getSubscriptionKey segítő függvény elkéri a felhasználótól a Bing Spell Check API kulcsának értékét, és visszaadja a kulcs értékét.

```html
    function getSubscriptionKeyCookie() {
        var key = findCookie(COOKIE);
        while (key.length !== 32) {
            key = prompt("Enter Bing Spell Check API subscription key:", "").trim();
            var expiry = new Date();
            expiry.setFullYear(expiry.getFullYear() + 2);
            document.cookie = COOKIE + "=" + key.trim() + "; expires=" + expiry.toUTCString();
        }
        return key;
    }
```

A getSubscriptionKeyLocalStorage segítő függvény először megpróbálja lekérni a Bing Spell Check API kulcsát a megfelelő cookie megkeresésével. Ha a cookie nem található, elkéri a felhasználótól a kulcs értékét. Ezután visszaadja a kulcs értékét.

```html
    function getSubscriptionKeyLocalStorage() {
        var key = localStorage.getItem(COOKIE) || "";
        while (key.length !== 32)
            key = prompt("Enter Bing Spell Check API subscription key:", "").trim();
        localStorage.setItem(COOKIE, key)
        return key;
    }
```

A getSubscriptionKey segítő függvényhez egy paraméter, az **invalidate** szükséges. Ha az **invalidate** értéke **true**, a getSubscriptionKey törli a Bing Spell Check API kulcsát tartalmazó cookie-t. Ha az **invalidate** értéke **false**, a getSubscriptionKey visszaadja a Bing Spell Check API kulcsának értékét.

```html
    function getSubscriptionKey(invalidate) {
        if (invalidate) {
            try {
                localStorage.removeItem(COOKIE);
            } catch (e) {
                document.cookie = COOKIE + "=";
            }
        } else {
            try {
                return getSubscriptionKeyLocalStorage();
            } catch (e) {
                return getSubscriptionKeyCookie();
            }
        }
    }
```

Adja vissza a getSubscriptionKey segítő függvényt a külső getSubscriptionKey függvény eredményeként. Zárja be a külső getSubscriptionKey függvény definícióját.

```html
    return getSubscriptionKey;

}();
```

## <a name="helper-functions"></a>Segítő függvények

A pre segítő függvény visszaadja a [pre](https://www.w3schools.com/tags/tag_pre.asp) HTML-címke segítségével előre formázott, megadott szöveget.

```html
function pre(text) {
    return "<pre>" + text.replace(/&/g, "&amp;").replace(/</g, "&lt;") + "</pre>"
}
```

A renderSearchResults függvény a Bing Spell Check API megadott eredményeit jeleníti meg a JSON rendezett formátumának használatával.

```html
function renderSearchResults(results) {
    document.getElementById("results").innerHTML = pre(JSON.stringify(results, null, 2));
}
```

A renderErrorMessage függvény a megadott hibaüzenetet és a hibakódot jeleníti meg.

```html
function renderErrorMessage(message, code) {
    if (code)
        document.getElementById("results").innerHTML = "<pre>Status " + code + ": " + message + "</pre>";
    else
        document.getElementById("results").innerHTML = "<pre>" + message + "</pre>";
}
```

## <a name="bingspellcheck-function"></a>bingSpellCheck függvény

A bingSpellCheck függvény van meghívva minden alkalommal, amikor a felhasználó szöveget ad meg a HTML-űrlap mezőjében.
Ehhez két paraméterre van szüksége: a HTML-űrlap mezőjének tartalmára és a Bing Spell Check API kulcsára.

```html
function bingSpellCheck(query, key) {
```

Adja meg a Bing Spell Check API-végpontot, és deklaráljon egy XMLHttpRequest objektumot, amelyet arra fog használni, hogy kéréseket küldjön a végpontnak.

```html
    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    var request = new XMLHttpRequest();

    try {
        request.open("GET", endpoint + "?mode=proof&mkt=en-US&text=" + encodeURIComponent(query));
    }
    catch (e) {
        renderErrorMessage("Bad request");
        return false;
    }
```

Állítsa az **Ocp-Apim-Subscription-Key** fejlécet a Bing Spell Check API kulcsának értékére.

```html
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
```

Kezelje a végpont válaszát. Ha az állapot 200 (OK), jelenítse meg az eredményeket, egyéb esetben pedig a hibára vonatkozó információkat jelenítse meg.

```html
    request.addEventListener("load", function() {
        if (this.status === 200) {
            renderSearchResults(JSON.parse(this.responseText));
        }
        else {
            if (this.status === 401) getSubscriptionKey(true);
            renderErrorMessage(this.statusText, this.status);
        }
    });
```

Kezelje az XMLHttpRequest objektum lehetséges hibaeseményeit is.

```html
    request.addEventListener("error", function() {
        renderErrorMessage("Network error");
    });

    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });
```

Küldje el a kérést. Zárja be a bingSpellCheck függvényt, a **script** címkét és a **head** címkét.

```html
    request.send();
    return false;
}
// --></script>

</head>
```

## <a name="html-body"></a>HTML-törzs

A weboldal betöltésekor győződjön meg róla, hogy rendelkezik a Bing Spell Check API kulcsával, és szükség esetén kérje el a felhasználótól.

```html
<body onload="document.forms.bing.query.focus(); getSubscriptionKey();">
```

Jelenítse meg a Bing emblémát.

```html
<img id="logo" align=base src="data:image/png;base64,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">
```

Hozzon létre egy szövegmezővel rendelkező HTML-űrlapot. Kezelje az **onsubmit** eseményt, és hívja meg a bingSpellCheck függvényt, átadva a szövegmező tartalmát és a Bing Spell Check API kulcsát.

```html
<form name="bing" onsubmit="return bingSpellCheck(this.query.value, getSubscriptionKey())">
    <h2>Spell Check</h2>
    <input type="text" name="query" size="80" placeholder="Spell Check" autocomplete=off>
</form>
```

Adja hozzá a HTML **div** címkét, amelyet az eredmények megjelenítéséhez használ. A korábban definiált JavaScript erre a **div** címkére hivatkozik.

```html
<h2>Results</h2>
<div id="results">
<p>None yet.</p>

</div>

</body>
</html>
```

Mentse a fájlt.

## <a name="display-results"></a>Eredmények megjelenítése

Nyissa meg a weboldalt a böngészőben. Ha a rendszer kéri, adja meg a Bing Spell Check API előfizetői azonosítóját. Adjon meg egy lekérdezést (például a „Hollo, wrld!” kifejezést) a **Spell Check** szövegmezőben, majd nyomja le az **ENTER** billentyűt. A weboldal ezután megjeleníti a lekérdezési eredményeket.

```json
{
  "_type": "SpellCheck",
  "flaggedTokens": [
    {
      "offset": 0,
      "token": "Hollo",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "Hello",
          "score": 0.856629936217145
        },
        {
          "suggestion": "Hollow",
          "score": 0.816717853225633
        }
      ]
    },
    {
      "offset": 7,
      "token": "wlrd",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "world",
          "score": 0.856629936217145
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati és megjelenítési követelmények](../UseAndDisplayRequirements.md)
