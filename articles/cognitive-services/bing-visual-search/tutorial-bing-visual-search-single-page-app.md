---
title: 'Oktatóanyag: Egyoldalas webalkalmazás készítése – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Útmutató a Bing Visual Search API egyoldalas webalkalmazásban való használatához.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: e3cd36d799256406b3ae12f35303bd2406468b3c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227180"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Oktatóanyag: Egyoldalas Visual Search-webalkalmazás

A Bing Visual Search API a Bing.com képeket kereső szolgáltatásában látható képadatokhoz hasonló élményt nyújt. A Visual Search segítségével megadhat egy képet, és olyan információkat kaphat vissza a képpel kapcsolatban, mint a vizuálisan hasonló képek, a vásárlási lehetőségek, a képet tartalmazó weboldalak stb. 

Ez az oktatóanyag kibővíti a Bing Image Search oktatóanyagában használt egyoldalas webalkalmazást (lásd: [Egyoldalas webalkalmazás](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Az oktatóanyag elkezdéséhez a teljes forráskódot itt találja: [Egyoldalas webalkalmazás (forráskód)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Az oktatóanyag végleges forráskódjáért lásd: [Egyoldalas Visual Search-webalkalmazás](tutorial-bing-visual-search-single-page-app-source.md).

Az oktatóanyag az alábbi feladatokat tárgyalja:

> [!div class="checklist"]
> * A Bing Visual Search API meghívása egy képmegállapítási jogkivonattal
> * Hasonló képek megjelenítése

## <a name="call-bing-visual-search"></a>A Bing Visual Search meghívása
Szerkessze a Bing Image Search-oktatóanyagot és adja hozzá a következő kódot a 409. sorban található szkriptelem végéhez. A kód meghívja a Bing Visual Search API-t, és megjeleníti az eredményeket.

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
Adja hozzá a következő kódot a 151. sorban található `searchItemsRenderer` objektumhoz. Ez a kód hozzáad egy **hasonló keresése** hivatkozást, amely meghívja a `bingVisualSearch` függvényt, ha rákattintanak. A függvény argumentumként kapja meg az imageInsightsToken jogkivonatot.

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
> [Egyoldalas Visual Search-webalkalmazás forráskódja](tutorial-bing-visual-search-single-page-app-source.md)
> [Bing Visual Search API-referencia](https://aka.ms/bingvisualsearchreferencedoc)