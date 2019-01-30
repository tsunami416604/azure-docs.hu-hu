---
title: Mi a Válaszkeresés projekt?
titlesuffix: Azure Cognitive Services
description: A Válaszkeresés projekt bemutatása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: cb02c9067e4d672b0aace4caf13e4c8f0d718afb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220195"
---
# <a name="what-is-project-answer-search"></a>Mi a Válaszkeresés projekt?
A Project Answer Search API a Bing v7 végpontot használja a válaszokat váró lekérdezések megválaszolására. Például a „Mekkora a Föld kerülete?” kérdésre a rendszer tényekkel válaszol.  A személyekkel, helyekkel vagy dolgokkal kapcsolatos lekérdezések a lekérdezésben azonosított entitásokkal kapcsolatos információkat adnak vissza. Ezek a forgatókönyvek olyan alkalmazások esetén lehetnek hasznosnak, amilyenek a csevegőrobotok, az üzenőalkalmazások, az olvasók stb.  

A lekérdezések a lekérdezési forgatókönyvtől függő válaszokat adnak vissza: webhelyeket mindig visszaadnak, [tényeket](fact-queries.md) és/vagy [entitásokat](entity-queries.md) pedig csak akkor, ha azok relevánsak.

## <a name="endpoint"></a>Végpont
A személyekkel, helyekkel vagy dolgokkal kapcsolatos információk lekéréséhez küldjön egy kérést az Answer Search API-végpontra. A különféle specifikációkat a fejlécekkel és az URL-paraméterekkel adhatja meg.  Szerepeltesse az *Ocp-Apim-Subscription-Key* fejlécet egy érvényes jogkivonattal.  A piac paraméter megadása kötelező. Jelenleg csak az `en-us` piac támogatott.

Az alábbi lekérdezés lekérdezi a kérdésekre adott válaszok: "Mi az a föld kerület?"

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

A keresés tárgyának meghatározásához a `q=` URL-paramétert meg kell adni.

## <a name="response-object"></a>Válaszobjektum

A válasz HTTP-fejléceket, weblapokat, illetve tényeket és/vagy entitásokat tartalmaz.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Használati feltételek
A Válaszkeresés és a Videótrendek projekt használatára [a Bing Search használati és megjelenítési követelményei vonatkoznak](use-display-requirements.md).

Ön vagy az Ön nevében eljáró harmadik fél nem használhatja, őrizheti meg, tárolhatja, gyorsítótárazhatja, oszthatja meg vagy terjesztheti az URL Preview API-ról szerzett adatokat tesztelés, fejlesztés, képzés, terjesztés vagy bármely nem Microsoft szolgáltatás vagy funkció elérhetővé tétele céljából. 

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Adatok forrásmegjelölése  

A Válaszkeresés projekt által adott válaszok külső felek tulajdonában álló információkat tartalmaznak. Az Ön felelőssége az információk megfelelő használatának biztosítása, például azzal, hogy megfelel a felhasználói felületeihez és folyamataihoz igénybe vett Creative Commons-licencek feltételeinek.  
  
Ha valamely válasz vagy eredmény tartalmazza az `contractualRules`, `attributions` vagy `provider` mezőket, az adatok forrását meg kell jelölnie. Ha a válasz egyiket sem tartalmazza, nem kell megjelölni a forrást. Ha a válasz a `contractualRules` mezőt, valamint az `attributions` és/vagy a `provider` mezőt tartalmazza, az adatok forrásának megjelöléséhez a szerződéses szabályokat kell alkalmaznia.  
  
