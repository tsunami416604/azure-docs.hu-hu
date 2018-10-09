---
title: Mi a Válaszkeresés projekt?
titlesuffix: Azure Cognitive Services
description: A projekt válaszkeresés bemutatása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 87fe7b008e3e7c6cd8d1a9a870c0fb8ce2f6a7cd
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868255"
---
# <a name="what-is-project-answer-search"></a>Mi a Válaszkeresés projekt?
Projekt válasz Search API a Bing v7-végpont használatával interrogative lekérdezésekre adott válaszok. A kérdés például a "Mi az a föld kerület?" adatok választ adja vissza.  Egy-egy személyt, hely vagy egy dolog lekérdezést a lekérdezés által azonosított entitás adatait adja vissza. Ezeket a forgatókönyveket akkor lehet hasznos az alkalmazások, például a természetes nyelvi robotokat üzenetküldő alkalmazások, az olvasók, stb.  

Lekérdezések vissza válaszok, amelyek lekérdezési forgatókönyvtől függ: hagyása minden esetben vannak ad vissza, miközben [tények](fact-queries.md) és/vagy [entitások](entity-queries.md) adott adja vissza.

## <a name="endpoint"></a>Végpont
Válaszok a a kérdés vagy információ egy személy, hely vagy egy dolog, hogy a válasz keresési API-végpont egy kérés küldése. A fejlécek és URL-paraméterek használata a különböző előírásoknak.  Például *Ocp-Apim-Subscription-Key* érvényes token fejléc.  A piaci paraméter megadása kötelező. Csak `en-us` piaci jelenleg támogatott.

Az alábbi lekérdezés lekérdezi a kérdésekre adott válaszok: "Mi az a föld kerület?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Az URL-cím paraméter `q=` keresés Objekt megadása szükséges.

## <a name="response-object"></a>Válaszobjektum

A válasz HTTP fejlécek, weblapjait, tényeket, illetve entitásokat tartalmazza.

````
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

````

## <a name="terms-of-use"></a>Használati feltételek
Válaszkeresés projekt és a projekt videó trendeket is szabályozza a [követelmények a Bing Search használata és megjelenítése](use-display-requirements.md).

Vagy egy harmadik féltől származó, az Ön nevében, előfordulhat, hogy nem használja, megőrzése, tárolásához, gyorsítótár, oszt meg, vagy a tesztelés, fejlesztés, képzés, terjesztése vagy minden nem Microsoft-szolgáltatás elérhetővé tétele céljából, az URL-cím előzetes API-ból adatokat terjeszteni vagy szolgáltatás. 

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Adatokat tesznek elérhetővé; ilyenek.  

Projekt válaszkeresés válaszok harmadik fél által birtokolt információkat tartalmaznak. Ön felelőssége annak biztosítása érdekében, használatára megfelelő, például úgy, hogy megfelel a creative commons licencet a felhasználói élmény támaszkodhat.  
  
Ha egy válasz vagy az eredmény tartalmazza a `contractualRules`, `attributions`, vagy `provider` mezők, meg kell attribútum az adatokat. Ha a válasz nem tartalmazza a mezőt, nem tesznek elérhetővé; ilyenek nem szükséges. Ha az eredmény tartalmazza a `contractualRules` mezőt és a `attributions` és/vagy `provider` mezőket, szerződéses szabályok segítségével attribútum az adatokat.  
  
Az alábbi példa bemutatja egy olyan entitás, amely tartalmaz egy MediaAttribution szerződéses szabály és a egy rendszerképet, amely tartalmazza a `provider` mező. A MediaAttribution szabály azonosítja a kép a szabály céljaként, így lenne, figyelmen kívül a lemezkép `provider` mező, ezért használja inkább a MediaAttribution szabály tesznek elérhetővé; ilyenek biztosít.  
  
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
  
