---
title: Képek beszereznie az internetről – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API-val releváns képeket kereshet és kaphat le az internetről.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542766"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Képek beszereznie az internetről a Bing Képkereső API-val

A Bing Image Search REST API használatakor a következő GET-kérelem elküldésével kaphat le képeket az internetről, amelyek a keresési kifejezéshez kapcsolódnak:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Használja a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) lekérdezésparamétert az URL-kódolású keresési kifejezéshez. Ha például *vitorlás csónakokat*ad `q` `sailing+dinghies` meg, állítsa be a vagy `sailing%20dinghies`a értéket.

> [!IMPORTANT]
> * Minden kérést kiszolgálóról kell kérelmet intézni, nem ügyfélről.
> * Ha ez az első alkalom, hogy felhívja a Bing keresési API-k bármelyikét, ne adja meg az ügyfélazonosító fejlécét. Csak akkor adja meg az ügyfélazonosítót, ha korábban olyan Bing API-t hívott meg, amely ügyfélazonosítót adott vissza a felhasználó és az eszköz kombinációjához.

## <a name="get-images-from-a-specific-web-domain"></a>Képek beszereznie egy adott webtartományból

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](https://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Az operátort használó `site:` lekérdezésekre adott válaszok felnőtt tartalmat is tartalmazhatnak, függetlenül a [biztonságos Keresés](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) beállításától. Csak `site:` akkor használja, ha ismeri a tartomány tartalmát.

## <a name="filter-images"></a>Képek szűrése

 Alapértelmezés szerint a Képkereső API a lekérdezéshez kapcsolódó összes lemezképet visszaadja. Ha szűrni szeretné a Bing által visszaadott képeket (például csak átlátszó háttérrel vagy adott méretű képeket szeretne visszaadni), használja a következő lekérdezési paramétereket:

* [szempont](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)– A képek képarány szerinti szűrése (például normál vagy széles vásznú képek).
* [szín](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)– A képek szűrése domináns szín vagy fekete-fehér szerint.
* [frissesség](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)– A képek kor szerinti szűrése (például a Bing által az elmúlt héten felfedezett képek)
* [magasság](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [szélesség](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)–Képek szűrése szélesség és magasság szerint.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)– A képek szűrése tartalom szerint (például olyan képek, amelyek csak egy személy arcát jelenítik meg).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— Képek szűrése szöveg szerint (például ClipArt-elemek, animált GIF-ek vagy átlátszó hátterek).
* [licenc](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)– A webhelyhez társított licenc típusa szerint szűrheti a képeket.
* [méret](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)– A képek et méret szerint szűrheti, például kis képeket akár 200x200 képpontig.

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](https://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

A következő példa bemutatja, hogyan szerezhet be kis képeket a bing által az elmúlt héten felfedezett ContosoSailing.com.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing képkeresési válaszformátuma

A Bing válaszüzenete tartalmaz egy [Képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) választ, amely tartalmazza azon képek listáját, amelyeket a Cognitive Services a lekérdezés szempontjából relevánsnak határozott. A listában szereplő [minden képobjektum](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) a következő információkat tartalmazza a képről: az URL-cím, annak mérete, méretei, kódolási formátuma, a kép miniatűrjének URL-címe és a bélyegkép méretei.

> [!NOTE]
> * A képeket a válaszban megadott sorrendben kell megjeleníteni.
> * Mivel az URL-formátumok és -paraméterek előzetes értesítés nélkül változhatnak, használja az összes URL-t a hogyési.Because URL formats and parameters are to change without notice, use all URL as-is. Nem szabad függőséget az URL-formátumvagy paraméterek, kivéve, ha azt megjegyezte.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Amikor meghívja a Bing Image Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. A válasz `totalEstimatedMatches` mezője az összes megtekinthető kép becsült számát adja meg. A többi kép lapozásáról a [Lapozási képek](../paging-images.md)című témakörben talál részleteket.

## <a name="next-steps"></a>További lépések

Ha még nem próbálta ki a Bing Image Search API-t, próbálkozzon egy [rövid útmutatóval.](../quickstarts/csharp.md) Ha valami összetettebbet keres, próbálja ki az oktatóanyagot egy [egyoldalas webalkalmazás](../tutorial-bing-image-search-single-page-app.md)létrehozásához.
