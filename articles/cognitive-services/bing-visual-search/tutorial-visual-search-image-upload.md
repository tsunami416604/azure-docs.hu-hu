---
title: 'Oktatóanyag: Kép – a Bing Visual Search feltöltése'
titleSuffix: Azure Cognitive Services
description: Lebontja a kép a Bingbe való feltöltésének folyamatát, melynek során a rendszer megállapításokat ad vissza a képről, majd elemzi és megjeleníti a választ.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 0963c61027358c2c8e971533052631de28994b57
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491083"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Oktatóanyag: Képek feltöltése a Bing Visual Search API

A Bing Visual Search API lehetővé teszi a hasonló megjelennek a feltöltött képek weben való kereséshez. Ez az oktatóanyag segítségével hozzon létre egy webalkalmazást, amely egy lemezképet küldhet az API-hoz, és adja vissza, a képernyőn látható weblapon található meg a. Vegye figyelembe, hogy az alkalmazás nem felel meg az összes [követelmények Bing használata és megjelenítése](../bing-web-search/use-display-requirements.md) tartozó API használatával.

Megtalálhatja a teljes forráskódot a minta további hibakezelést és jegyzetek [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:

> [!div class="checklist"]
> * Feltölthet egy képet a Bing Visual Search API
> * Egy webalkalmazás kép keresési eredmények megjelenítése
> * Ismerje meg az API által biztosított különböző elemzések

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>A weblap felépítését

Hozzon létre egy HTML-oldalt, amely egy képet küld a Bing Visual Search API, insights kap, és megjeleníti őket. A kedvenc szerkesztőjében, vagy IDE hozzon létre egy "uploaddemo.html" nevű fájlt. Az alábbi alapszintű HTML struktúrával adja hozzá a fájlhoz:

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

Az oldal osztása egy kérelem szakaszt, ahol a felhasználó megadja a kéréshez szükséges összes információt, és a egy válasz szakaszt, ahol az insights jelennek meg. Adja hozzá a következő `<div>` -címkék a `<body>`. A `<hr>` címke vizuálisan elkülöníti a kérelem szakaszban, a válasz szakaszban:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Adjon hozzá egy `<script>` címkét a `<head>` címke tartalmazza a JavaScript, az alkalmazás:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>A feltöltendő fájl beolvasása

Ahhoz, hogy a felhasználó számára feltölteni a képet, az alkalmazás használja a `<input>` a type attribútumot állítsa megjelölés `file`. A felhasználói felületen kell, hogy törölje a jelet, hogy használja-e az alkalmazás a Bing keresési eredményt.

Adja hozzá a következő `<div>` , a `requestSection` `<div>`. A bemeneti fájl egyetlen képet fogad el, amely bármilyen típusú lehet (például .jpg, .gif, .png). Az `onchange` esemény megadja a kezelőt, amelyet a rendszer akkor hív meg, amikor a felhasználó kiválaszt egy fájlt.

A `<output>` címkét használ a kiválasztott kép egy miniatűr megjelenítéséhez:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Hozzon létre egy fájlt kezelő

Hozzon létre egy kezelő funkciót, amely képes olvasni a feltölteni kívánt kép. A fájlok az iterálás során a `FileList` objektumot, a kezelő győződjön meg arról, a kiválasztott fájl képfájlra, és annak mérete 1 MB vagy kisebb. Ha a kép mérete nagyobb, mielőtt feltöltené azokat csökkentenie kell a méretét. Végül a kezelő jeleníti meg a miniatűr kép:

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

## <a name="add-and-store-a-subscription-key"></a>Adja hozzá, és a egy előfizetési kulcsot tárolni

Az alkalmazás használatához egy előfizetési kulcsot, így a Bing Visual Search API-hívások. Ehhez az oktatóanyaghoz, amellyel biztosíthatja a felhasználói felületen. Adja hozzá a következő `<input>` címkét (az a szöveges értékre type attribútum) a `<body>` csak a fájl alábbi `<output>` címkét:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

A lemezkép és az előfizetési kulcsot győződjön meg, a hívást a Bing Visual Search kaphat elemzési információkat a lemezképet. Ebben az oktatóanyagban a hívás használja az alapértelmezett piaci (`en-us`) és biztonságos keresés (`moderate`).

Ez az alkalmazás ezeket az értékeket módosítsa lehetőség van. Adja hozzá a következő `<div>` az előfizetési kulcs alatt `<div>`. Használja az alkalmazás egy `<select>` piaci és biztonságos keresés értékeket adja meg a legördülő listából válassza ki a címkét. Mindkét listái az alapértelmezett érték.

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
                <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
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

## <a name="add-search-options-to-the-webpage"></a>A képernyőn látható weblapon keresési beállítások hozzáadása

Az alkalmazás elrejtése a egy összezárható listákat `<div>` , amelyek vezérlik a lekérdezési beállítások hivatkozására. A lekérdezési beállítások hivatkozására kattintva a `<div>` bontja ki, hogy mit talál és a lekérdezési beállítások módosítása. Ha a lekérdezési beállítások hivatkozásra kattint, a `<div>` magasságúra csökken, és el van rejtve. A következő kódrészlet azt mutatja be, a lekérdezési beállítások hivatkozására `onclick` kezelő. A kezelő szabályozza-e a `<div>` megjelenített vagy rejtett. A kezelő történő hozzáadása a `<script>` szakaszban. A kezelő által az összes használt összecsukható `<div>` bemutató szakaszait.

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

## <a name="call-the-onclick-handler"></a>Hívja a `onclick` kezelő

Adja hozzá a következő `"Get insights"` gombra a beállítások alatt `<div>` törzsében. A gomb lehetővé teszi, hogy a hívás indításához. A gombra kattint, amikor a kurzor módosul, amelyikben a tartalomfogyasztás várakozási mutatót, és a `onclick` kezelő nevezzük.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Adja hozzá a gomb `onclick` -kezelő `handleQuery()` , a `<script>` címke.

## <a name="handle-the-query"></a>A lekérdezés kezeléséhez

A kezelő `handleQuery()` biztosítja a jelen az előfizetési kulcsot és 32 karakter hosszúságú, és az, hogy a lemezkép van kiválasztva. Eltávolítja az előző lekérdezésből származó megállapításokat is. Ezután meghívja a `sendRequest()` függvény a híváshoz.

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

## <a name="send-the-search-request"></a>A keresési kérelem küldése

A `sendRequest()` függvény formázza a végpont URL-címe, beállítja a `Ocp-Apim-Subscription-Key` az előfizetési kulcsot a fejléc fűzi hozzá a bináris feltöltése a kép, adja meg a válasz-kezelő és a hívást:

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

## <a name="get-and-handle-the-api-response"></a>Első és az API-válasz kezelésére is alkalmas

A `handleResponse()` függvény kezeli a hívást a Bing Visual Search válaszát. Ha a hívás sikeres, elemzi a JSON-választ az egyes címkéknek megfelelően, amelyek a megállapításokat tartalmazzák. Ezután hozzáadja a keresési eredmények a lapot. Az alkalmazás ezután létrehoz egy összecsukható `<div>` minden címke kezeléséhez, hogy mennyi adat jelenik meg. Adja hozzá a kezelő a `<script>` szakaszban.

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

### <a name="parse-the-response"></a>A válasz elemzéséhez

A `parseResponse` függvény alakítja át a JSON-válasz egy dictionary objektum által iterálás `json.tags`.

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

### <a name="build-a-tag-section"></a>A kód szakaszban létrehozása

A `buildTagSections()` függvény végighalad az elemzett JSON-címkékkel és a hívások a `buildDiv()` függvény hozhat létre egy `<div>` minden tag számára. Minden címke egy hivatkozás jelenik meg. Amikor a hivatkozásra kattint, a címke bontja ki, az elemzéseket a címkéhez társított megjelenítése. A szakasz összecsukása a hivatkozásra kattintva újra okoz.

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

## <a name="display-the-search-results-in-the-webpage"></a>A keresési eredmények megjelenítése a weblap

A `buildDiv()` függvény meghívja a `addDivContent` függvény hozhat létre minden egyes címke tartalmát a összecsukható `<div>`.

A címke tartalmazza a címkéhez tartozó válaszból származó JSON-t. Kezdetben csak az első 100 karakter a JSON jelenik meg, de kattinthat a JSON-karakterlánc minden JSON megjelenítése. Ha ismét rákattint, a JSON-sztring összecsukódik, és ismét csak 100 karakter látható.

Következő lépésként adja hozzá a címkében található művelettípusokat. Minden egyes művelettípus hívja meg a megfelelő függvényeket a insights hozzáadása:

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

## <a name="display-insights-for-different-actions"></a>Elemzések a különböző műveletek megjelenítése

A következő funkciók meg különböző műveletekhez. A functions biztosítani egy kattintható rendszerképet vagy kattintható hivatkozás, amely egy weblaphoz csatlakozott, a lemezkép kapcsolatos további információkat. Ezen a lapon vagy Bing.com vagy a kép eredeti webhelyet üzemelteti. Nem minden az insights-adatok akkor jelenik meg, ebben az alkalmazásban. Az összes egy elemzést az elérhető mezők, olvassa el a [rendszerképek – vizuális keresés](https://aka.ms/bingvisualsearchreferencedoc) referencia.

> [!NOTE]
> Nincs egy minimális insight-adatokat a lapon meg kell jelenítenie. Tekintse meg a [Bing keresési API-t használja, és megjelenítési követelményeihez](../bing-web-search/use-display-requirements.md) további.

### <a name="relatedimages-insights"></a>RelatedImages insights

A `addRelatedImages()` függvény létrehoz egy címet a webhelyek, a kapcsolódó kép üzemeltető által iterálás a listája minden egyes `RelatedImages` műveleteket és hozzáfűzése egy `<img>` külső címkét `<div>` minden:

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

### <a name="pagesincluding-insights"></a>PagesIncluding insights

A `addPagesIncluding()` függvény létrehoz egy hivatkozást a webhelyek, a feltöltött kép üzemeltető által iterálás a listája minden egyes `PagesIncluding` műveleteket és hozzáfűzése egy `<img>` külső címkét `<div>` minden:

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

### <a name="relatedsearches-insights"></a>RelatedSearches insights

A `addRelatedSearches()` függvény létrehoz egy hivatkozást a webhely által listájának iterálás a képen üzemeltető `RelatedSearches` műveleteket és hozzáfűzése egy `<img>` külső címkét `<div>` minden:

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

### <a name="recipes-insights"></a>Receptek insights

A `addRecipes()` függvény hivatkozást hoz létre az egyes listájának iterálás által visszaadott receptek `Recipes` műveleteket és hozzáfűzése egy `<img>` külső címkét `<div>` minden:

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

### <a name="shopping-insights"></a>Insights vásárlás

A `addShopping()` függvény hivatkozást hoz létre, bármely listájának iterálás vissza vásárlási eredmények `RelatedImages` műveleteket és hozzáfűzése egy `<img>` külső címkét `<div>` minden:

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

### <a name="products-insights"></a>Termékek insights

A `addProducts()` függvény hivatkozást hoz létre, bármely listájának iterálás vissza termékek eredmények `Products` műveleteket és hozzáfűzése egy `<img>` külső címkét `<div>` minden:

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

### <a name="textresult-insights"></a>TextResult insights

A `addTextResult()` függvény a felismert szöveget az ábrán jeleníti meg:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

A `addEntity()` függvény egy hivatkozás, amely végigvezeti a felhasználót a Bing.com, akkor értesítést kaphatnak az entitástípus adatait a képen Ha bármelyik észlelt jeleníti meg:

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

A `addImageWithWebSearchUrl()` függvény egy kattintható kép megjelenítése a `<div>` átkerül a felhasználót, hogy a keresési eredmények a Bing.com oldalon:

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

## <a name="add-a-css-style"></a>Adjon hozzá egy CSS-stílus

Adja hozzá a következő `<style>` részt a `<head>` a képernyőn látható weblapon elrendezését címkét:

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

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
> [Oktatóanyag: Keresse meg a korábbi kereséseket ImageInsightsToken használatával hasonló képek](./tutorial-visual-search-insights-token.md)
