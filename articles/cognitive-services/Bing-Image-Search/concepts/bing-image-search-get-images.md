---
title: Rendszerképek lekérése a weben a Bing Image Search API az |} A Microsoft Docs
description: Keresse meg a Bing Image Search API használatával, és képeket lekérése az internetről.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082606"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Rendszerképek lekérése a weben a Bing Image Search API az

A Bing Image Search REST API használata esetén a felhasználó keresési kifejezés a következő GET kérelem küldésével kapcsolatos webes képeket is lekérése:

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

## <a name="getting-images-from-a-specific-web-domain"></a>Rendszerképek lekérése egy adott webes tartomány

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](http://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Adott válaszok segítségével a `site:` operátort tartalmazhatnak felnőtt tartalom, függetlenül attól, hogy a [biztonságos keresési](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) beállítás. Csak `site:` Ha a tartalom a tartományon.

A következő példa azt mutatja be, hogyan kérhet le olyan kis méretű képeket a ContosoSailing.com webhelyről, amelyeket a Bing az elmúlt héten fedezett fel.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filtering-images"></a>Képek szűrése

 Alapértelmezés szerint az Image Search API, amely a lekérdezés az összes rendszerkép ad vissza. Ha meg szeretné szűrheti a képeket, a Bing adja vissza (például csak képeket transparant háttér vagy meghatározott méretet), használhatja a lekérdezési paraméterek:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) – Képek szűrése méretarány alapján (például szabványos vagy szélesvásznú képek)
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) – Képek szűrése domináns szín alapján vagy fekete-fehér képek keresése
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) – Képek szűrése kor alapján (például a Bing által az elmúlt héten felfedezett képek)
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) – Képek szűrése szélesség és magasság alapján
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) – Képek szűrése tartalom alapján (például csak arcokat mutató képek)
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) – Képek szűrése típus alapján (például ClipArtok, animált GIF-ek vagy átlátszó hátterű képek)
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) – Képek szűrése a webhelyhez tartozó licenc típusa alapján
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) – Képek szűrése méret alapján, például kis képek, legfeljebb 200x200 képpontos méretig

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](http://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

A következő példa azt mutatja be, hogyan kérhet le olyan kis méretű képeket a ContosoSailing.com webhelyről, amelyeket a Bing az elmúlt héten fedezett fel.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing – Képkeresés válaszának formátuma

A Bing a válaszüzenet tartalmaz egy [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) választ, amely fontos lehet a lekérdezést az Azure cognitive services megállapítani a rendszerképek listáját tartalmazza. Minden egyes [kép](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektum a lista tartalmazza a kép a következő információkat: az URL-címet, annak méretét, a dimenziók, a kódolási formátum, a képet, és a miniatűr dimenziók Miniatűr URL-CÍMÉT.

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

Amikor meghívja a Bing Image Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. A válasz `totalEstimatedMatches` mezője az összes megtekinthető kép becsült számát adja meg. A képek közötti lapozásról a [képek lapozását](../paging-images.md) ismertető témakör tartalmaz további információkat.

## <a name="next-steps"></a>További lépések

Ha még nem próbálta a Bing Image Search API előtti, próbálja meg egy [rövid](../quickstarts/csharp.md). Ha összetettebb keres, próbálja meg a következő oktatóanyagban létrehozása egy [egyoldalas webalkalmazást](../tutorial-bing-image-search-single-page-app.md).
