---
title: 'Oktatóanyag: rendszerkép feltöltése a Bing Visual Search API használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel egy képet a Bingbe, hogyan szerezhet be információkat, és megjelenítheti a választ.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: d5dc762499342f0004073fb6c240c98c4ac2bc09
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334565"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Oktatóanyag: lemezképek feltöltése a Bing Visual Search APIba

A Bing Visual Search API lehetővé teszi, hogy az Ön által feltöltött képekhez hasonló képeket keressen a weben. Ezzel az Oktatóanyaggal létrehozhat egy webalkalmazást, amely képes képet küldeni az API-nak, és megjeleníti a weblapon visszaadott elemzéseket. Vegye figyelembe, hogy ez az alkalmazás nem felel meg az API-k használatának összes [Bing-és megjelenítési követelményének](../bing-web-search/use-display-requirements.md) .

A minta teljes forráskódját a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html)további hibakezelés és jegyzetek is megtalálhatja.

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:

> [!div class="checklist"]
> * Rendszerkép feltöltése a Bing Visual Search APIba
> * Képkeresés eredményeinek megjelenítése egy webalkalmazásban
> * Ismerkedjen meg az API által nyújtott különböző információkkal

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>A weblap létrehozása és szerkezete

Hozzon létre egy HTML-oldalt, amely egy képet küld a Bing Visual Search APInak, beolvassa az elemzéseket, és megjeleníti őket. A kedvenc szerkesztőben vagy az IDE-ban hozzon létre egy "uploaddemo.html" nevű fájlt. Adja hozzá a következő alapszintű HTML-struktúrát a fájlhoz:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Az oldal felosztása egy kérelem szakaszba, ahol a felhasználó megadja a kéréshez szükséges összes információt, valamint egy válasz szakaszt, ahol megjelennek az információk. Adja hozzá az alábbi címkéket a következőhöz: `<div>` `<body>` . A `<hr>` címke vizuálisan elválasztja a kérés szakaszt a válasz szakaszból:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Adjon hozzá egy `<script>` címkét a `<head>` címkéhez, hogy tartalmazza az alkalmazáshoz tartozó JavaScriptet:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>A feltöltött fájl letöltése

Ahhoz, hogy a felhasználó kiválassza a feltölteni kívánt képet, az alkalmazás a `<input>` Type attribútummal beállított címkét használja `file` . A felhasználói felületnek egyértelművé kell tennie, hogy az alkalmazás a Bing használatával kapja meg a keresési eredményeket.

Adja hozzá a következőket a következőhöz: `<div>` `requestSection` `<div>` . A bemeneti fájl egyetlen képet fogad el, amely bármilyen típusú lehet (például .jpg, .gif, .png). Az `onchange` esemény megadja a kezelőt, amelyet a rendszer akkor hív meg, amikor a felhasználó kiválaszt egy fájlt.

A `<output>` címke a kiválasztott rendszerkép miniatűrjét jeleníti meg:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Fájlkezelő létrehozása

Hozzon létre egy kezelői függvényt, amely képes olvasni a feltölteni kívánt képet. Az objektumban található fájlok ismétlése közben `FileList` a kezelőnek meg kell győződnie arról, hogy a kijelölt fájl képfájl, és hogy mérete 1 MB vagy kevesebb. Ha a rendszerkép mérete nagyobb, akkor a feltöltés előtt csökkentenie kell a méretét. Végül a kezelő megjeleníti a rendszerkép miniatűrjét:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Előfizetési kulcs hozzáadása és tárolása

Az alkalmazásnak előfizetési kulcsra van szüksége, hogy hívásokat hajtson végre a Bing Visual Search API. Ebben az oktatóanyagban megadhatja azt a felhasználói felületen. Adja hozzá a következő `<input>` címkét (írja be a Type attribútumot szövegre) a `<body>` fájl `<output>` címkéje alá:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

