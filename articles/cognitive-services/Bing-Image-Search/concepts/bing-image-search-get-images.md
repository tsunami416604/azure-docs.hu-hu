---
title: Rendszerképek lekérése a webes – a Bing Image Search API
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API használatával keresse meg és képeket lekérése az internetről.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: e7be29d2a3c41e421c2e98b9d9b19eed375180e2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876657"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Képek bekérése a weben a Bing Image Search API az

A Bing Image Search REST API használata esetén a keresési kifejezést a következő GET kérelem küldésével kapcsolatos webes kaphat lemezképek:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Egy kiszolgálóról, és nem az ügyféltől érkező összes kérés kell tenni.
> * Ha most először bármely, a Bing keresési API-k hívása, az ügyfél-ID fejléc nem tartalmaznak. Csak tartalmazza az ügyfél-Azonosítót, ha korábban már meghívta a Bing-API, amely az ügyfél-Azonosítót a felhasználó és eszköz kombinációt adja vissza.
> * Képek a válaszban megadott sorrendben kell megjeleníteni.

## <a name="get-images-from-a-specific-web-domain"></a>Rendszerképek lekérése egy adott webes tartományból

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](https://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Adott válaszok segítségével a `site:` operátort tartalmazhatnak felnőtt tartalom, függetlenül attól, hogy a [biztonságos keresési](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) beállítás. Csak `site:` Ha tisztában a tartalmat a tartományon.

A következő példa azt mutatja be, hogyan kérhet le olyan kis méretű képeket a ContosoSailing.com webhelyről, amelyeket a Bing az elmúlt héten fedezett fel.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filter-images"></a>Rendszerképek szűrése

 Alapértelmezés szerint az Image Search API, amely a lekérdezés az összes rendszerkép adja vissza. Ha szeretne szűrheti a képeket, a Bing adja vissza (például csak képeket áttetsző hátterű vagy meghatározott méretet), használja a lekérdezési paraméterek:

* [aspektus](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect)– rendszerképek szűrés oldalarány (például standard vagy nagy képernyőképek).
* [szín](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color)– színig vagy fekete-fehér-rendszerképek szűrése.
* [frissessége](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness)– rendszerképek szűrése kor (például képek felderítette a Bing, az elmúlt héten).
* [magasság](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [szélesség](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width)– szélességének és magasságának-rendszerképek szűrése.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent)– rendszerképek szűrése tartalom (például képek, amelyek csak egy személy face megjelenítése) által.
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype)– rendszerképek (például ClipArt, GIF, vagy áttetsző háttérrel) típus szerint szűrheti.
* [licenc](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license)– rendszerképek szűrése a helyhez hozzárendelt licenc típusa szerint.
* [méret](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size)– például a kis méretű szűrő lemezképek lemezkép legfeljebb 200 x 200 képpont.

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](https://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

 > [!NOTE]
 > Adott válaszok segítségével a `site:` operátort tartalmazhatnak felnőtt tartalom, függetlenül attól, hogy a [biztonságos keresési](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) beállítás. Csak `site:` Ha tisztában a tartalmat a tartományon.

Az alábbi példa bemutatja, hogyan kis képek beolvasni a Bing által az elmúlt héten felderített ContosoSailing.com.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing – Képkeresés válaszának formátuma

A Bing a válaszüzenet tartalmaz egy [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) választ, amely fontos lehet a lekérdezés megállapítani a Cognitive Services rendszerképek listáját tartalmazza. Minden egyes [kép](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektum a lista tartalmazza a kép a következő információkat: az URL-címet, annak méretét, a dimenziók, a kódolási formátum, a képet, és a miniatűr dimenziók Miniatűr URL-CÍMÉT.

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

Amikor meghívja a Bing Image Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. A válasz `totalEstimatedMatches` mezője az összes megtekinthető kép becsült számát adja meg. További részének a lemezképek lapra kapcsolatos részletekért lásd: [lapozás lemezképek](../paging-images.md).

## <a name="next-steps"></a>További lépések

Ha még nem próbálta a Bing Image Search API előtti, próbálja meg egy [rövid](../quickstarts/csharp.md). Ha összetettebb keres, próbálja meg létrehozni az oktatóanyag egy [egyoldalas webalkalmazást](../tutorial-bing-image-search-single-page-app.md).
