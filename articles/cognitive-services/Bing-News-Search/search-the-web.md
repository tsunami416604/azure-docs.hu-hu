---
title: Mi a Bing News Search? | Microsoft Docs
description: Arra vonatkozó tudnivalók, hogyan kereshet híreket az interneten a Bing News Search API-val.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 4B35B035-34FB-403A-9F52-6470AF726FB6
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: overview
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 977b2e10c8a2ceccc5a6ffb3f396e6721afe1816
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096989"
---
# <a name="what-is-bing-news-search"></a>Mi a Bing News Search?

A Bing News Search API a [Bing Hírekhez](https://www.bing.com/news) hasonló (de nem pontosan ugyanolyan) élményt nyújt. A Bing News Search API lehetővé teszi, hogy keresési lekérdezést küldjön a Bingnek, és egy ahhoz kapcsolódó híreket tartalmazó listát kapjon vissza.

Ha keresési eredmények csak híreket tartalmazó oldalának létrehozásán dolgozik, amely a felhasználó keresési lekérdezéséhez kapcsolódó híreket keres, ezt az API-t hívja meg az általánosabb [Bing Web Search API](../bing-web-search/search-the-web.md) helyett. Ha híreket és egyéb típusú tartalmakat, például weboldalakat, képeket és videókat is szeretne keresni, akkor a Bing Web Search API-t hívja meg.

## <a name="suggesting--using-search-terms"></a>Keresési kifejezések javaslása és használata

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja a keresőkifejezést, kódolja azt URL-címként a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query) lekérdezési paraméter beállítása előtt. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

## <a name="getting-general-news"></a>Általános hírek lekérése

A felhasználó keresőkifejezéséhez kapcsolódó, az interneten megtalálható általános hírek lekéréséhez küldje el a következő GET kérést:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához.

Ha egy konkrét tartományban található híreket szeretne lekérni, használja a [site:](http://msdn.microsoft.com/library/ff795613.aspx) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Az alábbiakban az előző lekérdezésre adott válasz látható. Az egyes cikkeket a válaszban kapott sorrendben kell megjelenítenie. Ha a cikkhez fürtözött cikkek tartoznak, jelezze, hogy vannak kapcsolódó cikkek, és jelenítse meg őket, ha a felhasználó ezt kéri.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

A [news](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#news) válasz listázza a cikkeket, amelyeket a Bing a lekérdezéshez kapcsolódónak ítélt. A `totalEstimatedMatches` mező az összes megtekinthető cikk becsült számát adja meg. További információért a cikkek lapozásáról olvassa el a [hírek lapozását](./paging-news.md) ismertető cikket.

A lista minden [cikke](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#newsarticle) tartalmazza a cikk nevét, leírását és a cikket tartalmazó eredeti webhely URL-címét. Ha a cikk tartalmaz képet, az objektum tartalmazza a kép miniatűrjét. Használja a `name` és az `url` mezőket egy hivatkozás létrehozásához, amely átirányítja a felhasználót a cikkhez az eredeti webhelyen. Ha a cikk tartalmaz képet, tegye a képet kattinthatóvá az `url` használatával. Ügyeljen arra, hogy megjelenítse a cikk forrását a `provider` használatával.

Ha a Bing meg tudja határozni a cikk kategóriáját, a cikk tartalmazza a `category` mezőt.

## <a name="getting-todays-top-news"></a>A nap legfrissebb híreinek lekérése

A nap legfrissebb híreinek lekéréséhez ugyanazt a kérést kell végrehajtania, mint az általános hírek lekérésénél, azzal a különbséggel, hogy a `q` mezőt nem kell beállítania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A legfrissebb hírek lekérésére adott válasz majdnem ugyanolyan, mint az általános hírek lekérésénél. A `news` válasz azonban nem tartalmazza a `totalEstimatedMatches` mezőt, mivel a találatok száma meg van határozva. A legfrissebb hírek cikkeinek száma eltérő lehet a hírciklustól függően. Ügyeljen arra, hogy megjelenítse a cikk forrását a `provider` használatával.

## <a name="getting-news-by-category"></a>Hírek lekérése kategóriák szerint

A hírek kategóriák szerinti (például a legfrissebb, sporttal vagy szórakozással kapcsolatos cikkek) lekéréséhez küldje el a Bingnek a következő GET kérést:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) lekérdezési paraméter használatával adja meg a lekérendő cikkek kategóriáját. A lehetséges megadható hírkategóriák listájáért tekintse meg a [Hírkategóriák piac szerint](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-categories-by-market) című részt.

A hírek kategóriák szerinti lekérésére adott válasz majdnem ugyanolyan, mint az általános hírek lekérésénél. A cikkek azonban mind a megadott kategóriába tartoznak.

## <a name="getting-headline-news"></a>Fő hírek lekérése

A fő hírek az összes hírkategóriából való lekéréséhez küldje el a Bingnek a következő GET kérést:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) lekérdezési paramétert ne használja.

A fő hírek lekérésére adott válasz majdnem ugyanolyan, mint a legfrissebb hírek lekérésénél. Ha a cikk egy vezércikk, a `headline` mező **true** értékre van állítva.

Alapértelmezés szerint a válasz legfeljebb 12 vezércikket tartalmaz. A visszaadandó vezércikkek számának módosításához adja meg a [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headlinecount) lekérdezési paramétert. A válasz hírkategóriánként legfeljebb négy olyan cikket is tartalmaz, amely nem vezércikk.

A válasz a fürtöket egy cikknek veszi. Mivel egy fürtbe számos cikk tartozhat, a válasz kategóriánként 12-nél több vezércikket és négynél több nem vezércikket is tartalmazhat.

## <a name="getting-trending-news"></a>Népszerű hírek lekérése

A közösségi hálózatokon népszerű hírtémakörök lekéréséhez küldje el a Bingnek a következő GET kérést:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> A Népszerű témakörök csak az amerikai (en-US) és a kínai (zh-CN) piacon érhetők el.

Az alábbi JSON az előző kérésre adott választ mutatja. Minden népszerű hír tartalmaz egy kapcsolódó képet, egy friss hírek jelzőt és egy URL-címet, amely a cikkhez tartozó Bing keresési eredményekre mutat. Használja a `webSearchUrl` mezőben lévő URL-címet a felhasználó a Bing keresési eredményeinek oldalára való átirányításához. Használhatja a lekérdezési szöveget is a [Web Search API](../bing-web-search/search-the-web.md) meghívásához és a találatok megjelenítéséhez.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Kapcsolódó hírek lekérése

Ha egyéb cikkek is kapcsolódnak egy hírhez, a hír tartalmazhatja a [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle-clusteredarticles) mezőt. Az alábbiakban egy fürtözött cikkekkel rendelkező cikk látható.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérelem létrehozásának, olvassa el az [első kérelem létrehozását bemutató](./quickstart.md) cikket.

Ismerkedjen meg a [Bing News Search API v7] (https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) referenciájával. A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

A keresőmező felhasználói élményének fejlesztésére vonatkozó további tudnivalókért tekintse át a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) ismertetését. Ahogy a felhasználó megadja a lekérdezési kifejezést, meghívhatja ezt az API-t, hogy mások által használt kapcsolódó lekérdezési kifejezéseket jelenítsen meg.

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./useanddisplayrequirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.