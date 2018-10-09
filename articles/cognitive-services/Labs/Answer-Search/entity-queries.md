---
title: 'Gyors útmutató: Projekt válasz keresési Entity query'
titlesuffix: Azure Cognitive Services
description: Lekérdezések az entitások használatával projekt válaszkeresés
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: article
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: efb46fc7064bcad69b5ea84f9bdfe923d95ccbe6
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867578"
---
# <a name="quickstart-query-for-entities"></a>Gyors útmutató: Entitások lekérdezése

Ha a lekérdezés egy személy, hely vagy dolog adatokat kér, a válasz tartalmazhat egy `entities` választ.  Lekérdezések bármikor visszatérhet a weblapok, [tények](fact-queries.md) és/vagy [entitások](entity-queries.md) lekérdezés függenek.

Entitások három lekérdezés forgatókönyveket támogatja: 
-   DominantEntity – csak egy entitást, amely megfelel a felhasználó lekérdezés és a leképezés van. Például a lekérdezést, terület tű, egy DominantEntity forgatókönyvet. 
-   Egyértelműsítő – egynél több entitás, amely megfelel a felhasználó lekérdezési és szándékot, és a felhasználó a megfelelő entitás kijelölése esetén a. Például a lekérdezés játékot, Thrones Egyértelműsítő a forgatókönyv a televízió-műsor és a könyv sorozat adja vissza. 
-   Lista – nincsenek több entitások, amelyek megfelelnek a felhasználói lekérdezés és szándékot. A lekérdezés "Veszélyeztetett List" például formázott megjelenítéshez a sorok és a cellák táblázatos értékeit adja vissza a lista forgatókönyv lesz. 
 
A lekérdezés forgatókönyv meghatározásához használja a `queryScenario` mezőjében a `entities` objektum. Az adatokat, amely az entitás tartalmazza az entitás típusától függ. Habár entitások ugyanazokat az alapszintű információkat tartalmaz, például idegenforgalmi létesítmények vagy könyvek néhány entitás tartalmazza a további tulajdonságokat. Entitások, amelyek tartalmazzák a további tulajdonságok közé tartozik a `_type` mező, amely tartalmaz egy mutatót a szerializáló használják. A következő entitásokat további tulajdonságokat tartalmazza: 
-   Címjegyzék 
-   MusicRecording 
-   Személy 
-   A vonzás 
 
Entitás, amely tartalmazza a válasz típusának meghatározása, használja a `entityTypeHints` mező, ahogy az a lekérdezés esetében Bill Gates.
````
        },
        "description": "Bill Gates is an American business man and philanthropist, co-founder of Microsoft",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Person"
          ]
        },
        "bingId": "6d7d66a7-2cb8-0ae9-637c-f81fd749dc9a"
      }
````
Az alábbiakban látható egy hely tű-lekérdezést:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
````
A válasz tartalmazza a `entities` választ. Megjegyzés: a `entityScenario` és `entityTypeHints` mezőket. 
````
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
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dSpace+Needle\u0026filters\u003dsid:%22f8dd5b08-206d-2554-6e4a-893f51f4de7e%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Space Needle",
        "image": {
          "name": "Space Needle",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA15d336cf119b9b5c7e0ab37e271421d3\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 152,
          "sourceHeight": 300
        },
        "description": "The Space Needle is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle. It was built in the Seattle Center for the 1962 World\u0027s Fair, which drew over 2.3 million visitors, when nearly 20,000 people a day used its elevators.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Attraction"
          ]
        },
        "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e"
      }
    ]
  },
````

A lekérdezés listáját is visszaadása, ha megfelelő.

**Lekérdezés:** a következő lekérdezés talál veszélyeztetett listáját:

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

````

**Válasz:** a válasz formázott megjelenítéshez táblázatos értékek listáját tartalmazza:
````
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "seeMoreUrl": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "value": [
      {
        "subjectName": "Species Directory",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "Common name",
            "Scientific name",
            "Conservation status ↓"
          ],
          "rows": [
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Amur Leopard",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/amur-leopard"
                },
                {
                  "text": "Panthera pardus orientalis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Black Rhino",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/black-rhino"
                },
                {
                  "text": "Diceros bicornis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Bornean Orangutan",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/bornean-orangutan"
                },
                {
                  "text": "Pongo pygmaeus"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Cross River Gorilla",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/cross-river-gorilla"
                },
                {
                  "text": "Gorilla gorilla diehli"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "46 more rows",
            "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
          }
        }
      }
    ]
  },

````


## <a name="next-steps"></a>További lépések
- [C# gyorsútmutató](c-sharp-quickstart.md)
- [Java a rövid útmutató](java-quickstart.md)
- [Csomópont a rövid útmutató](node-quickstart.md)
- [Python a rövid útmutató](python-quickstart.md)