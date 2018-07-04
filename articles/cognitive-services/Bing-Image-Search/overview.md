---
title: Mi a Bing Image Search? | Microsoft Docs
description: Arra vonatkozó tudnivalók, hogyan kereshet az interneten képeket a Bing Images Search API használatával.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336519"
---
# <a name="what-is-bing-image-search"></a>Mi a Bing Image Search?

A Bing Image Search API a [Bing-képfolyam](https://www.bing.com/images) szolgáltatáshoz hasonló élményt nyújt, és lehetővé teszi, hogy elküldjön egy felhasználói keresési lekérdezést a Bingnek, és egy ahhoz kapcsolódó képeket tartalmazó listát kapjon vissza.

Ha keresési eredmények csak képeket tartalmazó oldalának létrehozásán dolgozik, amely a felhasználó keresési lekérdezéséhez kapcsolódó képeket keres, ezt az API-t hívja meg az általánosabb [Web Search API](../bing-web-search/search-the-web.md) helyett. Ha képeket és egyéb típusú tartalmakat, például weboldalakat, híreket és videókat is szeretne keresni, akkor a Web Search API-t hívja meg.

## <a name="suggesting--using-search-terms"></a>Keresési kifejezések javaslása és használata

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja a keresőkifejezést, kódolja URL-címként a karakterláncot a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) lekérdezési paraméter beállítása előtt. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

## <a name="getting-images"></a>Képek lekérése

A felhasználó keresőkifejezéséhez kapcsolódó, az interneten megtalálható képek lekéréséhez küldje el a következő GET kérést:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Minden kérést egy kiszolgálóról kell elküldeni. Ügyfélről nem indíthat hívásokat.

Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához.

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](http://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A válasz tartalmaz egy [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) választ, amely azoknak a képeknek a listáját tartalmazza, amelyeket a Bing a lekérdezéshez kapcsolódónak ítélt. A listában minden egyes [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objektum tartalmazza a kép URL-címét, fájlméretét, méreteit és kódolási formátumát. A képobjektum a kép miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

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

Megjelenítheti az összes miniatűr kollázsát vagy a miniatűrök egy részét is. Ha csak a miniatűrök egy részét jeleníti meg, biztosítson lehetőséget a felhasználónak a többi kép megtekintésére. A képeket a válaszban kapott sorrendben kell megjelenítenie.

A miniatűrt ki is bonthatja, ha a felhasználó fölé viszi az egérmutatót. Ügyeljen arra, hogy kibontáskor megjelenítse a kép forrását. Például beolvashatja a gazdanevet a [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) URL-címből, és megjelenítheti a kép alatt. A miniatűrök átméretezésével kapcsolatos információkért lásd a [miniatűrök átméretezésével és körülvágásával](./resize-and-crop-thumbnails.md) foglalkozó szakaszt.

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) használatával megjelenítheti neki a teljes méretű képet. Ügyeljen arra, hogy megjelenítse a kép forrását.

Ha a `shoppingSourcesCount` vagy a `recipeSourcesCount` nullánál nagyobb, adjon hozzá egy jelvényt a miniatűrhöz, például egy bevásárlókosarat, ezzel jelezve, hogy a képen látható tárgy megvásárolható, vagy tartozik hozzá egy recept.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Ha további információkat szeretne lekérni a képről, például azokat a weboldalakat, amelyek tartalmazzák, vagy azokat a személyeket, akik felismerhetők rajta, használja az [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) lehetőséget. További részletekért tekintse át a [képek elemzéseivel](./image-insights.md) foglalkozó cikket.