A következő példa bemutat egy entitást, amely tartalmazza a MediaAttribution (médiatartalmak forrásmegjelölésére vonatkozó) szerződéses szabályt, valamint egy képet, amely egy `provider` mezőt tartalmaz. A MediaAttribution szabály megállapítja, hogy a szabály a képre is vonatkozik, így a kép `provider` mezője figyelmen kívül hagyható, és helyette a MediaAttribution szabály alkalmazható a forrás megjelöléséhez.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Ha egy szerződéses szabály tartalmazza a `targetPropertyName` mezőt, a szabály csak az érintett mezőkre vonatkozik. Ha nem, akkor a szabály a `contractualRules` mezőt tartalmazó szülőobjektumra vonatkozik.  
  
  
A következő példában a `LinkAttribution` szabály tartalmazza a `targetPropertyName` mezőt, így a szabály a `description` mezőre vonatkozik. Az egyes mezőkre vonatkozó szabályoknál be kell szúrnia egy sort közvetlenül a célzott adatok után, amely a szolgáltató webhelyére mutató hivatkozást tartalmaz. A leírás forrásának megjelöléséhez például szúrjon be egy sort közvetlenül a leírás szövege után a szolgáltató webhelyére mutató hivatkozással (esetünkben ez az en.wikipedia.org).  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Licencek forrásmegjelölése  

Ha a szerződéses szabályok listája tartalmaz egy [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) szabályt, a licencre vonatkozó tájékoztatást közvetlenül azután a tartalom után kell megjeleníteni, amelyre a licenc vonatkozik. A `LicenseAttribution` szabály a `targetPropertyName` mező segítségével azonosítja a tulajdonságot, amelyre a licenc vonatkozik.  
  
Az alábbi példa tartalmaz egy `LicenseAttribution` szabályt.  
  
![Licencek forrásmegjelölése](./media/licenseattribution.png)  
  
A megjelenített licencadatok között kell lennie egy hivatkozásnak, amely a licenc információit tartalmazó webhelyre mutat. Általában a licenc nevét szokás hivatkozásként szerepeltetni. Például ha a tájékoztatás így szól: **A szövegre a CC-BY-SA licenc vonatkozik**, és a CC-BY-SA a licenc neve, a CC-BY-SA nevet érdemes hivatkozássá tenni.  
  
### <a name="link-and-text-attribution"></a>Hivatkozások és szövegek forrásmegjelölése  

A [LinkAttribution](reference.md#linkattribution) és [TextAttribution](reference.md#textattribution) szabályokat általában az adatszolgáltató azonosítására alkalmazzuk. A `targetPropertyName` mező azonosítja a mezőt, amelyre a szabály vonatkozik.  
  
A szolgáltató megjelölése érdekében szúrjon be egy sort közvetlenül a tartalom után, amelyre a forrásmegjelölés vonatkozik (például a célmező után). A sorban egyértelműen jelezni kell, hogy a szolgáltató az adatok forrása. Egy erre alkalmas megfogalmazás például a következő: „Az adatok forrása: en.wikipedia.org”. A `LinkAttribution` szabályoknál létre kell hoznia egy hivatkozást, amely a szolgáltató webhelyére mutat.  
  
Az alábbi példa `LinkAttribution` és `TextAttribution` szabályokat is tartalmaz.  
  
![Hivatkozások és szövegek forrásmegjelölése](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Médiatartalmak forrásmegjelölése  

Ha az entitás képet tartalmaz, amelyet megjelenít, egy, a szolgáltató webhelyére mutató átkattintásos hivatkozást kell beszúrnia. Ha az entitás tartalmaz egy [MediaAttribution](reference.md#mediaattribution) szabályt, a szabály URL-címe alapján kell létrehozni az átkattintásos hivatkozást. Ha nem tartalmaz, használja a kép `provider` mezőjében lévő URL-címet.  
  
Az alábbi példa egy kép `provider` mezőjét és szerződéses szabályait mutatja be. Mivel a példa tartalmazza a szerződéses szabályt, a kép `provider` mezőjét figyelmen kívül kell hagyni, és a `MediaAttribution` szabályt kell alkalmazni.  
  
![Médiatartalmak forrásmegjelölése](./media/mediaattribution.png)  

## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)
