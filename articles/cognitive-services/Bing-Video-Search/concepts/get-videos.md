---
title: Keresési kérések küldését a Bing Videókeresési API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a keresési lekérdezéseket küld a Bing Videókeresési API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 08e8050fde6d2cf6249826911117dad9f595b6e4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879599"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Keressen videókat a Bing Videókeresési API

A Bing Videókeresési API megkönnyíti a Bing cognitive news keresési képességek integrálása az alkalmazásokba. Bár az API-t elsősorban megkeresi és releváns videókat a weben adja vissza, számos funkciót kínál intelligens és összpontosítás videó lekéréséhez a weben.

## <a name="getting-videos"></a>Videók lekérése

A felhasználó keresőkifejezéséhez kapcsolódó, az interneten megtalálható videók lekéréséhez küldje el a következő GET kérést:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Minden kérést egy kiszolgálóról kell elküldeni.

Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához.

Ha egy konkrét tartományban található videókat szeretne lekérni, használja a [site:](https://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A válasz tartalmaz egy [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) választ, amely azoknak a videóknak a listáját tartalmazza, amelyeket a Bing a lekérdezéshez kapcsolódónak ítélt. A listában minden egyes [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) objektum tartalmazza többek között a videó URL-címét, hosszát, méreteit és kódolási formátumát. A videóobjektum a videó miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Videóminiatűrök

Megjelenítheti az összes, vagy a Bing Video Search API által visszaadott a videóminiatűrök egy részét. Ha csak a miniatűrök egy részét jeleníti meg, biztosítson lehetőséget a felhasználónak a többi videó megtekintésére. a Bing API részeként [használja, és megjelenítési követelményeihez](../UseAndDisplayRequirements.md), meg kell jelenítenie a videók a válaszban megadott sorrendben. A miniatűrök átméretezésével kapcsolatos információkért lásd a [miniatűrök átméretezésével és körülvágásával](../resize-and-crop-thumbnails.md) foglalkozó szakaszt. 

A [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) használatával beállíthatja, hogy ha a felhasználó a miniatűr fölé viszi a mutatót, akkor elinduljon a videó miniatűr verziójának lejátszása. Ügyeljen arra, hogy megjelenítse a mozgó miniatűr forrását.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

A miniatűr kattintáskor három lehetőség áll rendelkezésre a videó megtekintése:

- [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) használata esetén a videó az eredeti webhelyen (például a YouTube-on) tekinthető meg;
- [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) használata esetén a videó a Bing videóböngészőjében tekinthető meg;
- [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) használata esetén a videót saját felületére ágyazza be. 

Ügyeljen arra, hogy lejátszáskor a videóban megjelenítse annak közzétevőjét és létrehozóját.

A [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) a videóhoz kapcsolódó információk beszerzésére vonatkozó használatáról bővebben a [videóelemzésekkel](../video-insights.md) foglalkozó részben olvashat.

## <a name="filtering-videos"></a>Videók szűrése

Alapértelmezés szerint a Video Search API a lekérdezéshez kapcsolódó összes videót visszaadja. Ha csak ingyenes vagy öt percnél rövidebb videókat szeretne visszakapni, a következő szűrőlekérdezési paramétereket használhatja:

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash;Videók szűrése ár alapján (például ingyenes vagy fizetős videók);
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash;Videók szűrése felbontás alapján (például 720p vagy nagyobb felbontású videók);
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash;Videók szűrése hossz alapján (például öt percnél rövidebb videók);
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash;Videók szűrése a feltöltés időpontja alapján (például a Bing által az elmúlt héten felfedezett videók).

Ha egy konkrét tartományban található videókat szeretne lekérni, foglalja bele a [site:](https://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort a lekérdezési sztringbe.

> [!NOTE]
> A lekérdezéstől függően, ha a `site:` lekérdezési operátort használja, előfordulhat, hogy a válasz a [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch) beállítás ellenére is felnőtteknek szóló tartalmakat ad vissza. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.

A következő példa azt mutatja be, hogyan kérhet le olyan ingyenes videókat a ContosoSailing.com webhelyről, amelyek 720p vagy nagyobb felbontással rendelkeznek, és amelyeket a Bing az elmúlt hónapban fedezett fel.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>A lekérdezés bővítése

Ha a Bing ki tudja bővíteni a lekérdezést az eredeti keresés szűkítéséhez, a [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) objektumban szerepelni fog a `queryExpansions` mező. Például, ha a lekérdezés a(z) *tisztítás ekkor*, lehet, hogy a kibontott lekérdezéseket: Kötésmargó tisztítás **eszközök**, ekkor tisztítás **egészen az alapoktól**, kötésmargó tisztítás **gép**, és **könnyen** kötésmargó tisztítás.

Az alábbi példában a *Cleaning Gutters* bővített lekérdezései láthatók.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

A `queryExpansions` mező a [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) objektumok listáját tartalmazza. A `text` mezőben a bővített lekérdezés, a `displayText` mezőben pedig a bővítési kifejezés található. A szöveg és a miniatűr mezőben megjelenítheti a bővített lekérdezési sztringeket arra az esetre, ha a felhasználó valójában az egyik bővített lekérdezési sztringet keresné. A miniatűrt és a szöveget a `webSearchUrl` URL vagy a `searchLink` URL használatával kattinthatóvá teheti. Akkor használja a `webSearchUrl` lehetőséget, ha a felhasználót a Bing keresési találatait szeretné küldeni, a `searchLink` lehetőséget pedig akkor, ha saját eredményoldalt használ.

## <a name="pivoting-the-query"></a>A lekérdezés szegmentálása

Ha a Bing szegmensekre tudja bontani az eredeti keresési lekérdezést, a [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) objektumban szerepelni fog a `pivotSuggestions` mező. Ha például az eredeti lekérdezés a *Cleaning Gutters* volt, a Bing a kifejezést *Cleaning* és *Gutters* részekre bonthatja.

Az alábbi példában a *Cleaning Gutters* keresési kifejezés szegmentálási javaslatai láthatók.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

A válasz minden szegmenshez kínál olyan [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) objektumokat, amelyek a javasolt lekérdezéseket tartalmazzák. A `text` mező a javasolt lekérdezést, a `displayText` mező pedig az eredeti lekérdezésben a szegmenst lecserélő kifejezést tartalmazza. Például: Window Cleaning.

A `text` és a `thumbnail` mezőben megjelenítheti a bővített lekérdezési sztringeket arra az esetre, ha a felhasználó valójában az egyik bővített lekérdezési sztringet keresné. A miniatűrt és a szöveget a `webSearchUrl` URL vagy a `searchLink` URL használatával kattinthatóvá teheti. Akkor használja a `webSearchUrl` lehetőséget, ha a felhasználót a Bing keresési találatait szeretné küldeni, a `searchLink` lehetőséget pedig akkor, ha saját eredményoldalt használ.

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
