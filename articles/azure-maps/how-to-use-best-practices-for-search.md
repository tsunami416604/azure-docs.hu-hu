---
title: Hatékony keresés a Azure Maps Search szolgáltatás használatával | Microsoft Docs
description: Ismerje meg, hogyan használhatók az ajánlott eljárások a Azure Maps keresési szolgáltatással való kereséshez
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 25615ae8bc9bc8cadbe973f3a1859c2d43b067a9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915564"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Ajánlott eljárások Azure Maps Search Service használatához

A Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) különböző képességekkel rendelkező API-kat tartalmaz, például a címek keresésével, hogy egy adott helyen lévő érdeklődési pont (POI) adatait keressék. Ebben a cikkben az ajánlott eljárásokat fogjuk megosztani Azure Maps keresési szolgáltatásokon keresztül. Az alábbiakat fogja elsajátítani:

* Lekérdezések készítése a megfelelő egyezések visszaküldéséhez
* Keresési eredmények korlátozása
* Különböző típusú eredmények közötti különbség
* A keresési válasz struktúrájának beolvasása


## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Maps Service API-kon bármilyen hívást lehessen kezdeményezni, szüksége van egy Maps-fiókra és egy kulcsra. A fiókok létrehozásával kapcsolatos információkért kövesse a [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) című témakör útmutatását, és kövesse az [elsődleges kulcs lekérése](./tutorial-search-location.md#getkey) a fiókhoz elsődleges előfizetési kulcs lekéréséhez című témakör lépéseit.

