---
title: JavaScript a rövid útmutató a projekt URL-cím előnézete – a Microsoft Cognitive Services |} A Microsoft Docs
description: Példaszkript gyorsan használatba a Bing URL-cím előzetes API-t a Microsoft Cognitive Services, Azure-ban.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dda6f7c105dfbadc3c22f0c008aa8759fe12fa03
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301352"
---
# <a name="url-preview-in-javascript"></a>A JavaScript URL-cím előnézete 

A következő egyoldalas alkalmazás JavaScript használatával hozzon létre egy SwiftKey webhely URL-cím előnézete: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Előfeltételek

Az ingyenes próbaverzióra hívóbetű lekérése [Cognitive Services Labs tevékenységét](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Kód forgatókönyv
Ez a példa javascript tartalmaz egy szövegmező bemeneti objektumot, ahol a felhasználó sikeresen megadja az URL-cím, az előzetes verzióra.  Amikor a felhasználó rákattint a **előzetes** gomb, onclick metódus útvonalakat `getPreview` állít elő, ahol kódot a webes kérelem a **UrlPreview** végpont.

A kód létrehoz egy *XMLHttpRequest*, hozzáadja a *Ocp-Apim-Subscription-Key* fejlécet és a kulcsot, és elküldi a kérelmet.  Hozzáad egy aszinkron eseménykezelő feldolgozni a választ.

Sikerült a választ adja vissza, ha a kezelő rendeli hozzá a JSON-szöveget az adott válaszban a `demo` bekezdés az oldalon. Más válasz elemek vannak állítva a következő bekezdések a megjelenítéshez.

**Nyers JSON-válasz**

````
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

````

**A futó demó**

![A példa JavaScript URL-cím előnézete](./media/java-script-demo.png)

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához:

1. Cserélje le a `YOUR-SUBSCRIPTION-KEY` értéket az előfizetéshez tartozó érvényes elérési kulcsot.
2. Mentse a HTML- és parancsfájl .html kiterjesztésű fájlt.
3. A weblap futtatása a böngészőben.
4. Használja a meglévő URL-címet, vagy adjon meg egy másik a szövegmezőbe.
5. Kattintson a **előzetes** gombra.

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
- [C# gyorsútmutató](csharp.md)
- [Java a rövid útmutató](java-quickstart.md)
- [Csomópont a rövid útmutató](node-quickstart.md)
- [Python a rövid útmutató](python-quickstart.md)
