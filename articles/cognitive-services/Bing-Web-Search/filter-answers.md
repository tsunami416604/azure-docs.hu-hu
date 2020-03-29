---
title: A keresési eredmények szűrése – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A "responseFilter" lekérdezési paraméter használatával szűrheti, hogy a Bing milyen típusú válaszokat tartalmaz a válaszban (például képek, videók és hírek).
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220269"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>A keresési válasz által tartalmazott válaszok szűrése  

Amikor lekérdezi a webet, a Bing visszaadja a kereséshez megtalált összes releváns tartalmat. Ha például a keresési lekérdezés "sailing+dinghies", a válasz a következő válaszokat tartalmazhatja:

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

A Bing által visszaadott válaszok szűréséhez használja az alábbi lekérdezési paramétereket az API hívásakor.  

### <a name="responsefilter"></a>ResponseFilter (Válaszszűrő)

A Bing által a válaszban szereplő válaszok típusai (például képek, videók és hírek) szűrhetők a [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) lekérdezésparaméter használatával, amely a válaszok vesszővel tagolt listája. A válasz akkor kerül be a válaszba, ha a Bing releváns tartalmat talál hozzá. 

Ha ki szeretné zárni a válasz konkrét `-` válaszait, például a képeket, készítsen elő egy karaktert a választípusra. Példa:

```
&responseFilter=-images,-videos
```

Az alábbiakban bemutatjuk, hogyan lehet képeket, `responseFilter` videókat és híreket kérni a vitorlás csónakokról. A lekérdezési karakterlánc kódolásakor a vesszők %2C-re változnak.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban az előző lekérdezésre adott válasz látható. Mivel a Bing nem talált releváns videó- és híreredményeket, a válasz nem tartalmazza őket.

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

Bár a Bing az előző válaszban nem adott vissza videó- és híreredményeket, ez nem jelenti azt, hogy a videó- és hírtartalom nem létezik. Ez egyszerűen azt jelenti, hogy az oldal nem tartalmazza őket. Ha azonban több eredményt [ad,](./paging-webpages.md) a következő oldalak valószínűleg tartalmazzák azokat. Továbbá, ha közvetlenül hívja meg a [Video Search API-](../bing-video-search/search-the-web.md) és [hírkeresési API-végpontokat,](../bing-news-search/search-the-web.md) a válasz valószínűleg eredményeket tartalmaz.

Nem szívesen használja `responseFilter` egyetlen API-ból származó eredmények lehívását. Ha egyetlen Bing API-ból szeretne tartalmat kapni, hívja meg közvetlenül az API-t. Ha például csak a képek fogadásához küld egy kérelmet a Képkereső API-végpont, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` vagy egy másik [képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) végpontok. Az egyetlen API-hívás nem csak teljesítménymiatt fontos, hanem azért is, mert a tartalomspecifikus API-k gazdagabb eredményeket kínálnak. Az eredmények szűréséhez például olyan szűrőket is használhat, amelyek nem érhetők el a Webes keresési API számára.  

### <a name="site"></a>Webhely

Ha egy adott tartományból szeretne `site:` keresési eredményeket kapni, vegye fel a lekérdezési paramétert a lekérdezési karakterláncba.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> A lekérdezéstől függően, ha `site:` a lekérdezésoperátort használja, előfordulhat, hogy a válasz a [biztonságos Keresés](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) beállítástól függetlenül felnőtt tartalmat tartalmaz. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.

### <a name="freshness"></a>Frissesség

Ha a webes választalálati eredményeket a Bing által egy adott időszakban felderített weblapokra szeretné korlátozni, állítsa a [frissességlekérdezési](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) paramétert a következő, kis- és nagybetűket nem megkülönböztető értékek egyikére:

* `Day`— A Bing által az elmúlt 24 órában felfedezett weblapok visszaküldése
* `Week`— A Bing által az elmúlt 7 napban felfedezett weblapok visszaküldése
* `Month`— Az elmúlt 30 napban felfedezett visszaadási weboldalak

Ezt a paramétert az űrlapon egyéni dátumtartományra is beállíthatja. `YYYY-MM-DD..YYYY-MM-DD` 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Ha az eredményeket egyetlen dátumra szeretné korlátozni, állítsa a frissesség paramétert egy adott dátumra:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Az eredmények között szerepelhetnek olyan weblapok, amelyek kívül esnek a megadott időszakon, ha a Bing által a szűrési feltételeknek megfelelő weblapok száma kisebb, mint a kért weblapok száma (vagy a Bing által visszaadott alapértelmezett szám).

## <a name="limiting-the-number-of-answers-in-the-response"></a>A válaszok számának korlátozása a válaszban

A Bing több választípust is visszaadhat a JSON-válaszban. Ha például *vitorlázás+dinghies lekérdezést*kérdez, `videos`a `relatedSearches`Bing visszaadhat `webpages`, `images`, és .

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

Ha korlátozni szeretné a válaszok számát, hogy a Bing visszatér az első két válasz (weblapok és képek), állítsa a [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) lekérdezési paraméter 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A válasz `webPages` csak `images`és a .

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

Ha hozzáadja `responseFilter` a lekérdezési paramétert az előző lekérdezéshez, és weblapokra és hírekre állítja be, a válasz csak weblapokat tartalmaz, mert a hírek nem rangsorolva vannak.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Nem rangsorolt válaszok népszerűsítése

Ha a bing által egy lekérdezéshez visszaadott legmagasabb rangsorolt válaszok weblapok, képek, videók és kapcsolódó keresések, a válasz tartalmazza ezeket a válaszokat. Ha [az answerCount értékét](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) kettőre (2) állítja be, a Bing a két legfontosabb rangsorolt választ adja vissza: weblapokat és képeket. Ha azt szeretné, hogy a Bing képeket és videókat vegyen fel a válaszba, adja meg az [előléptetési](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) lekérdezés paramétert, és állítsa be képekre és videókra.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A fenti kérésre a következő válasz található. A Bing az első két választ, a weblapokat és a képeket adja vissza, és a videókat a válaszba lépteti fel.

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

Ha hírekre állítod, `promote` a válasz nem tartalmazza a hírválaszt,&mdash;mert ez nem rangsorolt válasz, amelyet csak rangsorolt válaszokat hirdethetsz.

A hirdetni kívánt válaszok nem `answerCount` számítanak bele a korlátba. Ha például a rangsorolt válaszok hírek, képek és `answerCount` videók, `promote` és 1-re és hírekre állítod, a válasz híreket és képeket tartalmaz. Vagy ha a rangsorolt válaszok videók, képek és hírek, a válasz videókat és híreket tartalmaz.

Csak akkor `promote` használható, ha `answerCount` megadja a lekérdezési paramétert.
