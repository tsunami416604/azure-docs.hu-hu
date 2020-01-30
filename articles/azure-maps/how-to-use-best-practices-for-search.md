---
title: Hatékony keresés a Azure Maps Search Service használatával | Microsoft Azure térképek
description: Ismerje meg, hogyan alkalmazhatja az ajánlott eljárásokat a keresési szolgáltatáshoz Microsoft Azure Maps használatával
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845754"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Ajánlott eljárások Azure Maps Search Service használatához

A Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) különböző képességekkel rendelkező API-kat tartalmaz. A Search API például a keresett pont (POI) vagy egy adott hely körüli keresésre szolgál. Ez a cikk a Azure Maps keresési szolgáltatások adatainak meghívásakor alkalmazandó helyes eljárásokat mutatja be. Az alábbiakat fogja elsajátítani:

* Lekérdezések készítése a megfelelő egyezések visszaküldéséhez
* Keresési eredmények korlátozása
* Különböző típusú eredmények közötti különbség
* A keresési válasz struktúrájának beolvasása


## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Maps Service API-kon bármilyen hívást lehessen kezdeményezni, szüksége lesz egy Azure Maps fiókra és egy kulcsra. Ha szükséges, kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) és az [elsődleges kulcs beszerzése](quick-demo-map-app.md#get-the-primary-key-for-your-account)című témakör utasításait. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