A rendszerképpel és az előfizetési kulccsal megadhatja, hogy a Bing Visual Search meghívja a rendszerképet. Ebben az oktatóanyagban a hívás az alapértelmezett piaci ( `en-us` ) és a biztonságos keresési értéket ( `moderate` ) használja.

Ez az alkalmazás lehetősége van módosítani ezeket az értékeket. Adja hozzá a következőt `<div>` az előfizetési kulcs alá `<div>` . Az alkalmazás egy `<select>` címkét használ a piaci és a biztonságos keresési értékek legördülő listájának megadásához. Mindkét listában az alapértelmezett érték látható.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Keresési beállítások hozzáadása a weboldalhoz

Az alkalmazás elrejti a listát egy összecsukható `<div>` , amelyet a lekérdezési beállítások hivatkozása vezérel. Ha a lekérdezési beállítások hivatkozásra kattint, a `<div>` kibontások lehetőségre kattintva megtekintheti és módosíthatja a lekérdezési beállításokat. Ha ismét rákattint a lekérdezési beállítások hivatkozásra, az `<div>` összecsukja és rejtve marad. A következő kódrészlet a lekérdezési beállítások hivatkozás kezelőjét jeleníti meg `onclick` . A kezelő szabályozza, hogy a `<div>` kibontása vagy összecsukása megtörténjen-e. Adja hozzá a kezelőt a `<script>` szakaszhoz. A kezelőt a bemutató összes összecsukható `<div>` szakaszában használják.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>A kezelő meghívása `onclick`

Adja hozzá a következő `"Get insights"` gombot a törzsben lévő beállítások alatt `<div>` . A gomb lehetővé teszi a hívás kezdeményezését. Ha a gombra kattint, a kurzor a spinning WAIT kurzorra változik, és a `onclick` kezelő hívása megtörténik.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Adja hozzá a gomb `onclick` kezelőjét `handleQuery()` a `<script>` címkéhez.

## <a name="handle-the-query"></a>A lekérdezés kezelése

A kezelő `handleQuery()` gondoskodik arról, hogy az előfizetési kulcs megjelenő, 32 karakter hosszú legyen, és hogy a rendszerkép ki van választva. Eltávolítja az előző lekérdezésből származó megállapításokat is. Ezt követően meghívja a `sendRequest()` függvényt a hívás elvégzéséhez.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>A keresési kérelem elküldése

A `sendRequest()` függvény formázza a végpont URL-címét, beállítja a `Ocp-Apim-Subscription-Key` fejlécet az előfizetési kulcshoz, hozzáfűzi a feltölteni kívánt rendszerkép bináris fájlját, megadja a válasz kezelőjét, és a hívást a következő módon hozza:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Az API-válasz beolvasása és kezelése

A `handleResponse()` függvény kezeli a hívást a Bing Visual Search felé irányuló választ. Ha a hívás sikeres, elemzi a JSON-választ az egyes címkéknek megfelelően, amelyek a megállapításokat tartalmazzák. Ezután hozzáadja a keresési eredményeket az oldalhoz. Az alkalmazás ezután létrehoz egy összecsukható `<div>` címkét az egyes címkék számára, hogy kezelje a megjelenített adatmennyiséget. Adja hozzá a kezelőt a `<script>` szakaszhoz.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>A válasz feldolgozása

A `parseResponse` függvény átváltja a JSON-választ egy szótár objektummá, ha megismétli a műveletet `json.tags` .

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Címke szakasz összeállítása

A `buildTagSections()` függvény megismétli az ELEMZETT JSON-címkéket, és meghívja a `buildDiv()` függvényt, hogy létrehozza a `<div>` címkét. Az egyes címkék hivatkozásként jelennek meg. Ha a hivatkozásra kattint, a címke kibontja a címkével kapcsolatos bepillantást. Ha ismét rákattint a hivatkozásra, a szakasz összeomlik.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>A keresési eredmények megjelenítése a weblapon

A `buildDiv()` függvény meghívja a `addDivContent` függvényt az egyes címkék összecsukható tartalmának összeállításához `<div>` .

