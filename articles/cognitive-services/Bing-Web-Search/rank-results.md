---
title: Használja a prioritás a webes válaszok megjelenítendő |} Microsoft Docs
description: Bemutatja, hogyan prioritás használatával megjelenítheti a kérdésekre adott válaszokat, amely a webes Bing keresési API adja vissza.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 750146f3bb28b94594a71733b68f092880360c5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348998"
---
# <a name="using-ranking-to-display-results"></a>Eredmények megjelenítéséhez használja a prioritása  

Minden keresési válasz tartalmazza a [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogy meg kell jelenítenie a keresési eredmények között. A rangsorolási válasz csoportosítja az eredményeket által mainline tartalmat és oldalsávon tartalmat a hagyományos keresés eredményeit tartalmazó lap. Ha nem jelenik meg az eredményeket egy hagyományos mainline és oldalsávon formátum, mint az oldalsó sáv mainline tartalom magasabb látható kell megadnia.  
  
Az egyes csoportok (mainline vagy oldalsávon), a [elemek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tömb azonosítja a megfelelő szerepelnie kell a tartalmát. Minden elem azonosításához az eredmény belül választ az alábbi két módon biztosít.  
  
-   `answerType` és `resultIndex` – a `answerType` mező azonosítja a válasz (például weblap vagy News) és `resultIndex` belül a válasz (például egy hírek cikk) eredményeként azonosítja. Az index értéke nulla alapú.  
  
-   `value` – A `value` mezőben egy megfelelő választ vagy a válasz belül eredményeként az azonosítója. A válasz vagy az eredményeket az azonosítója, de nem mindkettőt tartalmaz.  
  
Az azonosító használata egyszerűbb csak egyeznie kell a rangsorolási ID azonosítójú választ vagy annak egyik az eredményeket, mert használni. Ha egy válasz objektum tartalmaz egy `id` mezőben, a válasz eredmények együttes megjelenítése. Például ha a `News` objektum tartalmazza a `id` mezőbe a hírcikkeket együttes megjelenítése. Ha a `News` objektum nem tartalmazza a `id` mezőben, majd minden egyes hírek a cikk egy `id` mező és rangsorolási válasza a hírcikkeket kever egyéb válaszokat eredményeinek.  
  
Használja a `answerType` és `resultIndex` egy kicsit bonyolultabb. Használhat `answerType` azonosítására a választ, amely tartalmazza az eredmények megjelenítéséhez. Ezután `resultIndex` indexhez keresztül a válasz eredmények megjeleníthető eredmény. (A `answerType` mezőjének neve a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektum.) Ha a válasz eredmények együttes megjelenítése elvárt befejezési rangsorolási válasz elem nem tartalmazza a a `resultIndex` mező.  

## <a name="ranking-response-example"></a>Prioritás válasz – példa

A következő példáját mutatja be [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). A Webes válasz nem tartalmazza egy `id` mező, akkor jeleníti meg, külön-külön alapján a rangsorolási összes weblapok (minden weblap tartalmaz egy `id` mező). És a képek, videókat és kapcsolódó keresések válaszokat tartalmazza a `id` mező, akkor jeleníti meg, ezek a válaszok együtt a rangsorolási alapján eredményeit.
  
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
    },  
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
  
Ez rangsorolási a válasz alapján, a mainline jeleníti meg a következő keresési eredmények között:  
  
-   Az első képernyőn látható weblapon eredmény 
-   A lemezképek  
-   A második és harmadik weblap eredmények  
-   A videók  
-   A 4., 5. és 6. a weblap eredmények  
  
És az oldalsávon jeleníti meg, a következő keresési eredmények között:  
  
-   A kapcsolódó keresi  
  

## <a name="next-steps"></a>További lépések

Előléptetés unranked eredmények kapcsolatos információkért lásd: [történő előléptetésekor nem rangsora válaszok](./filter-answers.md#promoting-answers-that-are-not-ranked).

A válaszban szereplő rangsorolt válaszok száma korlátozott kapcsolatos információkért lásd: [a válaszban szereplő válaszok számának korlátozása](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Például egy C# használó prioritás eredmények megtekintése céljából, lásd: [C# rangsorolási oktatóanyag](./csharp-ranking-tutorial.md).