---
title: Mi a Bing Video Search?
titlesuffix: Azure Cognitive Services
description: Arra vonatkozó tudnivalók, hogyan kereshet videókat az interneten a Bing Video Search API-val.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: e48a0a056628e0c863330de792f8edfaa48aae34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261859"
---
# <a name="what-is-bing-video-search"></a>Mi a Bing Video Search?

A Bing Video Search API a [Bing Videókhoz](https://www.bing.com/videos) hasonló (de nem pontosan ugyanolyan) élményt nyújt. A Bing Video Search API lehetővé teszi, hogy keresési lekérdezést küldjön a Bingnek, és a keresés eredményeként a kapcsolódó videókat tartalmazó listát kapja vissza.

Ha keresési eredményeknek egy csak videókat tartalmazó oldalának létrehozásán dolgozik, amely a felhasználó keresési lekérdezéséhez kapcsolódó videókat keres, ezt az API-t hívja meg az általánosabb [Bing Web Search API](../bing-web-search/search-the-web.md) helyett. Ha videókat és egyéb típusú tartalmakat, például weboldalakat, híreket és képeket is szeretne keresni, akkor a Bing Web Search API-t hívja meg.

## <a name="suggesting--using-search-terms"></a>Keresési kifejezések javaslása és használata

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja a keresőkifejezést, kódolja azt URL-címként a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) lekérdezési paraméter beállítása előtt. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

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

Megjelenítheti az összes miniatűr kollázsát vagy a miniatűrök egy részét is. Ha csak a miniatűrök egy részét jeleníti meg, biztosítson lehetőséget a felhasználónak a többi videó megtekintésére. A videókat a válaszban kapott sorrendben kell megjelenítenie. A miniatűrök átméretezésével kapcsolatos információkért lásd a [miniatűrök átméretezésével és körülvágásával](./resize-and-crop-thumbnails.md) foglalkozó szakaszt. 

A [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) használatával beállíthatja, hogy ha a felhasználó a miniatűr fölé viszi a mutatót, akkor elinduljon a videó miniatűr verziójának lejátszása. Ügyeljen arra, hogy megjelenítse a mozgó miniatűr forrását.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a következő videómegtekintési lehetőségek állnak rendelkezésre:

- [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) használata esetén a videó az eredeti webhelyen (például a YouTube-on) tekinthető meg;
- [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) használata esetén a videó a Bing videóböngészőjében tekinthető meg;
- [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) használata esetén a videót saját felületére ágyazza be. 

Ügyeljen arra, hogy lejátszáskor a videóban megjelenítse annak közzétevőjét és létrehozóját.

A [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) a videóhoz kapcsolódó információk beszerzésére vonatkozó használatáról bővebben a [videóelemzésekkel](./video-insights.md) foglalkozó részben olvashat.

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

Ha a Bing ki tudja bővíteni a lekérdezést az eredeti keresés szűkítéséhez, a [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) objektumban szerepelni fog a `queryExpansions` mező. Ha például az eredeti lekérdezés a *Cleaning Gutters* volt, a bővített lekérdezések a következők lehetnek: Gutter Cleaning **Tools**, Cleaning Gutters **From the Ground**, Gutter Cleaning **Machine** és **Easy** Gutter Cleaning.

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérelem létrehozásának, olvassa el az [első kérelem létrehozását bemutató](./quick-start.md) cikket.

Az előfizetői azonosító beszerzéséről az [előfizetői azonosítókkal foglalkozó részben](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api) olvashat.

Ismerkedjen meg a [Bing Video Search API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) referenciáival. A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne. 

A keresőmező felhasználói élményének fejlesztésére vonatkozó további tudnivalókért tekintse át a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) ismertetését. Ahogy a felhasználó megadja a lekérdezési kifejezést, meghívhatja ezt az API-t, hogy mások által használt kapcsolódó lekérdezési kifejezéseket jelenítsen meg.

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./useanddisplayrequirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.

Amikor meghívja a Video Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. A válasz `totalEstimatedMatches` mezője az összes megtekinthető videó becsült számát adja meg. A videók közötti lapozásról a [videók lapozását](./paging-videos.md) ismertető témakör tartalmaz további információkat.

A videókhoz kapcsolódó információk beszerzéséről bővebben a [videóelemzésekkel](./video-insights.md) foglalkozó részben olvashat.

A népszerű videók beszerzéséről bővebben a [népszerű videókkal](./trending-videos.md) foglalkozó részben olvashat.