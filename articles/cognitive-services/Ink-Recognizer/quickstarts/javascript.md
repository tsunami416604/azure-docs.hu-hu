---
title: 'Gyors útmutató: Ismeri fel a digitális ink a szabadkézi felismerő REST API-t és a Node.js'
description: A tinta felismerő API használatával indítsa el a digitális tollvonások felismerve.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 0dc672f0efc420ab73fd923191c2bd52fb571a4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056843"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Gyors útmutató: Ismeri fel a digitális ink a szabadkézi felismerő REST API-t és a JavaScript

Ez a rövid útmutató segítségével a digitális tollvonások a szabadkézi felismerő API használatának megkezdéséhez. A JavaScript-alkalmazását egy JSON-formátumú ink körvonal adatokat tartalmazó API-kérést küld, és a válasz megjeleníti.

Ezt az alkalmazást a webböngészőben fut, és a Javascript nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Általában az API-t kellene hívása egy digitális szabadkézi alkalmazásból. Ez a rövid útmutató a következő kézzel írt mintát ink körvonal adatokat küld a JSON-fájlból.

![írt szöveg felismerése képekből képe](../media/handwriting-sample.jpg)

Ez a rövid útmutató forráskódja találhatók [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Előfeltételek

- Egy webes böngésző
- Ez a rövid útmutató példa ink körvonal adatai találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, hozzon létre egy új `.html` fájlt. Ezután hozzá alapszintű HTML, az újabb adunk hozzá kódot.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Belül a `<body>` címkével, adja hozzá a következő HTML-kódot:
    1. Két szöveges területre a JSON-kérések és válaszok megjelenítése.
    2. Hívása gomb a `recognizeInk()` -függvény, amely később létrejön.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>A példa JSON-adatok betöltése

1. Belül a `<script>` címkével, hozzon létre egy változót az sampleJson. Ezután hozzon létre egy JavaScript-függvény nevű `openFile()` , amely megnyitja a Fájlkezelőben, ezért kiválaszthatja, hogy a JSON-fájlt. Ha a `Recognize ink` gombra kattint, ez a függvény meghívása, és a fájl olvasását kezdeni.
2. Használja a `FileReader` objektum `onload()` függvényt, hogy a fájl műveletek aszinkron feldolgozásához. 
    1. Cserélje le az összes `\n` vagy `\r` a fájl üres karakterlánc-karakter. 
    2. Használat `JSON.parse()` szöveg átalakítása érvényes JSON
    3. Frissítés a `request` szövegmezőbe az alkalmazásban. Használat `JSON.stringify()` formázhatja a JSON-karakterláncot. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>A tinta felismerő API-kérés küldése

1. Belül a `<script>` címkével, hozzon létre egy függvényt, nevű `recognizeInk()`. Ez a függvény később az API-t, és frissítse a lapot a választ. Adja hozzá a kódot, a következő lépéseket a függvényen belül. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Változók létrehozása a végponti URL-cím, az előfizetési kulcs és a JSON-mintának. Ezután hozzon létre egy `XMLHttpRequest` objektum az API-kérelem küldése. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Az a függvény létrehozása a `XMLHttpRequest` objektum. Ez a funkció a kérelem sikeres, az API-válasz elemzése, és megjeleníthetik azt az alkalmazást. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. A hiba függvény a kérelem-objektum létrehozása. Ez a funkció a konzolon naplózza a hibát. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Hozzon létre egy függvényt a kérelem objektum `onreadystatechange` tulajdonság. A kérelem objektum készültségi állapot megváltozik, a fenti lépjen vissza, és hiba funkciók lépnek érvénybe.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Az API-kérelem küldése. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc, és állítsa be a `content-type` , `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
