---
title: Bing Web Search API válaszszerkezete és választípusai
titleSuffix: Azure Cognitive Services
description: Amikor keresési kérelmet küld a Bing `SearchResponse` Web Search szolgáltatásnak, az egy objektumot ad vissza a választörzsben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110612"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Web Search API válaszszerkezete és választípusai  

Amikor keresési kérelmet küld a Bing [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Web Search szolgáltatásnak, az egy objektumot ad vissza a választörzsben. Az objektum tartalmaz egy mezőt minden válaszhoz, amelyről a Bing megállapította, hogy releváns volt a lekérdezéshez. Ez a példa egy válaszobjektumot mutat be, ha a Bing az összes választ visszaadta:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

A Bing Web Search általában a válaszok egy részét adja vissza. Ha például a lekérdezés *kifejezése vitorlás csónakok* `webPages`volt, a válasz ban lehet a , `images`és `rankingResponse`a . Hacsak nem használta [a responseFilter-t](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) a weblapok `webpages` `rankingResponse` kiszűrésére, a válasz mindig tartalmazza a válaszokat és a válaszokat.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Weblapok válasza

A [webpages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) válasz tartalmazza azon weblapokra mutató hivatkozások listáját, amelyekről a Bing Web Search megállapította, hogy relevánsak voltak a lekérdezés szempontjából. A lista minden [weboldala](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) tartalmazni fogja: az oldal nevét, URL-címét, megjelenített URL-jét, a tartalom rövid leírását és a Bing által a tartalmat megtaláló dátumot.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Olyan `name` `url` hivatkozás t, amely a felhasználót a weblapra viszi, és létrehozhat ja.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Képek válasz

A [képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) válasz tartalmaz egy listát a képeket, hogy a Bing úgy gondolta, releváns a lekérdezést. A listában szereplő [minden kép](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) tartalmazza a kép URL-címét, méretét, méreteit és kódolási formátumát. A képobjektum a kép miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

A felhasználó eszközétől függően általában a miniatűrök egy részét jeleníti meg, és a felhasználó nak lehetősége van a fennmaradó [képeken lapozni.](paging-webpages.md)

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

A miniatűrt ki is bonthatja, ha a felhasználó fölé viszi az egérmutatót. Ügyeljen arra, hogy kibontáskor megjelenítse a kép forrását. Például úgy, hogy kivonja a gazdatestet a képből, `hostPageDisplayUrl` és megjeleníti a kép alatt. A miniatűrök átméretezésével kapcsolatos információkért lásd a [miniatűrök átméretezésével és körülvágásával](./resize-and-crop-thumbnails.md) foglalkozó szakaszt.

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Ha a felhasználó a miniatűrre kattint, vigye `webSearchUrl` a felhasználót a Bing keresési eredményoldalára a képekhez, amely a képek kollázsát tartalmazza.

A képválaszról és a képekről az [Image Search API című témakörben](../bing-image-search/search-the-web.md)talál részleteket.

## <a name="related-searches-answer"></a>Kapcsolódó keresések válasz

A [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) válasz a más felhasználók által leggyakrabban kapcsolódó lekérdezések listáját tartalmazza. A lista minden [lekérdezése](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) tartalmaz`text`egy lekérdezési karakterláncot (`displayText`mind egy`webSearchUrl`lekérdezési karakterláncot , amely lenyomja a kiemelt karaktereket ( ), és egy URL-t ( ) a Bing keresési eredménylapjához.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

A `displayText` lekérdezési karakterlánc `webSearchUrl` és az URL-cím segítségével hozzon létre egy hivatkozást, amely a felhasználót a kapcsolódó lekérdezés Bing keresési eredménylapjára viszi. Használhatja a `text` lekérdezési karakterláncot a saját Web Search API-lekérdezésében is, és saját maga is megjelenítheti az eredményeket.

A kiemelő jelölők kezeléséről a `displayText` [Leadott érték kiemelése](../bing-web-search/hit-highlighting.md)című témakörben talál további információt.

Az alábbi példa a kapcsolódó lekérdezések használatát Bing.com.

![Kapcsolódó keresések példa a Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Videók válasz

A [videók](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) válasza tartalmazza azoknak a videóknak a listáját, amelyekről a Bing úgy gondolta, hogy relevánsak a lekérdezés szempontjából. A listában szereplő minden [videó](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) tartalmazza a videó URL-címét, időtartamát, méreteit és kódolási formátumát. A videóobjektum a videó miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

A felhasználó eszközétől függően általában a videók egy részét szeretné megjeleníteni, és a felhasználó megtekintheti a többi videót. Meg kell jelenítenie a videó miniatűrjét a videó hosszával, leírásával (nevével) és hozzárendelésével (közzétevő).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Ahogy a felhasználó a miniatűr `motionThumbnailUrl` fölé viszi az egérmutatót, a videó miniatűr verziójának lejátszásához használhatja. Ügyeljen arra, hogy megjelenítse a mozgó miniatűr forrását.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a következő videómegtekintési lehetőségek állnak rendelkezésre:

- A `hostPageUrl` videó megtekintése a fogadó webhelyen (például YouTube)
- A `webSearchUrl` videó megtekintése a Bing videoböngészőben
- A `embedHtml` videó beágyazása a saját élményébe

A videóválaszról és a videókról a [Video Search API című témakörben](../bing-video-search/search-the-web.md)talál részleteket.

## <a name="news-answer"></a>Hírek válasz

A [hírválasz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) tartalmazza azon hírek listáját, amelyekről a Bing úgy vélte, hogy relevánsak a lekérdezés szempontjából. A lista minden [cikke](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) tartalmazza a cikk nevét, leírását és a cikket tartalmazó eredeti webhely URL-címét. Ha a cikk tartalmaz képet, az objektum tartalmazza a kép miniatűrjét.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

A felhasználó eszközétől függően a hírek egy részét meg kell jelenítenie, és a felhasználó megtekintheti a többi cikket. Használja a `name` és az `url` mezőket egy hivatkozás létrehozásához, amely átirányítja a felhasználót a cikkhez az eredeti webhelyen. Ha a cikk képet tartalmaz, a `url`képet a használatával kattintva teheti elérhetővé. Ügyeljen arra, hogy megjelenítse a cikk forrását a `provider` használatával.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

A hírekre adott válaszról és a hírcikkekről a [News Search API című témakörben](../bing-news-search/search-the-web.md)talál részleteket.

## <a name="computation-answer"></a>Számítási válasz

Ha a felhasználó matematikai kifejezést vagy egységkonverziós lekérdezést ad meg, a válasz [számítási](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) választ is tartalmazhat. A `computation` válasz tartalmazza a normalizált kifejezést és annak eredményét.

Az egységkonverziós lekérdezés olyan lekérdezés, amely az egyik egységet átalakítja egy másikba. Például, *Hány láb 10 méter?* vagy *Hány evőkanál egy 1/4 csésze?*

A következő `computation` mutatja a *választ, hogy hány láb 10 méter?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Az alábbi példák matematikai lekérdezésekre és a hozzájuk tartozó `computation` válaszokra mutatnak be példákat.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

A matematikai kifejezés a következő szimbólumokat tartalmazhatja:

|Szimbólum|Leírás|
|------------|-----------------|
|+|Összeadás|
|-|Kivonás|
|/|Osztás|
|*|Szorzás|
|^|Power|
|!|Faktoriális|
|.|Decimal|
|()|Elsőbbségi csoportosítás|
|[]|Függvény|

A matematikai kifejezés a következő állandókat tartalmazhatja:

|Szimbólum|Leírás|
|------------|-----------------|
|Pi|3.14159...|
|Mértékben|Mértékben|
|i|Képzetes szám|
|e|e, 2.71828...|
|GoldenRatio között|Arany arány, 1,61803...|

A matematikai kifejezés a következő függvényeket tartalmazhatja:

|Szimbólum|Leírás|
|------------|-----------------|
|Rendezés|Négyzetgyök|
|Bűn[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Kiságy[x]|Trigonometriai függvények (a radián argumentumaival)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Inverz trigonometriai függvények (radiánok eredményének adása)|
|Exp[x], E^x|Exponenciális függvény|
|Napló[x]|Természetes logaritmus|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Hiperbolikus funkciók|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Inverz hiperbolikus függvények|

A változókat tartalmazó matematikai kifejezések (például 4x+6=18, ahol x a változó) nem támogatottak.

## <a name="timezone-answer"></a>TimeZone válasz

Ha a felhasználó idő- vagy dátumlekérdezést ad meg, a válasz [tartalmazhat TimeZone](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) választ. Ez a válasz támogatja az implicit vagy explicit lekérdezéseket. Egy implicit lekérdezés, például *mikor van?*, a felhasználó tartózkodási helye alapján adja vissza a helyi időt. Explicit lekérdezés, például *Mikor van seattle-i?*, visszaadja a seattle-i(WA) helyi időt.

A `timeZone` válasz tartalmazza a hely nevét, az aktuális UTC dátumot és időt a megadott helyen, valamint az UTC eltolást. Ha a hely határa több időzónán belül van, a válasz a határon belüli összes időzóna aktuális UTC-dátumát és idejét tartalmazza. Például mivel Florida állam két időzónába esik, a válasz mindkét időzóna helyi dátumát és időpontját tartalmazza.  

Ha a lekérdezés egy állam vagy ország/régió idejét kéri, a Bing meghatározza a hely földrajzi `primaryCityTime` határán belüli elsődleges várost, és visszaadja azt a mezőben. Ha a határ több időzónát tartalmaz, a fennmaradó `otherCityTimes` időzónák a mezőben lesznek visszaadva.

A következő példa lekérdezések, amelyek a választ advissza. `timeZone`

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion válasz

Ha a Bing megállapítja, hogy a felhasználó esetleg valami mást akart keresni, a válasz tartalmaz egy [SpellSuggestions objektumot.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) Ha például a felhasználó *carlos tollat*keres, a Bing megállapíthatja, hogy a felhasználó valószínűleg Carlos Pena keresésére irányul *(a Carlos pen*mások által végzett korábbi keresések alapján). Az alábbiakban egy példa helyesírás-válasz.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Válaszfejlécek

A Bing Web Search API válaszai a következő fejléceket tartalmazhatják:

|||
|-|-|
|`X-MSEdge-ClientID`|A Bing által a felhasználóhoz rendelt egyedi azonosító|
|`BingAPIs-Market`|A kérelem teljesítéséhez használt piac|
|`BingAPIs-TraceId`|A bing API-kiszolgáló naplóbejegyzése ehhez a kéréshez (támogatás)|

Különösen fontos, hogy továbbra is az ügyfél-azonosítót, és adja vissza a későbbi kérésekkel. Ha ezt teszi, a keresés a keresési eredmények rangsorolásában a múltbeli környezetet fogja használni, és egységes felhasználói élményt biztosít.

Ha azonban javascriptből hívja meg a Bing Web Search API-t, a böngésző beépített biztonsági szolgáltatásai (CORS) megakadályozhatják a fejlécek értékeinek elérését.

A fejlécek eléréséhez cors-proxyn keresztül teheti meg a Bing Web Search API-kérelmet. Az ilyen proxyk válasza rendelkezik egy `Access-Control-Expose-Headers` fejléccel, amely engedélyezési listára teszi a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

Könnyen telepíthet CORS proxyt, hogy [oktatóalkalmazásunk](tutorial-bing-web-search-single-page-app.md) hozzáférhessen az opcionális ügyfélfejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Web Search API-végpontot a HTML-fájlban a következőre:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="response-headers-in-production"></a>Válaszfejlécek éles környezetben

Az előző válaszban leírt CORS proxy megközelítés megfelelő a fejlesztéshez, teszteléshez és tanuláshoz.

Éles környezetben kiszolgálóoldali parancsfájlt kell üzemeltetnie ugyanazon a tartományon, mint a Bing Web Search API-t használó weblap. Ennek a parancsfájlnak a weblap JavaScript-jének kérésére API-hívásokat kell kezdeményeznie, és az összes eredményt , beleértve a fejléceket is, vissza kell adnia az ügyfélnek. Mivel a két erőforrás (lap és parancsfájl) egy eredeten osztozik, a CORS nem használatos, és a speciális fejlécek a weblapon található JavaScript számára érhetők el.

Ez a megközelítés is védi az API-kulcsot a nyilvános, mivel csak a kiszolgálóoldali szkript et kell. A parancsfájl más módszerrel győződjön meg arról, hogy a kérelem engedélyezve van.

Az alábbiakban bemutatjuk, hogyan használja a Bing a helyesírási javaslatot.

![Példa a Bing helyesírási javaslatára](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>További lépések  

* Tekintse [át a kérelem szabályozási dokumentációját.](throttling-requests.md)  

## <a name="see-also"></a>Lásd még  

* [Bing Web Search API-hivatkozás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
