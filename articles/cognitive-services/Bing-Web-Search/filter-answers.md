---
title: A Bing visszaadó webes válaszok szűrése |} Microsoft Docs
description: A kérdésekre adott válaszokat a webes Bing keresési API visszaadó szűréséhez használja a responseFilter szemlélteti.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: a5ee6241630ee24a05c2c4b932453bd7946a7508
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347883"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>A keresési válasz tartalmazza a válaszok szűrése  

Előfordulhat, hogy a webes, a Bing adja vissza, amelyet az összes tartalom fontos a keresés. Például ha a keresési lekérdezés "induló utazó + dinghies", a válasz tartalmazhatja az alábbi kérdésekre adott válaszok:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

Ha szeretné használni az adott típusú tartalmakat, például a képek, videók és híreket, csak ezek a válaszok segítségével kérheti a [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) lekérdezési paraméter. Bing kapcsolódó tartalom talált a megadott válaszokat, ha a Bing visszaadása. A válasz szűrője válaszok vesszővel tagolt listája. A következő bemutatja, hogyan használható `responseFilter` kérelem képek, videók és a vitorlás dinghies híreket. Kódolja a lekérdezési karakterláncot, ha a vesszővel válassza el egymástól: %2, C módosítható.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban látható az előző lekérdezés válasz. Látható Bing találtunk vonatkozó videó és híreket eredményeket, ezért a válasz nem tartalmazza azokat.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Bár a Bing nem adott vissza az előző válaszban videó és hírek eredményeket, azt nem jelenti azt, hogy a videó és hírek tartalmát nem létezik. Egyszerűen azt jelenti, hogy az oldal nem tartozik őket. Azonban ha Ön [lap](./paging-webpages.md) keresztül több találat, a következő lapjain valószínűleg tartalmazhat őket. Is ha meghívja a a [videó Search API](../bing-video-search/search-the-web.md) és [hírek Search API](../bing-news-search/search-the-web.md) végpontok közvetlenül, a válasz valószínűleg tartalmazza egyrészt az eredményeket. 

Nem ajánlott a áll `responseFilter` eredmények lekérése egyetlen API-val. Ha azt szeretné, hogy a tartalmat egy Bing API, hívható meg, hogy API közvetlenül. Például csak képek fogadásához kérelmet küld a kép keresési API-végpont `https://api.cognitive.microsoft.com/bing/v7.0/images/search` vagy egy másik [képek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) végpontok. Nem csak a megfelelő teljesítmény érdekében fontos a egyetlen API felület meghívásakor, de mivel a tartalom-specifikus API-k gazdagabb eredmények biztosít. Például használhatja szűrőket, amelyek nem érhetők el a webes keresés API eredmények szűrésére.  
  
Ahhoz, hogy a keresési eredmények az adott tartományból, tartalmazza a `site:` lekérdezés operátor szerepel a lekérdezésben.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE] 
> Attól függően, hogy a lekérdezés, ha a `site:` lekérdezési operátor nincs annak esélyét, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy a [biztonságos keresés](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) beállítást. Használjon `site:` csak akkor, ha tisztában lehet a tartalmat a helyen, és adott esetben támogatja a felnőtt tartalom lehetőségét. 
  
## <a name="limiting-the-number-of-answers-in-the-response"></a>A válaszban szereplő válaszok számának korlátozása

Bing rangsorolási a választ a rájuk adott válaszokat tartalmazza. Például, ha lekérdezést hajt végre *induló utazó + dinghies*, a Bing adja vissza `webpages`, `images`, `videos`, és `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

A válaszok száma korlátozza, hogy a Bing készletet ad vissza, a felső két válaszokkal (weblapjait és lemezképek), a [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) lekérdezésparaméter a 2. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A válasz tartalmazza csak `webPages` és `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Ha ad hozzá a `responseFilter` lekérdezésparaméter az előző lekérdezés és a csak weblapok úgy, hogy a weblapok és híreket, a válasz tartalmazza, mert hírek nem rangsorolását. készletéhez.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Válaszok, amelyek nem rangsora támogatása

Ha választ, amely egy lekérdezés visszaadja a Bing rangsorolva felső weblapjait, képek, videók és relatedSearches, a válasz ezek a válaszok tartalmazhat. Ha [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) a két (2), a Bing értéket ad vissza a felső két rangsorolt válasz: weblapjait és a képeket. Ha azt szeretné, hogy a Bing képek és videók szerepeljenek a választ, adja meg a [előléptetni](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) lekérdezésparaméter, majd állítsa be a képek és videó. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A fenti kérelemre adott válasz a következő: Bing adja vissza a felső két válasz, a weblapok és a képeket, és elősegíthető a videók be a válasz.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Ha `promote` híreket, hogy a válasz nem tartalmazza a hírek válasz mivel nincs a rangsorolt válasz&mdash;előléptetheti csak válaszok rangsorolását.

A kérdésekre adott válaszokat az előléptetni kívánt nem számítanak szemben a `answerCount` korlátot. Például, ha a rangsorolt válaszok híreket, lemezképek és videók, és beállíthatja `answerCount` 1 és `promote` híreket, hogy a válasz tartalmazza, híreket és a képeket. Vagy, ha a rangsorolt válaszok videók, lemezképek és híreket, a válaszban hírek és videók.

Használhatja a `promote` csak akkor, ha megadja a `answerCount` lekérdezési paraméter.
