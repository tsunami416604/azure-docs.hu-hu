---
title: 'Gyors útmutató: Projekt URL-cím előnézete, JavaScript'
titlesuffix: Azure Cognitive Services
description: A Bing URL Preview API JavaScript nyelvvel való azonnali használatába bevezető szkriptminta.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 08405d253cbc62b9f83bed63e3c252673649f7f9
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101202"
---
# <a name="quickstart-url-preview-in-javascript"></a>Gyors útmutató: A JavaScript URL-cím előnézete 

Az alábbi egyoldalas alkalmazás a JavaScript segítségével hoz létre egy URL-előnézetet a SwiftKey webhelyhez: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Előfeltételek

Hozzáférési kulcs lekérése a [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview) ingyenes próbaverziójához

## <a name="code-scenario"></a>Kódforgatókönyv
Az alábbi JavaScript-példa tartalmaz egy szövegmezős bemeneti objektumot, ahol a felhasználó megadja az előnézethez használni kívánt URL-címet.  Ha a felhasználó az **előnézeti** gombra kattint, az onclick metódus átirányítja a `getPreview` metódushoz, ahol a kód létrehoz egy webes kérést az **UrlPreview** végponthoz.

A kód létrehoz egy *XMLHttpRequest*-kérést, hozzáadja az *Ocp-Apim-Subscription-Key* fejlécet és kulcsot, majd elküldi a kérést.  Hozzáad egy aszinkron eseménykezelőt is a válasz feldolgozásához.

Ha a sikerességet jelző válasz érkezik, a kezelő hozzárendeli a válasz JSON-szövegét a `demo` bekezdéshez az oldalon. Az egyéb válaszelemek az alábbi bekezdésekben való megjelenítéshez vannak beállítva.

**Nyers JSON-válasz**

```
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

**A példa futás közben**

![URL-cím előnézete JavaScript nyelven – példa](./media/java-script-demo.png)

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatása:

1. A `YOUR-SUBSCRIPTION-KEY` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
2. Mentse a HTML-t és a szkriptet egy .html kiterjesztésű fájlba.
3. Futtassa a weboldalt egy böngészőben.
4. Használhatja a meglévő URL-címet, vagy megadhat egy másikat a szövegmezőben.
5. Kattintson a **Preview** (Előnézet) gombra.

**Forráskód:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)
