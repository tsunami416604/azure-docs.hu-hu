---
title: Keresési eredmények – a Bing Web Search API módosítják a rangsort használata
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ranglistán megjelenő használatával megjelenítheti a Bing Web Search API a keresési eredmények.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 9a49c4af474d7f0618bf0cff1a093e5cbb62fe2d
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295606"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Használata a Bing Web Search API-eredmények rangsorolása  

Minden keresés válasz tartalmazza a [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogyan kell a keresési eredmények megjelenítéséhez. A rangsorolás válasz csoportosítja az eredményeket a mainline tartalmat és oldalsáv tartalmat egy hagyományos keresési eredményeket megjelenítő lap esetében. Ha nem jelennek meg az eredményeket egy hagyományos által és a oldalsáv formátum, meg kell adnia által tartalom magasabb látható-e, mint az oldalsáv tartalmat.  

Minden csoportban (mainline vagy oldalsáv), a [elemek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tömb azonosítja a rendelést, szerepelnie kell a tartalmat. Minden elem azonosítására belül választ az eredmény a következő két módon biztosít.  

-   `answerType` és `resultIndex` – a `answerType` mező az eredmény (például weblapot vagy hírek) azonosítja és `resultIndex` azonosítja egy eredményt a válasz (például hír) belül. Az index nulla alapú.  

-   `value` – A `value` mező, amely a válasz vagy válasz belül eredményt azonosítója megegyezik-Azonosítót tartalmaz. Vagy a választ, vagy az eredmények tartalmazzák az azonosítója, de nem mindkettőt.  

Az azonosító használata egyszerűbb használni, mert csak egyeznie kell a rangsorolás azonosító azonosítójú, válasz vagy az eredmények közül. Ha egy válasz objektum tartalmaz egy `id` mezőben együtt a válasz eredmények megjelenítéséhez. Például ha a `News` objektum tartalmazza a `id` mezőt, és megjelenítheti az összes hírek cikk együtt. Ha a `News` objektum nem tartalmaz a `id` mezőben, majd minden egyes hír tartalmaz egy `id` mező, és a rangsorolás válasz eredményét a hírek egyéb válaszokat eredményeivel.  

Használatával a `answerType` és `resultIndex` egy kicsit bonyolultabb. Használhat `answerType` azonosíthatja a választ, amely tartalmazza az eredmények megjelenítéséhez. Ezután `resultIndex` indexbe az eredmény megjelenítéséhez a válasz eredmények keresztül. (A `answerType` a neve, a mező a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektum.) Ön kellene együtt a válasz eredményeket megjeleníteni, ha a rangsorolás válasz elem nem tartalmazza a `resultIndex` mező.  

## <a name="ranking-response-example"></a>Példa a válasz rangsorolása

Az alábbi példán látható [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). A Webes válasz nem tartalmazza egy `id` mező, akkor jeleníti meg, az összes weboldalt, külön-külön alapján a rangsorolás (minden egyes weblap tartalmaz egy `id` mezőben). És a képeket, videókat és kapcsolódó keresések válaszokat tartalmazza a `id` mező, akkor jeleníti meg, ezek a válaszok együtt alapján a rangsorolás eredményeit.

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

Ez ennek a területnek a válasz alapján, a által jeleníti meg, a következő keresési eredmények:  

-   Az első weblap eredmény
-   Az összes rendszerkép  
-   A második és harmadik weblap eredmények  
-   Az összes videóját  
-   A 4., 5. és 6. weblap eredmények  

És az oldalsáv jeleníti meg, a következő keresési eredmények:  

-   A kapcsolódó keresések  


## <a name="next-steps"></a>További lépések

Unranked eredményt előléptetése kapcsolatos információkért lásd: [választ, amely nem súlyosságon előléptetése](./filter-answers.md#promoting-answers-that-are-not-ranked).

További információ a válaszban rangsorolt válaszok számának korlátozása: [száma a válaszokat a válaszban](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

C#-példán használó ranglistán megjelenő eredményeket megjeleníteni, lásd: [C# oktatóanyag ennek a területnek](./csharp-ranking-tutorial.md).
