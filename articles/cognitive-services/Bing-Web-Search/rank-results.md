---
title: A rangsor használata a keresési eredmények megjelenítéséhez - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként jelenítheti meg a keresési eredményeket a Bing Web Search API-ból a rangsorolás segítségével.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 677f6089f649aae720a6303a7e1512e3c7ebeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390122"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>A rangsorolás használata a Bing Web Search API eredményeinek megjelenítéséhez  

Minden keresési válasz tartalmaz egy [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogyan kell megjeleníteni a keresési eredményeket. A rangsorolási válasz a találatokat a hagyományos keresési eredményoldal fővonali tartalma és oldalsávtartalma szerint csoportosítja. Ha az eredményeket nem hagyományos fővonal- és oldalsáv formátumban jeleníti meg, akkor a fővonal tartalmát jobban át kell adnia, mint az oldalsáv tartalmát.  

Az egyes csoportokon (fővonalon vagy oldalsávon) belül az [Elemek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) tömb azonosítja azt a sorrendet, amelyben a tartalomnak meg kell jelennie. Minden elem a következő két módon azonosítja az eredményt a válaszon belül.  

-   `answerType`és `resultIndex` – `answerType` A mező azonosítja a választ (például `resultIndex` weblapot vagy híreket), és a válaszban (például egy újságcikkben) azonosítja az eredményt. Az index nulla alapú.  

-   `value`— `value` A mező olyan azonosítót tartalmaz, amely megegyezik a válasz vagy a válaszon belüli eredmény azonosítójával. A válasz vagy az eredmények tartalmazzák az azonosítót, de mindkettőt nem.  

Az azonosító használata egyszerűbb, mert csak a rangsor azonosítóját kell egyeztetnie a válasz azonosítójával vagy annak egyik eredményével. Ha egy válaszobjektum `id` mezőt tartalmaz, jelenítse meg a válasz összes eredményét együtt. Ha például `News` az objektum `id` tartalmazza a mezőt, jelenítse meg az összes újságcikket együtt. Ha `News` az objektum nem `id` tartalmazza a mezőt, `id` akkor minden újságcikk tartalmaz egy mezőt, és a rangsorolási válasz összekeveri a híreket a többi válasz eredményeivel.  

Használata, `answerType` `resultIndex` és egy kicsit bonyolultabb. A `answerType` megjelenítendő eredményeket tartalmazó válasz azonosítására használható. Ezután indexelheti `resultIndex` a válasz eredményeit, hogy az eredmény megjelenjen. (Az `answerType` érték a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objektum mezőjének neve.) Ha a válasz összes eredményét együtt kell megjelenítenie, a rangsorolási `resultIndex` válasz elem nem tartalmazza a mezőt.  

## <a name="ranking-response-example"></a>Példa rangsorolásra adott válaszra

Az alábbiakban egy példa [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Mivel a webes válasz `id` nem tartalmaz mezőt, az összes weblapot külön-külön kell `id` megjeleníteni a rangsorolás alapján (minden weblap tartalmaz egy mezőt). Mivel a képekre, videókra és a `id` kapcsolódó keresésre adott válaszok tartalmazzák a mezőt, a válaszok eredményeit a rangsor alapján együtt jelenítheti meg.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

A rangsorolási válasz alapján a fővonal a következő keresési eredményeket jeleníti meg:  

-   Az első weboldal eredménye
-   Az összes kép  
-   A második és harmadik weboldal eredményei  
-   Az összes videó  
-   A 4., 5.  

És az oldalsáv a következő keresési eredményeket jeleníti meg:  

-   Az összes kapcsolódó keresés  


## <a name="next-steps"></a>További lépések

A rangsorolatlan eredmények népszerűsítéséről a [nem rangsorolt válaszok népszerűsítése című témakörben talál.](./filter-answers.md#promoting-answers-that-are-not-ranked)

A válaszban szereplő rangsorolt válaszok számának korlátozásáról a [Válaszban szereplő válaszok számának korlátozása](./filter-answers.md#limiting-the-number-of-answers-in-the-response)című témakörben talál.

Az eredmények megjelenítéséhez rangsorolást használó C# példát lásd: [C# ranking tutorial](./csharp-ranking-tutorial.md).
