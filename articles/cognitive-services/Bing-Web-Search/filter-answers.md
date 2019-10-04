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
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: a89d73b63680415aa8e516926b8e1d6c59ffbbad
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626018"
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

## <a name="query-parameters"></a>Lekérdezési paraméterek

A Bing által visszaadott válaszokat szűréséhez használja az alábbi lekérdezési paramétereket az API hívásakor.  

### <a name="responsefilter"></a>ResponseFilter

A válaszokat, amely a válaszban (például képek, videók és hírek) tartalmazza a Bing típusú használatával szűrheti a [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) lekérdezési paraméter, amely válasz egy vesszővel tagolt listája. Választ fog szerepelni a választ, ha a Bing kapcsolódó tartalom megkeresi azt. 

Adott válaszok kizárása a választ, például képeket, jogosultságokat egy `-` karakter, a válasz típusát. Példa:

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

Nem ajánlott használatával `responseFilter` egyetlen API-ból származó eredmények eléréséhez. Ha azt szeretné, hogy a tartalom egy egyetlen Bing-API-ból, hívja közvetlenül API. Például csak képeket fogadásához egy kérelmet küld a képkeresési API-végpont `https://api.cognitive.microsoft.com/bing/v7.0/images/search` vagy egy másik [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) végpontok. Nem csupán a teljesítmény javítása érdekében fontos a egyetlen API meghívására, de mivel a tartalom-specifikus API-k elérhetővé gazdagabb eredményeket. Például használhatja a szűrők, amelyek nem érhetők el a Web Search API-nak az eredmények szűréséhez.  

### <a name="site"></a>Hely

A keresési eredmények beolvasása egy adott tartományban, például a `site:` lekérdezési paraméter, a lekérdezési karakterláncban.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Ha használja a lekérdezéstől függően a `site:` operátor, annak esélyét, hogy a válasz tartalmazhat felnőtt tartalom, függetlenül attól, hogy van a [biztonságos keresési](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) beállítás. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.

### <a name="freshness"></a>Közzététel ideje

Korlátozza a Webes válasz eredményeket, amelyek egy adott időszak során felderített a Bing, állítsa be a [frissessége](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) lekérdezési paraméter a következő kis-és értékek egyikére:

* `Day` – Ad vissza, amelyek az elmúlt 24 órában észlelt a Bing
* `Week` – Ad vissza, amelyek az elmúlt 7 napon belül felderített Bing
* `Month` – Ad vissza, amelyek az elmúlt 30 napon belül felderített

Ezt a paramétert is megadhat, az űrlap egy egyéni dátumtartományt `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Korlátozza az eredményeket egy adott dátumot, állítsa be a frissesség paraméter a megadott dátum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Az eredmények tartalmazhat, amelyek a megadott időszakon kívül esnek, ha weboldalt, Bing, a szűrési feltételeknek megfelelő száma kisebb, mint a kért weblapok (vagy a alapértelmezett számát, amelyet a Bing adja vissza).

## <a name="limiting-the-number-of-answers-in-the-response"></a>A válaszban szereplő válaszok számának korlátozása

A Bing többféle választ adhat vissza a JSON-válaszban. Például, ha lekérdezheti, ha *induló utazó + dinghies*, a Bing vissza `webpages`, `images`, `videos`, és `relatedSearches`.

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

A válaszok száma korlátozható, hogy a Bing készletet ad vissza az első két válaszokkal (weblapjait és képeket), a [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) lekérdezési paraméter, 2.

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

Ha az első választ, amely egy lekérdezés visszaadja a Bing felső weblapok, képek, videók és relatedSearches, a válasz tartalmazhat ezek a válaszok. Ha [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) , két (2), a Bing értéket ad vissza a felső két rangsorolt válaszok: weblapjait és a képeket. Ha azt szeretné, hogy tartalmazza a képek és videók a válaszban a Bing, adja meg a [előléptetése](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) lekérdezési paraméter, és állítsa be a képek és videók.

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
