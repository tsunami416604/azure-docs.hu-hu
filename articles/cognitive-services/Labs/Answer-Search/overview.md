---
title: Válasz keresési áttekintés – Microsoft kognitív szolgáltatások projekt |} Microsoft Docs
description: A projekt válasz keresése bemutatása.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d87cf1390970d2c815b94bcaee7e07c19bc03cce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348695"
---
# <a name="what-is-project-answer-search"></a>Mi az a projekt válasz keresése?
Projekt válasz Search API a Bing v7 végpont használatával választ kaphat a interrogative lekérdezések. A kérdés, mint "Mi az föld kerületének?" tényleges adatokkal választ ad vissza.  A lekérdezés egy személy, hely vagy tárhelyének a lekérdezés által azonosított entitás információt ad vissza. Lehet, hogy ezek a forgatókönyvek hasznos alkalmazásokhoz, mint a conversational botok üzenetküldő alkalmazások, olvasók, stb.  

Lekérdezések vissza válaszok, amelyek a lekérdezés forgatókönyv függ: weblapok a rendszer mindig ad vissza, miközben [tények](fact-queries.md) és/vagy [entitások](entity-queries.md) vannak adott vissza, ha szükséges.

## <a name="endpoint"></a>Végpont
A válaszok a kérdés vagy információ egy személy, hely vagy dolog, a válasz keresési API-végpont kérelmet küld. Használjon különböző specifikációk a fejlécek és URL-cím paramétereket.  Tartalmaznak *Ocp-Apim-előfizetés-kulcs* fejléc a következő egy érvényes tokent.  A piacon paraméter megadása kötelező. Csak `en-us` piaci jelenleg támogatott.

Az alábbi lekérdezés lekérdezi a kérdésekre adott válaszok: "Mi az föld kerületének?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Az URL-paramétert `q=` adja meg a keresés objektum szükséges.

## <a name="response-object"></a>Válasz objektum

A válasz tartalmazza a HTTP fejléceket, weblapjait, tények, illetve entitások.

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
Projekt válasz keresése és a projekt videó trendek alá vannak vetve a [Bing keresési használatát és a megjelenített követelmények](use-display-requirements.md).

Vagy egy harmadik féltől származó az Ön nevében, előfordulhat, hogy nem használja, megőrizni, tárolásához, gyorsítótár, megosztásához vagy terjesztése a URL-cím előnézeti API tesztelése, fejleszt, betanítása, terjesztése vagy elérhetővé teszi a nem Microsoft-szolgáltatás adatokat vagy szolgáltatás. 

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Adatok attribútumára  

Projekt válasz keresési válaszok harmadik felek által birtokolt információkat tartalmaznak. Az Ön felelőssége annak biztosítása érdekében használata megfelelő, például kreatív commons licenc támaszkodhat a felhasználói élmény mellett.  
  
Ha egy válasz, vagy az eredmény tartalmazza a `contractualRules`, `attributions`, vagy `provider` mezők, az adatok kell attribútum. Ha a válasz nem tartalmazhatja a következő mezők, nem attribútumára megadása kötelező. Ha a válaszfájl tartalmazza a `contractualRules` mező és a `attributions` és/vagy `provider` mezők, kell használnia a szerződéses szabályok attribútum az adatokat.  
  
A következő példa bemutatja egy entitás, amely tartalmazza egy MediaAttribution szerződéses szabályt és egy olyanra, amely magában foglalja a `provider` mező. A MediaAttribution szabály a szabály céljaként azonosítja a lemezképet, akkor figyelmen kívül a lemezkép operációs rendszerének `provider` mezőben, és inkább a MediaAttribution szabály attribútumára biztosításához.  
  
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
  
Ha egy szerződéses szabályt tartalmaz a `targetPropertyName` mezőt, a szabály vonatkozik csak a megcélzott mező. Ellenkező esetben a szabály vonatkozik-e a szülői objektumot, amely tartalmazza a `contractualRules` mező.  
  
  
A következő példában a `LinkAttribution` szabályt tartalmaz a `targetPropertyName` mezőt, így a szabálynak az alkalmazása a `description` mező. Ez a szabály adott mezők vonatkozik meg kell adni a célként megadott adatokat, a szolgáltató webhelyéről mutató hivatkozást tartalmazó követő egy sor. Az attribútum a leírás, például a egy sor azonnal en.wikipedia.org mutató hivatkozás ebben az esetben az adatokat a szolgáltató webhelyéről, a hivatkozást tartalmazó leírásának szövege a következő létrehozásához.  
  
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

### <a name="license-attribution"></a>Licenc attribútumára  

Ha a szerződéses szabályok listáját tartalmazza egy [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) szabály, meg kell jelenítenie a hirdetmény a sor azonnal követően a tartalom, amelyre a licencszerződés vonatkozik. A `LicenseAttribution` szabályt használ a `targetPropertyName` mező azonosíthassa a tulajdonság, amelyre a licencszerződés vonatkozik.  
  
A következő szemléltet, amely tartalmazza a `LicenseAttribution` szabály.  
  
![Licenc attribútumára](./media/licenseattribution.png)  
  
A licenc hirdetmény megjelenített tartalmaznia kell a licenc információkat tartalmazó webhely mutató hivatkozás. Általában akkor teheti a nevet a licenc hivatkozás. Például, ha a nyilatkozat **CC biztonsági Társítás licence szöveg** és CC biztonsági Társítás a nevét, a licenc, akkor tenné CC biztonsági Társítás hivatkozás.  
  
### <a name="link-and-text-attribution"></a>Csatolás és a szöveg attribútumára  

A [LinkAttribution](reference.md#linkattribution) és [TextAttribution](reference.md#textattribution) szabályok általában a szolgáltató az adatok azonosítására szolgál. A `targetPropertyName` mező azonosítja a mezőt, amelyekre a szabály vonatkozik.  
  
A szolgáltatók attribútuma, vegyen fel egy azonnal követően a tartalmat, amelyek érvényesek a megjelölését (például a célként megadott mező). A sor egyértelműen feliratú annak jelzésére, hogy a szolgáltatók a következők: az adatok forrását. Például "adatait: en.wikipedia.org". A `LinkAttribution` szabályok, létre kell hoznia a szolgáltató webhelyéről mutató hivatkozás.  
  
A következő szemléltet, amely tartalmazza az `LinkAttribution` és `TextAttribution` szabályok.  
  
![Hivatkozás szövege attribútumára](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Media attribútumára  

Ha az entitást tartalmaz egy képet, és megjeleníti azt, meg kell adnia egy kattintások hivatkozást a szolgáltató webhelyéről. Ha az entitást tartalmaz egy [MediaAttribution](reference.md#mediaattribution) szabály, a szabály URL-CÍMÉT használja a kattintások kapcsolat létrehozásához. Ellenkező esetben használja az URL-cím szerepel a lemezkép operációs rendszerének `provider` mező a kattintások kapcsolat létrehozásához.  
  
A következő példa egy példa, amely tartalmaz egy képet `provider` mező és szerződéses szabályokat. A példa a szerződéses szabályt tartalmaz, akkor figyelmen kívül a lemezkép operációs rendszerének `provider` mezőben, majd alkalmazza a `MediaAttribution` szabály.  
  
![Media attribútumára](./media/mediaattribution.png)  

## <a name="next-steps"></a>További lépések
- [C# gyors üzembe helyezés](c-sharp-quickstart.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)
