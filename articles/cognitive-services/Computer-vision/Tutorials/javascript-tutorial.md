---
title: Számítógép Látástechnológiai API JavaScript oktatóanyag |} Microsoft Docs
description: Fedezze fel egy alapszintű JavaScript-alkalmazást, amely a számítógép Látástechnológiai API kognitív Microsoft-szolgáltatásokban. Feldolgozhatjuk miniatűrök létrehozása és a kép visual funkciók.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347226"
---
# <a name="computer-vision-api-javascript-tutorial"></a>Számítógép Látástechnológiai API JavaScript oktatóanyag

Ez az oktatóanyag bemutatja a Microsoft kognitív szolgáltatások számítógép stratégiai REST API felülete részeit.

Megismerkedhet a JavaScript alkalmazás optikai karakter felismerési (OCR) hajtsa végre, intelligens levágja miniatűröket, hozzon létre és észlelhető, kategorizálását, címkében és vizuális funkciókat, arc, beleértve a kép ismertetik a számítógép stratégiai REST API-t használ. Ez a példa lehetővé teszi egy kép URL-cím küldésekor elemzés vagy feldolgozása. Segítségével a nyílt forráskódú példa sablonként készítéséhez az alkalmazásba a JavaScript a számítógép stratégiai REST API-t használja.

A JavaScript űrlap alkalmazás már ki lett írva, de a számítógép stratégiai egyik funkció sem rendelkezik. Ebben az oktatóanyagban hozzáadja a megadott kód a számítógép stratégiai REST API-t végezze el az alkalmazás működését.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

Ebben az oktatóanyagban egy egyszerű szövegszerkesztőben fejlesztettek ki.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Számítógép Látástechnológiai API-ra szolgáltatásra, és egy előfizetés kulcs beszerzése 

