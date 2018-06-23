---
title: Bing kép keresési egyoldalas webalkalmazást |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan használja a kép Bing keresési API egy egyoldalas webalkalmazást.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348710"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Oktatóanyag: Visual keresési egyoldalas webalkalmazás

Bing Visual Search API Bing.com/images látható részletei hasonló élményt nyújt. Visual keresés adja meg a lemezkép és a szükséges segítségnyújtáshoz például vizuálisan hasonló lemezképek, a vásárlásra szolgáló adatforrások, amelyek közé tartozik a lemezképet, és több képet kaphat. 

Ez az oktatóanyag a Bing kép keresési oktatóanyagot terjeszti ki a egyoldalas webalkalmazást (lásd: [egyoldalas webalkalmazást](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Ez az oktatóanyag elindításához teljes forráskód, lásd: [egyoldalas webalkalmazást (forráskód)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Ez az oktatóanyag utolsó forráskódját, lásd: [Visual keresési egyoldalas webalkalmazást](tutorial-bing-visual-search-single-page-app-source.md).

Tartozó feladatok a következők:

> [!div class="checklist"]
> * Bing Visual Search API hívja meg a lemezkép insights tokent
> * Hasonló képeket jeleníthessen meg

## <a name="call-bing-visual-search"></a>Bing Visual keresési hívás
A Bing kép Search oktatóanyaga Szerkesztés, és adja hozzá a következő kódot a parancsfájl elem 409 sor végére. Ez a kód a Bing Visual keresési API-t, és megjeleníti az eredményeket.

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

## <a name="capture-insights-token"></a>Elemzések token rögzítése
Adja hozzá az alábbi kódot a `searchItemsRenderer` sor 151 objektum. Ezt a kódot ad hozzá egy **található hasonló** hivatkozás, amely behívja a `bingVisualSearch` kattintáskor működik. A függvény a imageInsightsToken argumentumként fogadja.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Hasonló képeket jeleníthessen meg
Adja hozzá az alábbi HTML-kódja 601 sor. A jelölőkód hozzáadja az Visual Bing keresési API-hívás eredményeit megjelenítéséhez használt elemet.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Minden új JavaScript-kód és HTML-elem van érvényben, a keresési eredmények között megjelenik egy **található hasonló** hivatkozásra. A hivatkozásra kattintva feltöltése a **hasonló** szakasz ismerteti, amelyet kivételezett hasonló képek. Lehet, hogy bontsa ki a **hasonló** szakaszt a képek megjelenítéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Visual keresési egyoldalas Web app forrás](tutorial-bing-visual-search-single-page-app-source.md)
> [Bing Visual keresési API-referencia](https://aka.ms/bingvisualsearchreferencedoc)