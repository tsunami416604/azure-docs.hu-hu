---
title: Keresési eredmények – a Bing Web Search API szűrése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szűrheti és a Bing Web Search API a keresési eredmények megjelenítéséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 26c38c34543683a3fc450d3a0ae932d8bd30dc98
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199493"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>A keresési válasz tartalmazza a válaszokat szűrése  

Előfordulhat, hogy a webes, a Bing a keresés megtalálja az összes kapcsolódó tartalom adja vissza. Például ha a keresési lekérdezés "dinghies induló utazó +", a válasz tartalmazhat a következő választ:

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
A tartalom (például képek, videók és hírek) esetén kap típusú használatával szűrheti a [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) lekérdezési paraméter. Ha a Bing kapcsolódó tartalmat a megadott válaszok talált, a lesz visszaadva. A válasz szűrő az válaszokat vesszővel tagolt listája. 

Kizárandó konkrét típusú tartalmakra, például képeket, a válasz, hozzáadhat egy `-` karakter elejéhez a `responseFilter` értéket. Kizárt típusok elkülönítheti a vessző (`,`). Példa:

```
&responseFilter=-images,-videos
```


A következő bemutatja, hogyan `responseFilter` a kérelem képek, videók és hírek, hajózási dinghies. A lekérdezési karakterláncot kódol, amikor a vesszők módosítani: %2, C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban az előző lekérdezésre adott válasz látható. A Bing nem található a releváns videókat és hírkeresési eredményeket, mert a válasz nem tartalmazza azokat.

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

Bing nem adott vissza az előző válaszban kapott a video- és hírkeresési eredményeket, bár ez nem jelenti azt, hogy a videó és hír tartalom nem létezik. Egyszerűen azt jelenti, hogy az oldal nem tartalmazza azokat. Azonban ha Ön [oldal](./paging-webpages.md) keresztül további találatok, a következő oldalakhoz valószínűleg tartalmazhat őket. Is Ha felhívja a [Video Search API](../bing-video-search/search-the-web.md) és [News Search API](../bing-news-search/search-the-web.md) végpontok közvetlenül, a válasz valószínűleg tartalmazza egyrészt az eredményeket.

Nem ajánlott használatával `responseFilter` egyetlen API-ból származó eredmények eléréséhez. Ha azt szeretné, hogy a tartalom egy egyetlen Bing-API-ból, hívja közvetlenül API. Például csak képeket fogadásához egy kérelmet küld a képkeresési API-végpont `https://api.cognitive.microsoft.com/bing/v7.0/images/search` vagy egy másik [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) végpontok. Nem csupán a teljesítmény javítása érdekében fontos a egyetlen API meghívására, de mivel a tartalom-specifikus API-k elérhetővé gazdagabb eredményeket. Például használhatja a szűrők, amelyek nem érhetők el a Web Search API-nak az eredmények szűréséhez.  

A keresési eredmények beolvasása egy adott tartományban, például a `site:` operátor a lekérdezési karakterláncban.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Ha használja a lekérdezéstől függően a `site:` operátor, annak esélyét, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy van a [biztonságos keresési](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) beállítás. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.

## <a name="limiting-the-number-of-answers-in-the-response"></a>A válaszban szereplő válaszok számának korlátozása

A Bing válaszokat tartalmazza ennek a területnek a választ. Ha például lekérdezheti, ha *induló utazó + dinghies*, a Bing adja vissza `webpages`, `images`, `videos`, és `relatedSearches`.

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

A válaszok száma korlátozható, hogy a Bing készletet ad vissza az első két válaszokkal (weblapjait és képeket), a [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) lekérdezési paraméter, 2.

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

Ha a `responseFilter` lekérdezési paraméter az előző lekérdezést és weblapjait és hírek, a válasz tartalmaz egyetlen weblapok mert hírek van nem rangsorolva, amelyekről beállítása.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Válaszok, amelyek nem súlyosságon előléptetése

Ha az első választ, amely egy lekérdezés visszaadja a Bing felső weblapok, képek, videók és relatedSearches, a válasz tartalmazhat ezek a válaszok. Ha [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) , két (2), a Bing értéket ad vissza a felső két rangsorolt válaszok: weblapjait és a képeket. Ha azt szeretné, hogy tartalmazza a képek és videók a válaszban a Bing, adja meg a [előléptetése](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) lekérdezési paraméter, és állítsa be a képek és videók.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban látható a fenti irányuló kérelemre adott válasz. A Bing a felső két válaszokat, weblapjait és képeket ad vissza, és elősegíti a videók, a választ.

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

Ha `promote` hírekhez, a válasz nem tartalmazza a hírek választ, mivel nem egy rangsorolt válasz&mdash;csak az első választ léptetheti elő.

A válaszok előléptetni kívánt nem számítanak bele a `answerCount` korlátot. Például, ha a rangsorolt válaszok híreket, képek és videók, és beállított `answerCount` 1 és `promote` hírekhez, a válasz tartalmazza hírek és lemezképek. Vagy, ha a rangsorolt válaszok videók, képek és hírek, a válasz tartalmazza, videók és hírek.

Használhat `promote` csak akkor, ha adja meg a `answerCount` lekérdezési paraméter.