Mielőtt létrehozná a példa, elő kell fizetnie a Microsoft kognitív szolgáltatások részét képező számítógép Látástechnológiai API. Előfizetés és a kulcskezelést részletek [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Az elsődleges és másodlagos kulcsok ebben az oktatóanyagban használható. 

## <a name="download-the-tutorial-project"></a>Az oktatóanyag projekt letöltése

Klónozott a [kognitív szolgáltatások JavaScript számítógép stratégiai oktatóanyag](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), vagy töltse le a .zip fájlt, és bontsa ki üres könyvtárba.

Ha meg kívánja használni a befejezett oktatóanyag hozzáadott oktatóanyag kóddal, használhatja a fájlokat a **befejezve** mappát.

## <a name="add-the-tutorial-code"></a>Adja hozzá az oktatóanyag kódot

A JavaScript-alkalmazását hat .html fájlt, egy, az egyes szolgáltatásokhoz van beállítva. Minden fájl mutatja be a számítógép átfogóan bemutató más függvény (elemzése, OCR stb.). A hat oktatóanyag részek nem rendelkezik egymástól függő szolgáltatásainak, úgy adhat hozzá az oktatóanyag kód egy fájlt, hat bővítményi vagy csak néhány fájlt. És a fájlok bármilyen sorrendben is hozzáadhat az oktatóanyag kódot.

Lássunk neki.

## <a name="analyze-an-image"></a>Kép elemzése

Az elemzési szolgáltatás számítógép átfogóan bemutató kép több mint 2000 felismerhető objektumok, élő szervezet, díszletek és műveletek elemzi. Az elemzés befejezése után a elemzése JSON-objektumból, amely leírja a kép leíró címkék, szín elemzés, feliratok és több adja vissza.

A elemzési szolgáltatás, az oktatóanyag alkalmazás befejezéséhez hajtsa végre az alábbi lépéseket:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Elemezze az 1. lépés: az űrlap gomb eseménykód kezelő hozzáadása

Nyissa meg a **analyze.html** fájlt egy szövegszerkesztőben, és keresse meg a **analyzeButtonClick** függvény a fájl alján.

A **analyzeButtonClick** esemény kezelő funkcióját a rendszer a képernyő, jeleníti meg az URL-címben megadott lemezkép ekkor meghívja a **AnalyzeImage** működnek, mint a kép elemzése.

Másolja és illessze be az alábbi kódot a **analyzeButtonClick** függvény.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Elemezze a 2. lépés: a REST API-hívások burkoló hozzáadása

A **AnalyzeImage** függvény becsomagolja a REST API hívása lemezkép elemzéséhez. Egy sikeres visszatérést követően az formázott JSON-elemzés a megadott szövegterület szó fog megjelenni, és a felirat jelenik meg a megadott tartomány.

Másolja és illessze be a **AnalyzeImage** működik a kódot közvetlenül az alatt a **analyzeButtonClick** függvény.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="analyze-step-3-run-the-application"></a>Elemezze a 3. lépés: az alkalmazás futtatása

Mentse a **analyze.html** fájlt, és nyissa meg a böngészőben. Helyezze be az Előfizetés kulcs a **előfizetés kulcs** mezőben, és győződjön meg arról, hogy a helyes régió használunk **terület előfizetési**. Adja meg egy URL-címet a lemezképre elemzése, majd kattintson a **elemzése kép** gombra a lemezkép elemzése és az eredményt.

## <a name="recognize-a-landmark"></a>Egy jellegzetes ismeri fel

A jellegzetes szolgáltatás számítógép átfogóan bemutató kép természetes és mesterséges jellegzetes, például hegyek vagy famous épületek hely elemzi. Az elemzés befejezése után, a környezet egy JSON-objektum, amely azonosítja a jellegzetes hely a lemezképben található adja vissza.

A jellegzetes funkció az oktatóanyag alkalmazás befejezéséhez hajtsa végre az alábbi lépéseket:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Jellegzetes 1. lépés: az űrlap gomb eseménykód kezelő hozzáadása

Nyissa meg a **landmark.html** fájlt egy szövegszerkesztőben, és keresse meg a **landmarkButtonClick** függvény a fájl alján.

A **landmarkButtonClick** esemény kezelő funkcióját a rendszer a képernyő, jeleníti meg az URL-címben megadott lemezkép ekkor meghívja a **IdentifyLandmarks** működnek, mint a kép elemzése.

Másolja és illessze be az alábbi kódot a **landmarkButtonClick** függvény.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Jellegzetes 2. lépés: a REST API-hívások burkoló hozzáadása

A **IdentifyLandmarks** függvény becsomagolja a REST API hívása lemezkép elemzéséhez. Egy sikeres visszatérést követően az formázott JSON-elemzés a megadott szövegterület szó fog megjelenni, és a felirat jelenik meg a megadott tartomány.

Másolja és illessze be a **IdentifyLandmarks** működik a kódot közvetlenül az alatt a **landmarkButtonClick** függvény.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="landmark-step-3-run-the-application"></a>Jellegzetes 3. lépés: az alkalmazás futtatása

Mentse a **landmark.html** fájlt, és nyissa meg a böngészőben. Helyezze be az Előfizetés kulcs a **előfizetés kulcs** mezőben, és győződjön meg arról, hogy a helyes régió használunk **terület előfizetési**. Adja meg egy URL-címet a lemezképre elemzése, majd kattintson a **elemzése kép** gombra a lemezkép elemzése és az eredményt.

## <a name="recognize-celebrities"></a>Hírességek felismerése

A Celebrities funkció számítógép átfogóan bemutató kép elemzi a famous számára. Az elemzés befejezése után a Celebrities egy JSON-objektum, amely azonosítja a lemezképben található Celebrities adja vissza.

Az oktatóanyag alkalmazás Celebrities jellemzője befejezéséhez hajtsa végre az alábbi lépéseket:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Celebrities 1. lépés: az űrlap gomb eseménykód kezelő hozzáadása

Nyissa meg a **celebrities.html** fájlt egy szövegszerkesztőben, és keresse meg a **celebritiesButtonClick** függvény a fájl alján.

A **celebritiesButtonClick** esemény kezelő funkcióját a rendszer a képernyő, jeleníti meg az URL-címben megadott lemezkép ekkor meghívja a **IdentifyCelebrities** működnek, mint a kép elemzése.

Másolja és illessze be az alábbi kódot a **celebritiesButtonClick** függvény.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Celebrities 2. lépés: a REST API-hívások burkoló hozzáadása

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="celebrities-step-3-run-the-application"></a>Celebrities 3. lépés: az alkalmazás futtatása

Mentse a **celebrities.html** fájlt, és nyissa meg a böngészőben. Helyezze be az Előfizetés kulcs a **előfizetés kulcs** mezőben, és győződjön meg arról, hogy a helyes régió használunk **terület előfizetési**. Adja meg egy URL-címet a lemezképre elemzése, majd kattintson a **elemzése kép** gombra a lemezkép elemzése és az eredményt.

## <a name="intelligently-generate-a-thumbnail"></a>Ezután a miniatűr készítése

A számítógép stratégiai miniatűr szolgáltatása miniatűr lemezképből hoz létre. Használatával a **intelligens körülvágása** funkció, a miniatűr szolgáltatás azonosítja egy lemezképet, és ez a terület több aesthetically pleasing miniatűr képeket létrehozásához a miniatűr center érdeklődik területén.

A miniatűr funkció az oktatóanyag alkalmazás befejezéséhez hajtsa végre az alábbi lépéseket:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>A miniatűr 1. lépés: az űrlap gomb eseménykód kezelő hozzáadása

Nyissa meg a **thumbnail.html** fájlt egy szövegszerkesztőben, és keresse meg a **thumbnailButtonClick** függvény a fájl alján.

A **thumbnailButtonClick** esemény kezelő funkcióját a rendszer a képernyő, jeleníti meg az URL-címben megadott lemezkép ekkor meghívja a **getThumbnail** kétszer létrehozásához két miniatűröket, egy intelligens levágja függvény és egy intelligens körülvágása nélkül.

Másolja és illessze be az alábbi kódot a **thumbnailButtonClick** függvény.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>A miniatűr 2. lépés: a REST API-hívások burkoló hozzáadása

A **getThumbnail** függvény becsomagolja a REST API hívása lemezkép elemzéséhez. Egy sikeres visszatérést követően a miniatűr jelenik meg a megadott img elemben.

Másolja és illessze be a következő **getThumbnail** működnek, mint alatt csak a **thumbnailButtonClick** függvény.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

### <a name="thumbnail-step-3-run-the-application"></a>A miniatűr 3. lépés: az alkalmazás futtatása

Mentse a **thumbnail.html** fájlt, és nyissa meg a böngészőben. Helyezze be az Előfizetés kulcs a **előfizetés kulcs** mezőben, és győződjön meg arról, hogy a helyes régió használunk **terület előfizetési**. Adja meg egy URL-címet a lemezképre elemzése, majd kattintson a **készítése a miniatűrök** gombra a lemezkép elemzése és az eredményt.

## <a name="read-printed-text-ocr"></a>Olvassa el nyomtatott szöveget (OCR)

A számítógép stratégiai optikai (OCR) funkciója elemzi nyomtatott szöveg képe. Az elemzés befejezése után a OCR egy JSON-objektum, amely tartalmazza a szöveg és a szöveg, a lemezkép helyét adja vissza.

Az oktatóanyag alkalmazás OCR jellemzője befejezéséhez hajtsa végre az alábbi lépéseket:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR 1. lépés: az űrlap gomb eseménykód kezelő hozzáadása

Nyissa meg a **ocr.html** fájlt egy szövegszerkesztőben, és keresse meg a **ocrButtonClick** függvény a fájl alján.

A **ocrButtonClick** esemény kezelő funkcióját a rendszer a képernyő, jeleníti meg az URL-címben megadott lemezkép ekkor meghívja a **ReadOcrImage** működnek, mint a kép elemzése.

Másolja és illessze be az alábbi kódot a **ocrButtonClick** függvény.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR 2. lépés: a REST API-hívások burkoló hozzáadása

A **ReadOcrImage** függvény becsomagolja a REST API hívása lemezkép elemzéséhez. Egy sikeres követően térjen vissza a leíró szöveg formázott JSON, és a szöveg helyét is megjelennek a megadott szövegterület.

Másolja és illessze be a következő **ReadOcrImage** működnek, mint alatt csak a **ocrButtonClick** függvény.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="ocr-step-3-run-the-application"></a>OCR 3. lépés: az alkalmazás futtatása

Mentse a **ocr.html** fájlt, és nyissa meg a böngészőben. Helyezze be az Előfizetés kulcs a **előfizetés kulcs** mezőben, és győződjön meg arról, hogy a helyes régió használunk **terület előfizetési**. Adjon meg egy URL-cím olvassa el, majd kattintson a szöveg a képhez a **olvasási kép** gombra a lemezkép elemzése és az eredményt.

## <a name="read-handwritten-text-handwriting-recognition"></a>Olvassa el a kézzel írt szöveg (kézírás-felismerés)

A kézírás-felismerés szolgáltatás számítógép átfogóan bemutató kép kézzel írt szöveg elemzi. Az elemzés befejezése után a kézírás-felismerés egy JSON-objektum, amely tartalmazza a szöveg és a szöveg, a lemezkép helyét adja vissza.

A kézírás-felismerés funkció az oktatóanyag alkalmazás befejezéséhez hajtsa végre az alábbi lépéseket:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Kézírás-felismerési 1.lépés: az űrlap gomb eseménykód kezelő hozzáadása

Nyissa meg a **handwriting.html** fájlt egy szövegszerkesztőben, és keresse meg a **handwritingButtonClick** függvény a fájl alján.

A **handwritingButtonClick** esemény kezelő funkcióját a rendszer a képernyő, jeleníti meg az URL-címben megadott lemezkép ekkor meghívja a **HandwritingImage** működnek, mint a kép elemzése.

Másolja és illessze be az alábbi kódot a **handwritingButtonClick** függvény.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Kézírás-felismerési lépés 2: a REST API-hívások burkoló hozzáadása

A **ReadHandwrittenImage** függvény a két lemezkép elemzéséhez szükséges REST API-hívásokat burkolja. Mivel a kézírás-felismerés időigényes folyamat, egy kétlépéses folyamat használatos. Az első hívás küldi el feldolgozásra; a kép a második hívás lekéri az észlelt szöveg, amikor feldolgozása befejeződött.

Miután a rendszer lekéri a szöveg, a leíró szöveget és a szöveg helye formázott JSON a megadott szövegterület jelenik meg.

Másolja és illessze be a következő **ReadHandwrittenImage** működnek, mint alatt csak a **handwritingButtonClick** függvény.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>Kézírás-felismerési lépés 3: az alkalmazás futtatása

Mentse a **handwriting.html** fájlt, és nyissa meg a böngészőben. Helyezze be az Előfizetés kulcs a **előfizetés kulcs** mezőben, és győződjön meg arról, hogy a helyes régió használunk **terület előfizetési**. Adjon meg egy URL-cím olvassa el, majd kattintson a szöveg a képhez a **olvasási kép** gombra a lemezkép elemzése és az eredményt.

## <a name="next-steps"></a>További lépések

- [Számítógép Látástechnológiai API C&#35; oktatóanyag](CSharpTutorial.md)
- [Számítógép Látástechnológiai API Python oktatóanyag](PythonTutorial.md)