> [!Tip]
> A Search szolgáltatás lekérdezéséhez használhatja a [Poster alkalmazást](https://www.getpostman.com/apps) a REST-hívások létrehozásához, vagy bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.


## <a name="best-practices-for-geocoding-address-search"></a>Ajánlott eljárások a helymeghatározáshoz (címek keresése)

Ha Azure Maps Search Service használatával teljes vagy részleges címeket keres, az API beolvassa a kulcsszavakat a keresési lekérdezésből, és visszaadja a címe hosszúsági és szélességi koordinátáit. Ezt a folyamatot helymeghatározáshoz nevezzük. Egy adott országban való geocode a közúti adatlefedettségtől és a helymeghatározáshoz szolgáltatás helymeghatározáshoz pontosságtól függ.

Tekintse meg a [helymeghatározáshoz lefedettségét](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) , hogy az ország/régió Azure Maps helymeghatározáshoz képességeiről tudjon többet megtudni.

### <a name="limit-search-results"></a>Keresési eredmények korlátozása

 A Azure Maps Search API segítségével megfelelően korlátozhatja a keresési eredményeket, így a kapcsolódó adatokat megjelenítheti a felhasználók számára.

   > [!Note]
   > Az alábbi lista nem tartalmazza az összes támogatott keresési API-paramétert

   **Geo-Bias keresési eredmények**

   Annak érdekében, hogy az eredményeket az adott felhasználóhoz tartozó megfelelő helyre szűkítse, mindig adja hozzá a lehető legrészletesebb hely bemenetet. A keresési eredmények korlátozásához vegye fontolóra a következő bemeneti típusok hozzáadását:

   1. Adja meg a `countrySet` paramétert (például "US, FR"). Az alapértelmezett keresési viselkedés a teljes világra irányuló keresés, ami felesleges eredményeket ad vissza. Ha a lekérdezés rendelkezik a `countrySet` paraméterrel, a keresés pontatlan eredményeket adhat vissza. Például a **Bellevue** nevű város keresése az USA és Franciaország eredményét fogja visszaadni, mivel vannak olyan városok, amelyeknek **a neve a** franciaországi és az Egyesült Államokban található.

   2. A határolókeret megadásához a `btmRight` és `topleft` paramétereket használhatja, hogy a keresést a Térkép egy adott területére korlátozza.

   3. Az eredmények fontossági területének befolyásolásához megadhatja a `lat`és `lon` koordináta-paramétereket, és a `radius` paraméter használatával beállíthatja a keresési terület sugarát.


   **Fuzzy keresési paraméterek**
   
  Azure Maps a [fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) a javasolt szolgáltatás, amely akkor használható, ha nem tudja, hogy a felhasználói bemenetek milyen keresési lekérdezésekhez tartoznak. Az API összekapcsolja az érdeklődési pont (POI) keresését és a helymeghatározáshoz egy kanonikus *egysoros kereséssel*.

   1. A `minFuzzyLevel` és `maxFuzzyLevel` segítséget nyújt a megfelelő egyezések visszaadásához, még akkor is, ha a lekérdezési paraméterek nem egyeznek pontosan a kívánt információkkal. A teljesítmény eléréséhez és a szokatlan eredmények csökkentéséhez az alapértelmezett keresési lekérdezések `minFuzzyLevel=1` és `maxFuzzyLevel=2`. A "restrant" keresési kifejezésre példaként tekintse meg az "étterem" kifejezést, ha a `maxFuzzyLevel` 2 értékre van állítva. Az alapértelmezett homályos szintek szükség szerint felülbírálva lehetnek.  

   2. A `idxSet` paraméterrel rangsorolhatja az eredményhalmaz pontos készletét is. Erre a célra elküldheti az indexek vesszővel tagolt listáját; az elemek sorrendje nem számít. A következő indexek támogatottak:

       * `Addr` - **címtartományok**: egyes utcáknál vannak olyan címek, amelyek az utca elejétől és végétől kezdve interpoláltak. Ezek a pontok címtartományokként jelennek meg.
       * `Geo` - **földrajzi**régió: a térképen egy olyan terület, amely a föld felügyeleti részlegét jelöli, vagyis ország, állam, város.
       * `PAD` - **pont címe**: pontok egy térképen, ahol az utca nevével és számával megadott címek megtalálhatók egy indexben, például Soquel Dr 2501. Ez a idxSet érték a címek számára elérhető legmagasabb szintű pontosság.  
       * `POI` - érdekes **pontok**: egy térképen, amely érdemes figyelmet érdemel, és érdekes lehet.  A [keresési címek beolvasása](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nem ad vissza POI-ket.  
       * `Str` - **utcáin**: az utcák ábrázolása a térképen.
       * `XStr` - **Cross Streets/** metszéspontok: az elágazások ábrázolása; helyek, ahol két utca metszi egymást.


       **Használati példák**:

       * idxSet = POI (csak keresési pontok) 

       * idxSet = PAD, addr (csak keresési címek, PAD = pont címe, addr = címtartomány)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Fordított geocode és földrajzi egység típusú szűrő

Ha fordított geocode keresést végez a [keresési fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)-val, a szolgáltatás képes visszaadni a sokszögeket a felügyeleti területekre. A kérelemben `entityType` paraméter megadásával szűkítheti a megadott földrajz típusú entitások keresését. Az eredményül kapott válasz tartalmazni fogja a földrajzi azonosítót és az entitás típusát. Ha egynél több entitást ad meg, a végpont az **elérhető legkisebb entitást**adja vissza. A visszaadott geometriai azonosító használatával lekérheti a földrajz geometriáját a [Get sokszög szolgáltatás](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)használatával.

**Példa a kérelemre:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Válasz**

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Keresési eredmények nyelve

A `language` paraméterrel kiválaszthatja az API által visszaadott eredmények nyelvét. Ha a kérelemben nincs beállítva a nyelv, a keresési szolgáltatás automatikusan az ország/régió leggyakrabban használt nyelvét adja meg. Továbbá, ha a megadott nyelven nem érhető el az adathalmaz, az alapértelmezett nyelvet használja a rendszer. A [támogatott nyelvek listáját](https://docs.microsoft.com/azure/azure-maps/supported-languages) a Azure Maps Services ország/régió szerint című témakörében tekintheti meg.


### <a name="predictive-mode-autosuggest"></a>Prediktív mód (automatikus javaslat)

A részleges lekérdezésekre vonatkozó további egyezések kereséséhez `typeahead` paramétert "true" értékre kell beállítani. A lekérdezés részleges bemenetként lesz értelmezve, és a keresés a prediktív módot fogja beírni. Ellenkező esetben a szolgáltatás azt feltételezi, hogy az összes vonatkozó adat át lett adva.

Az alábbi minta lekérdezésben láthatja, hogy a keresési címtartomány lekérdezése "m", az `typeahead` paraméter értéke **true (igaz**). Ha betartja a választ, láthatja, hogy a keresési szolgáltatás részleges lekérdezésként értelmezte a lekérdezést. A válasz az automatikus javasolt lekérdezés eredményét tartalmazza.

**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Válasz**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-kódolás speciális karakterek kezeléséhez 

A Cross Street-címek kereséséhez kódolnia kell az URI-t a címben szereplő speciális karakterek kezelésére. Tekintse meg ezt a példát: "1st Avenue & Union Street, Seattle". A (z) "&" speciális karaktert a kérés elküldése előtt kódolni kell. Azt javasoljuk, hogy kódolja a karakterkódolási karaktereket egy URI-ban, ahol az összes karakter kódolása "%" karakterrel, az UTF-8 karakternek megfelelő kétkarakteres hexadecimális értékkel.

**Használati példák**:

Keresési címek beolvasása:

```
query=1st Avenue & E 111th St, New York
```

 a következőképpen kódolja:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


A különböző nyelveken a különböző módszereket használhatja: 

JavaScript/írógéppel:
```Javascript
encodeURIComponent(query)
```

C#VB
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Lépjen
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Ajánlott eljárások a POI-kereséshez

A POI-keresések lehetővé teszik a POI-találatok név szerinti kérését, például a keresés üzleti név alapján. Nyomatékosan javasoljuk, hogy a `countrySet` paraméterrel adja meg, hogy az alkalmazás mely országokban legyen lefedettségi köre, mivel az alapértelmezett viselkedés a teljes világra való rákeresés, a szükségtelen eredmények visszaadása és/vagy a hosszú keresési idő miatti eredmény.

### <a name="brand-search"></a>Márka keresése

Az eredmények relevanciájának és a válaszban szereplő információk (POI) keresési válaszának tökéletesítése érdekében a válaszok elemzéséhez továbbra is használhatók a részletes információk.

A kérelemben elküldheti a márkanevek vesszővel tagolt listáját is. A listával korlátozhatja az eredményeket adott márkákra a `brandSet` paraméter használatával. Az elemek sorrendje nem számít. Több márka megadása esetén a rendszer csak a megadott (legalább) találatok egyikét adja vissza.

Tegyük fel, hogy a Microsoft Campus (Redmond, WA) közelében megjelenő [POI-kategóriák keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) kérelme a benzinkutak. Ha betartja a választ, megtekintheti a visszaadott POI-ra vonatkozó összes információt.


**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Válasz**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Repülőtér keresése

A POI Search a hivatalos repülőtéri kódok használatával támogatja a keresést a repülőtereken. Például: **Sea** (Seattle-Tacoma International Airport). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Közeli keresés

Ha csak a POI-eredményeket szeretné lekérni egy adott hely körül, a [közeli keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) lehet a megfelelő választás. Ez a végpont csak a POI eredményeit fogja visszaadni, és nem a keresési lekérdezési paramétert használja. Az eredmények korlátozásához ajánlott beállítani a sugarat.

## <a name="understanding-the-responses"></a>A válaszok ismertetése

Tegyünk egy címen belüli keresési kérelmet a Azure Maps [Search szolgáltatáshoz](https://docs.microsoft.com/rest/api/maps/search) egy Seattle-beli címen. Ha alaposan megtekinti az alábbi kérelem URL-címét, akkor a `countrySet` paramétert a **US** értékre állítva, hogy megkeresse a címet az Amerikai Egyesült Államokban.

**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

További tekintsük át az alábbi válasz-struktúrát. A válaszban található eredmény típusú objektumok eredményei eltérőek. Ha alaposan bemutatjuk, láthatjuk, hogy három különböző típusú eredmény-objektumunk van, amelyek "pont címe", "utca" és "Cross Street". Figyelje meg, hogy a címek keresése nem ad vissza POI-ket. Az egyes Response objektumokhoz tartozó `Score` paraméter a relatív egyezési pontszámot jelzi, hogy az azonos válaszban lévő más objektumok pontszáma is megtörténjen. A válasz objektum paramétereinek megismeréséhez tekintse meg a [keresési címek beolvasása](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) című témakört.

**Támogatott típusú eredmények:**

* **Pont címe:** A térképen az utca nevét és számát tartalmazó adott címen található pontok szerepelnek. A címekhez elérhető legmagasabb pontossági szint. 

* **Címtartomány:**  Egyes utcákon olyan címek találhatók, amelyek az utca elejéről és végéről vannak interpolált. Ezek a pontok címtartományokként jelennek meg. 

* **Földrajzi hely:** A térképen egy olyan terület, amely a föld felügyeleti részlegét jelöli, vagyis ország, állam, város. 

* **POI – (érdekes pontok):** Olyan térképen mutat, amely érdemes figyelmet érdemel, és érdekes lehet.

* **Utca:** Utcák ábrázolása a térképen. A címek a címet tartalmazó utca szélességi/hosszúsági koordinátáihoz vannak feloldva. Lehetséges, hogy a házszám nem dolgozható fel. 

* **Cross Street:** Metszéspontjait. A csomópontok képviseletei; helyek, ahol két utca metszi egymást.

**Válasz**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometria

Ha a válasz típusa **geometria**, belefoglalhatja az **adatforrások** objektumban a "geometria" és az "id" alatt visszaadott geometriá-azonosítót. A [Polygon szolgáltatás beolvasása](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) például lehetővé teszi a geometriai adatok GeoJSON formátumban való igénylését. Például a város vagy a repülőtér vázlata az entitások egy halmazához. Ezt a határt a [geokerítések](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) , vagy [a geometrián belüli kereséshez](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)használhatja.


A [kereséshez](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) vagy a homályos API-válaszok [kereséséhez](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) az adatforrások objektumban a "geometria" és az "id" alatt VISSZAadott **geometriai azonosító** is szerepelhet.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Következő lépések

* Megtudhatja [, hogyan hozhat létre Azure Maps keresési szolgáltatási kérelmeket](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Fedezze fel a Azure Maps [Search szolgáltatás API dokumentációját](https://docs.microsoft.com/rest/api/maps/search). 
