---
title: " Egyoldalas webes alkalmazás összeállítása – a Bing vizuális keresés"
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan integrálhatja a Bing Visual Search API egy egyoldalas webalkalmazást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e06fd7a4b2d072e5528643c2c8517d7545c36ef3
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338654"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Hozzon létre egy vizuális keresés egyoldalas webalkalmazást 

A Bing Visual Search API-Bing.com/images látható részletei hasonló élményt nyújt. A Visual Search segítségével megadhat egy képet, és olyan információkat kaphat vissza a képpel kapcsolatban, mint a vizuálisan hasonló képek, a vásárlási lehetőségek, a képet tartalmazó weboldalak stb. 

Ez a cikk ismerteti a Bing Image Search API-k kiterjesztése egy egyoldalas webalkalmazást. Oktatóanyag megtekintése, vagy szerezze be az itt használt forráskódját, lásd: [oktatóanyag: Hozzon létre egy egyoldalas alkalmazásból a Bing Image Search API](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md). 

A teljes (történő bővítése után, hogy a Bing Visual Search API), az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>A Bing Visual Search API meghívása, és kezelni a válasz

A Bing Képkeresés oktatóprogram szerkessze, és adja hozzá a következő kódot, végén a `<script>` elem (és a Bezárás előtt `</script>` címke). A következő kódot az API vizuális keresés válaszára kezeli, végighalad az eredményeket, és megjeleníti őket.

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

A következő kódot keresési kérelmet küld az API meghívásához egy Eseményfigyelőt használatával `handleVisualSearchResponse()`.


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

Adja hozzá az alábbi kódot a `searchItemsRenderer` objektum. Ez a kód hozzáad egy **hasonló keresése** hivatkozást, amely meghívja a `bingVisualSearch` függvényt, ha rákattintanak. A függvény argumentumként kapja meg az imageInsightsToken jogkivonatot.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Hasonló képek megjelenítése

Adja hozzá a következő HTML-kódot a 601. sorban. Ez a jelölőkód hozzáad egy elemet, amellyel megjeleníthetők a Bing Visual Search API meghívásának az eredményei.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

A rendelkezésre álló új JavaScript-kódok és HTML-elemek segítségével a keresési eredmények egy **hasonló keresése** hivatkozással jelennek meg. Kattintson a hivatkozásra a kiválasztott képhez hasonló képeket tartalmazó **Hasonló** szakasz feltöltéséhez. Lehetséges, hogy ki kell bontania a **Hasonló** szakaszt a képek megjelenítéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vágja körül, és töltse fel a képet](tutorial-visual-search-crop-area-results.md)
