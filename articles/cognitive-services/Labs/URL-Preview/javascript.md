---
title: A projekt URL-cím Preview - Microsoft kognitív szolgáltatások JavaScript gyors üzembe helyezés |} Microsoft Docs
description: Ha gyorsan a Bing URL-cím előnézeti API használatával a Microsoft Azure kognitív Services minta parancsprogram.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9041a88a292fb2dabead69195ebc3074e2ecf486
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348634"
---
# <a name="url-preview-in-javascript"></a>JavaScript megtekintés URL-címe 

A következő alkalmazás egyoldalas JavaScriptet használ a SwiftKey hely URL-cím előnézete létrehozásához: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Előfeltételek

Az ingyenes próbaidőszakra hozzáférési kulcs beszerzése [kognitív szolgáltatások Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Kód forgatókönyv
A következő javascript-példa a bemeneti objektum szövegmező, ahol a felhasználó megadja az URL-cím, az előzetes tartalmazza.  Amikor a felhasználó kattint a **előzetes** gombra kattint, a onclick metódus útvonalakat `getPreview` állít elő, amikor kódot webes kérelem a **UrlPreview** végpont.

A kód létrehoz egy *XMLHttpRequest*, hozzáadja a *Ocp-Apim-előfizetés-kulcs* fejléc és a kulcsot, és elküldi a kérelmet.  Hozzáadja a válasz feldolgozása aszinkron eseménykezelő.

A válasz sikeresen adja vissza, ha a kezelő hozzárendeli a JSON-szövegben a válasz a `demo` bekezdés az oldalon. Más válasz elemek megjelenítéséhez a következő bekezdések értékre van beállítva.

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

**A futó bemutató**

![JavaScript URL-cím előzetes verzió – példa](./media/java-script-demo.png)

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához:

1. Cserélje le a `YOUR-SUBSCRIPTION-KEY` egy érvényes elérési kulcsot az előfizetéshez tartozó értéket.
2. A HTML- és parancsfájl .html kiterjesztésű fájlba mentése.
3. A weblap futtatását a böngészőben.
4. Használja a meglévő URL-címet, vagy adjon meg egy másik a szövegmezőben.
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
- [C# gyors üzembe helyezés](csharp.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)