A címke tartalmazza a címkéhez tartozó válaszból származó JSON-t. Kezdetben csak a JSON első 100 karaktere jelenik meg, de a JSON-sztringre kattintva megjelenítheti az összes JSON-t. Ha ismét rákattint, a JSON-sztring összecsukódik, és ismét csak 100 karakter látható.

Következő lépésként adja hozzá a címkében található művelettípusokat. Minden Művelettípus esetében hívja meg a megfelelő függvényeket az elemzések hozzáadásához:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Információk megjelenítése különböző műveletekhez

A következő függvények a különböző műveletekkel kapcsolatos bepillantást jelenítik meg. A függvények vagy egy olyan kattintható képet vagy kattintható hivatkozást tartalmaznak, amely egy, a képpel kapcsolatos további információval ellátott weblapra küld. Ezt a lapot vagy a Bing.com, vagy a rendszerkép eredeti webhelye tárolja. Ebben az alkalmazásban nem jelenik meg az összes adatelemzési üzenet. A betekintéshez elérhető összes mező megtekintéséhez tekintse meg a [képek – Visual Search](https://aka.ms/bingvisualsearchreferencedoc) referenciát.

> [!NOTE]
> A lapon meg kell adni legalább egy betekintési információt. További információkért tekintse [meg a BING Search API használati és megjelenítési követelményeit](../bing-web-search/use-display-requirements.md) .

### <a name="relatedimages-insights"></a>RelatedImages-ismeretek

A `addRelatedImages()` függvény létrehoz egy címet a kapcsolódó rendszerképeket futtató összes webhelyhez, és megismétli a `RelatedImages` műveletek listáját, és hozzáfűzi `<img>` a címkét a külsőhöz a `<div>` következőhöz:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding-ismeretek

A `addPagesIncluding()` függvény létrehoz egy hivatkozást a feltöltött képet futtató összes webhelyhez, és megismétli a `PagesIncluding` műveletek listáját, és hozzáfűz egy `<img>` címkét a külsőhöz a `<div>` következőhöz:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches-ismeretek

A `addRelatedSearches()` függvény létrehoz egy hivatkozást a rendszerképet futtató webhelyhez, és megismétli a műveletek listáját, `RelatedSearches` és hozzáfűzi a `<img>` címkét a külsőhöz a `<div>` következőhöz:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Receptek bepillantást

A `addRecipes()` függvény létrehoz egy hivatkozást minden olyan recepthez, amelyet a műveletek listájának megismétlésével `Recipes` , valamint egy `<img>` címke a következőhöz való hozzáfűzésével adott vissza `<div>` :

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Vásárlási ismeretek

A `addShopping()` függvény létrehoz egy hivatkozást a visszaadott vásárlási eredményekhez a műveletek listájának megismétlésével `RelatedImages` , valamint egy `<img>` címke a következőn kívülre való hozzáfűzésével `<div>` :

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Products-ismeretek

A `addProducts()` függvény létrehoz egy hivatkozást a visszaadott termékek eredményeihez, ha megismétli a `Products` műveletek listáját, és hozzáfűz egy `<img>` címkét a külsőhöz a `<div>` következőhöz:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult-ismeretek

A `addTextResult()` függvény megjeleníti a rendszerképben felismerhető összes szöveget:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

A `addEntity()` függvény megjeleníti azt a hivatkozást, amely arra kéri a felhasználót, hogy Bing.com, hol szerezhetnek be adatokat a rendszerképben, ha a rendszer a következőt észlelte:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

A `addImageWithWebSearchUrl()` függvény egy kattintható képet jelenít meg a `<div>` felhasználónak, amely a Bing.com az eredményeket keresi:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>CSS-stílus hozzáadása

Adja hozzá a következő `<style>` szakaszt a `<head>` címkéhez a weblap elrendezésének rendszerezéséhez:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
> [Oktatóanyag: hasonló rendszerképek keresése az előző keresésekben a ImageInsightsToken használatával](./tutorial-visual-search-insights-token.md)
