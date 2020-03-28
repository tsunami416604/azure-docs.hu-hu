---
title: 'Rövid útmutató: A digitális tinta felismerése a Tintafelismerő REST API-val és a Node.js'
titleSuffix: Azure Cognitive Services
description: A Tintafelismerő API-val elkezdheti felismerni a digitális tintavonásokat ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448141"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Rövid útmutató: A digitális tinta felismerése a Tintafelismerő REST API-val és a JavaScript-tel

Ezzel a rövid útmutatóval elkezdheti használni a Tintafelismerő API-t digitális tintavonásokon. Ez a JavaScript-alkalmazás JSON-formátumú tollvonás-adatokat tartalmazó API-kérelmet küld, és megjeleníti a választ.

Míg ez az alkalmazás javascriptben íródott, és fut a böngészőben, az API egy RESTful webszolgáltatás kompatibilis a legtöbb programozási nyelvek.

Az API-t általában egy digitális szabadkézi alkalmazásból kell meghívni. Ez a rövid útmutató a következő kézzel írt minta tollvonási adatait küldi egy JSON-fájlból.

![kézzel írt szöveg képe](../media/handwriting-sample.jpg)

A rövid útmutató forráskódja a [GitHubon](https://go.microsoft.com/fwlink/?linkid=2089905)található.

## <a name="prerequisites"></a>Előfeltételek

- Webböngésző
- A rövid útmutató példa szabadkézi körvonalai a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Tintafelismerő erőforrás létrehozása

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A kedvenc IDE vagy szerkesztő, `.html` hozzon létre egy új fájlt. Ezután adja hozzá az alapvető HTML-t a kódhoz, amelyet később adunk hozzá.
    
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

2. A `<body>` címkén belül add hozzá a következő html:Within the tag, add a following html:
    1. Két szövegterület a JSON-kérelem és -válasz megjelenítéséhez.
    2. Egy gomb a `recognizeInk()` funkció hívására, amely később jön létre.
    
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

1. A `<script>` címke, hozzon létre egy változót a sampleJson. Ezután hozzon létre `openFile()` egy JavaScript függvénynevű, amely megnyitja a fájlkezelő, így kiválaszthatja a JSON fájlt. Amikor `Recognize ink` a gombra kattintanak, meghívja ezt a funkciót, és elkezdi olvasni a fájlt.
2. Az `FileReader` objektum függvényének `onload()` használatával aszinkron módon dolgozza fel a fájlt. 
    1. A `\n` fájl `\r` bármely vagy karakterét lecserélheti üres karakterláncra. 
    2. A `JSON.parse()` szöveg érvényes JSON-ra konvertálása
    3. Frissítse `request` az alkalmazás szövegmezőjét. A `JSON.stringify()` JSON-karakterlánc formázására használható. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Kérelem küldése a Tintafelismerő API-nak

1. A `<script>` címkén belül `recognizeInk()`hozzon létre egy függvényt, amelynek neve . Ez a függvény később meghívja az API-t, és frissíti az oldalt a válasz. Adja hozzá a kódot a következő lépések ben ezt a funkciót. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Hozzon létre változókat a végpont URL-címéhez, az előfizetési kulcshoz és a minta JSON-hoz. Ezután `XMLHttpRequest` hozzon létre egy objektumot az API-kérelem elküldéséhez. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Hozza létre az `XMLHttpRequest` objektum visszatérési funkcióját. Ez a függvény elemzi az API-választ egy sikeres kérelemből, és megjeleníti azt az alkalmazásban. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Hozza létre a kérelemobjektum hibafüggvényét. Ez a függvény naplózza a hibát a konzolra. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Hozzon létre egy függvényt `onreadystatechange` a kérelemobjektum tulajdonságához. Amikor a kérelemobjektum készültségi állapota megváltozik, a rendszer a fenti visszatérési és hibafüggvényeket alkalmazza.
            
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
    
    5. Küldje el az API-kérelmet. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot `content-type` a fejléchez, és állítsa be a`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Az alkalmazás futtatása és a válasz megtekintése

Ez az alkalmazás futtatható a böngészőben. A sikeres választ JSON formátumban adja vissza. A JSON-választ a [GitHubon is megtalálhatja:](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API-hivatkozás](https://go.microsoft.com/fwlink/?linkid=2089907)

A szabadkézi elismerésapi digitális szabadkézi alkalmazásokban való működésének megtekintéséhez tekintse meg a githubon található alábbi mintaalkalmazásokat:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
