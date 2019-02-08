---
title: 'Gyors útmutató: Projekt válasz keresési Entity query'
titlesuffix: Azure Cognitive Services
description: Entitások lekérdezése a Válaszkeresés projekt használatával
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: 0536831d22f40cb9783e95de2708f40010eb62d9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883865"
---
# <a name="quickstart-query-for-entities"></a>Gyors útmutató: Entitások lekérdezése

Ha a lekérdezés egy személlyel, hellyel vagy dologgal kapcsolatos adatokra irányul, a válasz tartalmazhat egy `entities` választ.  A lekérdezések minden esetben weblapokat adnak vissza, a [tények](fact-queries.md) és/vagy az [entitások](entity-queries.md) pedig lekérdezésfüggők.

Az entitások három lekérdezési forgatókönyvet támogatnak: 
-   DominantEntity (domináns entitás) – Egyetlen entitás felel meg a felhasználó lekérdezésének és szándékának. A „Space Needle” lekérdezés például egy DominantEntity forgatókönyv. 
-   Disambiguation (Egyértelműsítés) – Több entitás is megfelel a felhasználó lekérdezésének és szándékának, és a felhasználó maga választja ki közülük a megfelelőt. A „Trónok harca” lekérdezés például egy egyértelműsítési forgatókönyv, mivel a tévésorozatot és a könyvsorozatot is visszaadja. 
-   List (Lista) – Több entitás is megfelel a felhasználó lekérdezésének és szándékának. Például a „Veszélyeztetett állatok listája” lekérdezés egy listaforgatókönyv, amely sorokban és cellákban való megjelenítéshez formázott táblázatos értékeket ad vissza. 
 
A lekérdezési forgatókönyv meghatározásához használja az `entities` objektum `queryScenario` mezőjét. Az entitás által hordozott adatokat az entitás típusa határozza meg. Bár az entitások ugyanazokat az alapszintű információkat tartalmazzák, egyes entitások, például az idegenforgalmi látványosságok vagy a könyvek, további tulajdonságokkal is rendelkeznek. A további tulajdonságokkal is rendelkező entitásokban megtalálható a `_type` mező, amely egy, a szerializáló által használt mutatót tartalmaz. A következő entitások tartalmaznak további tulajdonságokat: 
-   Könyv 
-   MusicRecording (Zenei felvétel) 
-   Személy 
-   Látványosság 
 
A válasz által tartalmazott entitás típusának meghatározásához használja az `entityTypeHints` mezőt, ahogy azt a „Bill Gates” lekérdezésnél bemutatjuk.
```
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
```
Az alábbi a „Space Needle” lekérdezés:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
```
A válasz tartalmazza az `entities` választ. Figyelje meg az `entityScenario` és `entityTypeHints` mezőket. 
```
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
```

A lekérdezés adott esetben egy listát is visszaadhat.

**Lekérdezés:** A következő lekérdezés talál veszélyeztetett listáját:

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

```

**Válasz:** A válasz formázott megjelenítéshez táblázatos értékek listáját tartalmazza:
```
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

```


## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)