> [!Tip]
> A Search szolgáltatás lekérdezéséhez használhatja a [Poster alkalmazást](https://www.getpostman.com/apps) a REST-hívások létrehozásához, vagy bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.


## <a name="best-practices-for-geocoding"></a>Ajánlott eljárások a helymeghatározáshoz

Ha Azure Maps Search Service használatával keres teljes vagy részleges címeket, a keresési kifejezést veszi át, és visszaadja a címe hosszúsági és szélességi koordinátáit. Ezt a folyamatot helymeghatározáshoz nevezzük. Egy adott országban való geocode a közúti adatlefedettségtől és a helymeghatározáshoz szolgáltatás helymeghatározáshoz pontosságtól függ.

Tekintse meg a [helymeghatározáshoz lefedettségét](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) , hogy az ország/régió Azure Maps helymeghatározáshoz képességeiről tudjon többet megtudni.

### <a name="limit-search-results"></a>Keresési eredmények korlátozása

   Ebből a szakaszból megtudhatja, hogyan használhatja Azure Maps keresési API-kat a keresési eredmények korlátozására. 

   > [!Note]
   > Nem minden keresési API teljes mértékben támogatja az alább felsorolt paramétereket

   **Geo-Bias keresési eredmények**

   Annak érdekében, hogy az eredményeket az adott felhasználóhoz tartozó megfelelő helyre szűkítse, mindig adja hozzá a lehető legrészletesebb hely bemenetet. A keresési eredmények korlátozásához vegye fontolóra a következő bemeneti típusok hozzáadását:

   1. Adja meg `countrySet` a paramétert, például: "US, fr". Az alapértelmezett keresési viselkedés a teljes világra irányuló keresés, ami felesleges eredményeket ad vissza. Ha a lekérdezés nem tartalmaz `countrySet` paramétert, előfordulhat, hogy a keresés pontatlan eredményeket ad vissza. Például a **Bellevue** nevű város keresése az USA és Franciaország eredményeit jeleníti meg, mivel vannak a franciaországi és az Egyesült Államokban található **Bellevue** nevű városok.

   2. A (z) `btmRight` és `topleft` paraméterek használatával beállíthatja, hogy a határolókeret egy adott területre korlátozza a keresést a térképen.

   3. Az eredmények fontossági területének befolyásolásához megadhatja a `lat`és `lon` a koordináta paramétert, és beállíthatja a keresési terület sugarát a `radius` paraméter használatával.


   **Fuzzy keresési paraméterek**

   1. A `minFuzzyLevel` és`maxFuzzyLevel`a segítségével akkor is ad vissza releváns egyezéseket, ha a lekérdezési paraméterek nem egyeznek meg pontosan a kívánt információkkal. A legtöbb keresési lekérdezés alapértelmezett `minFuzzyLevel=1` értéke `maxFuzzyLevel=2` a és a teljesítmény, valamint a szokatlan eredmények csökkentése. A "restrant" keresési kifejezésre példaként tekintse meg az "étterem" kifejezést, ha a `maxFuzzyLevel` értéke 2. Az alapértelmezett homályos szintek felülbírálják a kérelmekre vonatkozó igényeket. 

   2. Azt is megadhatja, hogy a `idxSet` paraméter használatával pontosan milyen típusú eredményt adjon vissza. Erre a célra elküldheti az indexek vesszővel tagolt listáját, az elemek sorrendje nem számít. A támogatott indexek a következők:

       * `Addr` - **Címtartományok**: Egyes utcákon olyan címek találhatók, amelyek az utca elejéről és végéről vannak interpolált. Ezek a pontok címtartományokként jelennek meg.
       * `Geo` - **Földrajzi**területek: A térképen egy olyan terület, amely a föld felügyeleti részlegét jelöli, vagyis ország, állam, város.
       * `PAD` - **Pont címe**:  Olyan térképekre mutat, amelyekben az utca nevét és számát tartalmazó adott címek megtalálhatók egy indexben, például Soquel Dr 2501. Ez a legmagasabb szintű pontosság a címek számára.  
       * `POI` - **Érdekes pontok**: Olyan térképen mutat, amely érdemes figyelmet érdemel, és érdekes lehet.  A [keresési címek beolvasása](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nem ad vissza POI-ket.  
       * `Str` - **Utcák**: Utcák ábrázolása a térképen.
       * `XStr` - **Cross Streets/** kereszteződések:  A csomópontok képviselete; helyek, ahol két utca metszi egymást.


       **Használati példák**:

       * idxSet = POI (csak keresési pontok) 

       * idxSet = PAD, addr (csak keresési címek, PAD = pont címe, addr = címtartomány)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Fordított geocode és földrajzi egység típusú szűrő

Ha fordított geocode keresést folytat a [keresési fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)-val, a szolgáltatás képes visszaadni a sokszögeket a felügyeleti területekhez. Ha megadja a paramétert `entityType` a kérelemben, szűkítheti a megadott földrajzi entitások típusának keresését. Az eredményül kapott válasz tartalmazni fogja a földrajzi azonosítót, valamint az entitás típusát. Ha egynél több entitást ad meg, a végpont az **elérhető legkisebb entitást**adja vissza. A visszaadott geometriai azonosító használatával lekérheti a földrajz geometriáját a [Get sokszög szolgáltatás](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)használatával.

**Példa a kérelemre:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Válasz**

```JSON
{
    "summary": {
        "queryTime": 8,
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
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
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

A `language` paraméterrel beállíthatja, hogy mely nyelvi keresési eredményeket adja vissza. Ha a kérelemben nincs beállítva a nyelv, a Search szolgáltatás automatikusan az ország/régió leggyakrabban használt nyelvét adja meg. Továbbá, ha a megadott nyelven nem érhető el az adathalmaz, az alapértelmezett nyelvet használja a rendszer. A támogatott nyelvek listáját [a támogatott nyelvek listájában tekintheti](https://docs.microsoft.com/azure/azure-maps/supported-languages) meg az ország/régió Azure Maps szolgáltatások tekintetében.


### <a name="predictive-mode-auto-suggest"></a>Prediktív mód (automatikus javaslat)

Ha további egyezéseket szeretne találni a részleges `typeahead` lekérdezésekhez, a paramétert "true" értékre kell beállítani. A lekérdezés részleges bemenetként lesz értelmezve, és a keresés a prediktív módot fogja beírni. Ellenkező esetben a szolgáltatás azt feltételezi, hogy az összes vonatkozó adat át lett adva.

Az alábbi minta lekérdezésben láthatja, hogy a keresési címtartomány lekérdezése "m", a `typeahead` paraméter értéke TRUE ( **igaz**). Ha betartja a választ, láthatja, hogy a keresési szolgáltatás részleges lekérdezésként értelmezte a lekérdezést, és az automatikusan javasolt lekérdezés eredményét tartalmazza.

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
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI-kódolás speciális karakterek kezeléséhez 

A Cross Street-címek megkereséséhez, azaz a "1st Avenue & Union Street, Seattle" speciális "&" karaktert a kérés elküldése előtt kódolni kell. Azt javasoljuk, hogy kódolja a karakterkódolási karaktereket egy URI-ban, ahol az összes karakter kódolása "%" karakterrel, az UTF-8 karakternek megfelelő kétkarakteres hexadecimális értékkel.

**Használati példák**:

Keresési címek beolvasása:

```
query=1st Avenue & E 111th St, New York
```

 a következőképpen kódolja:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


A különböző nyelveken a különböző módszereket használhatja: 

JavaScript/írógéppel:
```Javascript
encodeURIComponent(query)
```

C#/VB:
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

A POI-keresések lehetővé teszik a POI-találatok név szerinti kérését, például a keresés üzleti név alapján. Nyomatékosan javasoljuk, hogy a `countrySet` paraméterrel adja meg azokat az országokat, amelyeken az alkalmazásnak lefedettségre van szüksége, mivel az alapértelmezett viselkedés a teljes világon való keresés, a szükségtelen eredmények visszaadása és/vagy a hosszú keresési idő elérése.

### <a name="brand-search"></a>Márka keresése

Az eredmények relevanciájának és a válaszban szereplő információk (POI) keresési válaszának tökéletesítése érdekében a válaszok elemzéséhez továbbra is használhatók a részletes információk.

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
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
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
                "url": "www.chevron.com",
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
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
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
                "url": "www.texaco.com/",
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
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
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

Tegyünk egy címen belüli keresési kérelmet a Azure Maps [Search szolgáltatáshoz](https://docs.microsoft.com/rest/api/maps/search) egy Seattle-beli címen. Ha alaposan megtekinti az alábbi kérelem URL-címét, akkor a `countrySet` paramétert úgy állította be, hogy megkeresse a címet az amerikai **Egyesült** Egyesült Államokban.

**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

További tekintsük át az alábbi válasz-struktúrát. A válaszban található eredmény típusú objektumok eredményei eltérőek. Ha alaposan bemutatjuk, láthatjuk, hogy három különböző típusú eredmény-objektumunk van, amelyek "pont címe", "utca" és "Cross Street". Figyelje meg, hogy a címek keresése nem ad vissza POI-ket. Az egyes Response objektumokhoz tartozó paraméterarelatívegyezésipontszámotjelzi,hogyazazonosválaszbanlévőmásobjektumokpontszámaismegtörténjen.`Score` A válasz objektum paramétereinek megismeréséhez tekintse meg a [keresési címek beolvasása](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) című témakört.

**Támogatott típusú eredmények:**

* **Pont címe:** A térképen az utca nevét és számát tartalmazó adott címen található pontok szerepelnek. A címekhez elérhető legmagasabb pontossági szint. 

* **Címtartomány:**  Egyes utcákon olyan címek találhatók, amelyek az utca elejéről és végéről vannak interpolált. Ezek a pontok címtartományokként jelennek meg. 

* **Földrajz** A térképen egy olyan terület, amely a föld felügyeleti részlegét jelöli, vagyis ország, állam, város. 

* **POI – (érdekes pontok):** Olyan térképen mutat, amely érdemes figyelmet érdemel, és érdekes lehet.

* **Utca házszám** Utcák ábrázolása a térképen. A címek a címet tartalmazó utca szélességi/hosszúsági koordinátáihoz vannak feloldva. Lehetséges, hogy a házszám nem dolgozható fel. 

* **Cross Street:** Metszéspontjait. A csomópontok képviseletei; helyek, ahol két utca metszi egymást.

**Válasz**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometria

Ha a válasz típusa **geometria**, belefoglalhatja az **adatforrások** objektumban a "geometria" és az "id" alatt visszaadott geometriá-azonosítót. A [poligon szolgáltatás beolvasása](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) például lehetővé teszi a geometriai adatok GeoJSON formátumban való megadását, például a város vagy a repülőtér körvonalát az entitások egy halmaza számára. Ezt a határt a [geokerítések](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) , vagy [a geometrián belüli kereséshez](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)használhatja.


A [kereséshez](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) vagy a homályos API-válaszok [kereséséhez](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) az adatforrások objektumban a "geometria" és az "id" alatt VISSZAadott **geometriai azonosító** is szerepelhet.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>További lépések

* Megtudhatja [, hogyan hozhat létre Azure Maps keresési szolgáltatási kérelmeket](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Fedezze fel a Azure Maps [Search szolgáltatás API dokumentációját](https://docs.microsoft.com/rest/api/maps/search). 
