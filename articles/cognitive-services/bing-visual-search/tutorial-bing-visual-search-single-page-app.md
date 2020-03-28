---
title: " Egyoldalas webalkalmazás létrehozása – Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként integrálhatja a Bing Visual Search API-t egyoldalas webalkalmazásba.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370103"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Oktatóanyag: Vizuális keresés egyoldalas webalkalmazás létrehozása

A Bing Visual Search API egy kép elemzési adatait adja vissza. Feltölthetsz egy képet, vagy megadhatsz egy URL-címet az egyikhez. Az elemzési adatok vizuálisan hasonló képek, vásárlási források, a képet tartalmazó weblapok stb. A Bing Visual Search API által visszaadott elemzések hasonlóak a Bing.com/images.

Ez az oktatóanyag bemutatja, hogyan bővítheti ki az egyoldalas webalkalmazást a Bing Image Search API-hoz. Az oktatóanyag megtekintéséhez vagy az itt használt forráskód beszerzéséhez olvassa el az [Oktatóanyag: Egyoldalas alkalmazás létrehozása a Bing Image Search API-hoz című témakört.](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)

Az alkalmazás teljes forráskódja (miután kiterjesztette a Bing Visual Search API használatára) elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Hívja fel a Bing Visual Search API-t, és kezelje a választ

Szerkesztsd a Bing Képkereső oktatóanyagát, és `<script>` add hozzá a `</script>` következő kódot az elem végéhez (és a záró címke előtt). A következő kód kezeli az API vizuális keresési válaszát, végighalad az eredményeken, és megjeleníti azokat:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

A következő kód keresési kérelmet küld az API-nak `handleVisualSearchResponse()`egy eseményfigyelő hívásával:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Megállapítási jogkivonat rögzítése

Adja hozzá a `searchItemsRenderer` következő kódot az objektumhoz. Ez a kód hozzáad egy **hasonló keresése** hivatkozást, amely meghívja a `bingVisualSearch` függvényt, ha rákattintanak. A függvény argumentumként `imageInsightsToken` kapja meg a függvényt.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Hasonló képek megjelenítése

Adja hozzá a következő HTML-kódot a 601. sorban. Ez a jelölőkód hozzáad egy elemet a Bing Visual Search API-hívás eredményeinek megjelenítéséhez:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

A rendelkezésre álló új JavaScript-kódok és HTML-elemek segítségével a keresési eredmények egy **hasonló keresése** hivatkozással jelennek meg. Kattintson a hivatkozásra a kiválasztott képhez hasonló képeket tartalmazó **Hasonló** szakasz feltöltéséhez. Lehetséges, hogy ki kell bontania a **Hasonló** szakaszt a képek megjelenítéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Kép körülvágása a Bing Visual Search SDK for C segítségével #](tutorial-visual-search-crop-area-results.md)