## <a name="filtering-images"></a>Képek szűrése

 Alapértelmezés szerint az Images Search API a lekérdezéshez kapcsolódó összes képet visszaadja. Ha azonban csak bizonyos tulajdonságú, például átlátszó hátterű vagy adott méretű képeket szeretne megjeleníteni, a következő lekérdezési paraméterekkel szűrheti a Bing által visszaadott találatokat.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) – Képek szűrése méretarány alapján (például szabványos vagy szélesvásznú képek)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) – Képek szűrése domináns szín alapján vagy fekete-fehér képek keresése
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) – Képek szűrése kor alapján (például a Bing által az elmúlt héten felfedezett képek)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) – Képek szűrése szélesség és magasság alapján
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) – Képek szűrése tartalom alapján (például csak arcokat mutató képek)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) – Képek szűrése típus alapján (például ClipArtok, animált GIF-ek vagy átlátszó hátterű képek)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) – Képek szűrése a webhelyhez tartozó licenc típusa alapján
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) – Képek szűrése méret alapján, például kis képek, legfeljebb 200x200 képpontos méretig

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](http://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

> [!NOTE]
> A lekérdezéstől függően, ha a `site:` operátort használja, előfordulhat, hogy a válasz a [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) beállítás ellenére is felnőtteknek szóló tartalmakat ad vissza. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.

A következő példa azt mutatja be, hogyan kérhet le olyan kis méretű képeket a ContosoSailing.com webhelyről, amelyeket a Bing az elmúlt héten fedezett fel.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>A lekérdezés szegmentálása

Ha a Bing szegmensekre tudja bontani az eredeti keresési lekérdezést, az [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektumban szerepelni fog a `pivotSuggestions` mező. Ha például az eredeti lekérdezés a *Microsoft Surface* volt, a Bing a kifejezést a *Microsoft* és a *Surface* részre bonthatja.  

Az alábbi példában a *Microsoft Surface* keresési kifejezés szegmentálási javaslatai láthatók.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Ezt követően megjeleníthet a felhasználónak opcionális lekérdezési kifejezéseket, amelyek érdekelhetik.

A `pivotSuggestions` mező azoknak a szegmenseknek a listáját tartalmazza, amelyekre az eredeti lekérdezést a rendszer felbontotta. A válasz minden szegmenshez kínál olyan [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) objektumokat, amelyek a javasolt lekérdezéseket tartalmazzák. A `text` mező a javasolt lekérdezést, a `displayText` mező pedig az eredeti lekérdezésben a szegmenst lecserélő kifejezést tartalmazza. Például: Release Date of Surface (Surface megjelenési dátuma).

A `text` és a `thumbnail` mezőben megjelenítheti a szegmentált lekérdezési sztringeket arra az esetre, ha a felhasználó valójában az egyik szegmentált lekérdezési sztringet keresné. A miniatűrt és a szöveget a `webSearchUrl` URL vagy a `searchLink` URL használatával kattinthatóvá teheti. Akkor használja a `webSearchUrl` lehetőséget, ha a felhasználót a Bing keresési találatait szeretné küldeni, a `searchLink` lehetőséget pedig akkor, ha saját eredményoldalt használ.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>A lekérdezés bővítése

Ha a Bing ki tudja bővíteni a lekérdezést az eredeti keresés szűkítéséhez, az [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektumban szerepelni fog a `queryExpansions` mező. Ha például a lekérdezés a *Microsoft Surface* volt, a bővített lekérdezések a következők lehetnek: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** és Microsoft Surface **Hub**.

Az alábbi példában a *Microsoft Surface* bővített lekérdezései láthatók.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

A `queryExpansions` mező a [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) objektumok listáját tartalmazza. A `text` mezőben a bővített lekérdezés, a `displayText` mezőben pedig a bővítési kifejezés található. A `text` és a `thumbnail` mezőben megjelenítheti a bővített lekérdezési sztringeket arra az esetre, ha a felhasználó valójában az egyik bővített lekérdezési sztringet keresné. A miniatűrt és a szöveget a `webSearchUrl` URL vagy a `searchLink` URL használatával kattinthatóvá teheti. Akkor használja a `webSearchUrl` lehetőséget, ha a felhasználót a Bing keresési találatait szeretné küldeni, a `searchLink` lehetőséget pedig akkor, ha saját eredményoldalt használ.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérés létrehozásának, olvassa el a [C# rövid útmutatóját](quickstarts/csharp.md).

Ismerkedjen meg a [Bing Image Search API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) referenciáival. A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

A keresőmező felhasználói élményének fejlesztésére vonatkozó további tudnivalókért tekintse át a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) ismertetését. Ahogy a felhasználó megadja a lekérdezési kifejezést, meghívhatja ezt az API-t, hogy mások által használt kapcsolódó lekérdezési kifejezéseket jelenítsen meg.

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./useanddisplayrequirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.

Amikor meghívja a Bing Image Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. A válasz `totalEstimatedMatches` mezője az összes megtekinthető kép becsült számát adja meg. A képek közötti lapozásról a [képek lapozását](./paging-images.md) ismertető témakör tartalmaz további információkat.