Ha egy szerződéses szabályt tartalmaz a `targetPropertyName` mező, a szabály vonatkozik csak a célként megadott mezőben. Ellenkező esetben a szabály vonatkozik-e a szülői objektumot, amely tartalmazza a `contractualRules` mező.  
  
  
A következő példában a `LinkAttribution` szabály tartalmazza a `targetPropertyName` mező, ezért a szabály vonatkozik a `description` mező. Bizonyos mezők alkalmazó szabályok meg kell adnia egy követő a meghatározott adatok a szolgáltató webhelyéről mutató hivatkozást tartalmazó sor. Az attribútum a leírást, például egy vonal azonnal ebben az esetben a leíró szöveg, amely tartalmaz egy hivatkozást az adatok a szolgáltató webhelyéről, a következő létrehozása en.wikipedia.org mutató hivatkozást.  
  
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

### <a name="license-attribution"></a>Licenc megnevezése  

Ha szerződéses szabályok listája tartalmaz egy [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) szabály, meg kell jelenítenie a hirdetmény a sor következett közvetlenül a tartalmat, amely a licenc vonatkozik. A `LicenseAttribution` szabályt használ a `targetPropertyName` mező segítségével azonosítja a tulajdonságot, amely a licenc vonatkozik.  
  
A következő látható egy példa, amely magában foglalja egy `LicenseAttribution` szabály.  
  
![Licenc megnevezése](./media/licenseattribution.png)  
  
A licenc figyelje meg, hogy jelenítjük meg azt a webhelyet, a licenc információkat tartalmaz egy hivatkozást kell tartalmaznia. Általában akkor győződjön meg arról, a licenc neve hivatkozás. Például, ha az értesítés **CC biztonsági Társítás licenc szöveg** és CC biztonsági Társítás a licenc neve, akkor biztosítja, CC biztonsági Társítás hivatkozás.  
  
### <a name="link-and-text-attribution"></a>Hivatkozásra, és a szöveg megnevezése  

A [LinkAttribution](reference.md#linkattribution) és [TextAttribution](reference.md#textattribution) szabályok általában az adatok a szolgáltató azonosítására szolgál. A `targetPropertyName` mező azonosítja a mezőt, amely a szabály vonatkozik.  
  
Az attribútum a szolgáltatók, vegyen fel egy közvetlenül követő tartalmat a alkalmazni az adatok (például a megcélzott mezőhöz). A sor egyértelműen feliratú jelzi, hogy a szolgáltatók a következők: az adatok forrását. Például "adatait: en.wikipedia.org". A `LinkAttribution` szabályok, létre kell hoznia egy hivatkozást a szolgáltató webhelyéről.  
  
Az alábbi példa, amely tartalmazza az `LinkAttribution` és `TextAttribution` szabályokat.  
  
![Hivatkozás szövege megnevezése](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Media megnevezése  

Ha az entitás tartalmaz egy képet, és megjeleníti azt, meg kell adnia a szolgáltató webhelyéről átkattintásos mutat. Ha az entitás tartalmaz egy [MediaAttribution](reference.md#mediaattribution) szabály, használja a szabály URL-címet az átkattintásos hivatkozást szeretne létrehozni. Ellenkező esetben használja az URL-címet, a lemezkép részeként történő `provider` mező az átkattintásos hivatkozást szeretne létrehozni.  
  
A következő látható egy példa, amely tartalmaz egy képet `provider` mező, és szerződéses szabályokat. Mivel a példa tartalmazza az általános szabály, akkor figyelmen kívül a lemezkép `provider` mezőben, majd a alkalmazni a `MediaAttribution` szabály.  
  
![Media megnevezése](./media/mediaattribution.png)  

## <a name="next-steps"></a>További lépések
- [C# gyorsútmutató](c-sharp-quickstart.md)
- [Java a rövid útmutató](java-quickstart.md)
- [Csomópont a rövid útmutató](node-quickstart.md)
- [Python a rövid útmutató](python-quickstart.md)
