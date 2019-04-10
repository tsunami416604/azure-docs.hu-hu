---
title: Hatékonyan az Azure Maps Search szolgáltatással keresésének módjai |} A Microsoft Docs
description: Ajánlott eljárások használata az Azure Maps Search szolgáltatással keresés
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3a9c5ad92494dd82500c4faee82c119e99346c7a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288156"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Ajánlott eljárások az Azure Maps a Search Service használata

Az Azure Maps [keresőszolgáltatás](https://docs.microsoft.com/rest/api/maps/search) szolgáltatás API-k és különböző funkciókat, például adatainak érdeklődés pont (KOORDINÁTÁIIG) adatokat tartalmaznak egy adott hely keresése Keresés cím. Ebben a cikkben nyújtunk az ajánlott eljárások Azure Maps keresési szolgáltatások keresztüli meghívására. Az alábbiakat fogja elsajátítani:

* Vissza a megfelelő egyezések lekérdezések összeállítása
* Korlát keresési eredmények
* Ismerje meg a különféle eredmények közötti különbség
* Olvassa el a cím keresési válasz struktúra


## <a name="prerequisites"></a>Előfeltételek

Bármely hívásokat a Maps szolgáltatás API-k, szüksége van egy Maps-fiók és a kulcsot. Hozzon létre egy fiókot, és a egy kulcs lekérése kapcsolatos tudnivalókat lásd: [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md).

> [!Tip]
> A keresési szolgáltatás lekérdezés, használja a [Postman alkalmazás](https://www.getpostman.com/apps) hozhat létre REST hívásokkal bármely API fejlesztési környezetet, amely igény szerint használható.


## <a name="best-practices-for-geocoding"></a>Ajánlott eljárások a Geokódolás

Azure Maps Search Service használatával teljes vagy részleges cím keresésekor vesz igénybe a keresési kifejezést, és a cím szélességi és hosszúsági koordinátáit adja vissza. Ez a folyamat a geokódolás nevezzük. A geocode országban el közúti adatok lefedettsége és a geokódolás pontosságát a geokódolási szolgáltatás függ.

Lásd: [geokódolás lefedettsége](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) tudni, miért az Azure Maps geokódolás képességek ország/régió szerint.

### <a name="limit-search-results"></a>Korlát keresési eredmények

   Ebben a szakaszban megtudhatja, hogyan korlátozza a keresési eredmények az Azure Maps keresési API-k használatával. 

   > [!Note]
   > Nem minden search API-k teljes mértékben támogatja alábbi paraméterek

   **GEO-eltérés a keresési eredmények**

   Geo-eltérés sorrendben az eredményeket a felhasználó a megfelelő területre mindig adja hozzá a részletes maximális lehetséges hely bemeneti. Korlátozza a keresési eredmények között, fontolja meg a következő bemeneti típusok hozzáadása:

   1. Állítsa be a `countrySet` paramétert, például "US, FR". Az alapértelmezett keresés viselkedését, hogy keressen az egész világ, potenciálisan a szükségtelen eredményt adnak vissza. Ha a lekérdezés nem tartalmazza a `countrySet` paramétert, a keresés pontos eredményeket adhatnak vissza. Például keresse meg a Város nevű **Bellevue** fog eredményeinek visszaadása az USA és Franciaország, mivel nevű Város **Bellevue** Franciaországban, és az USA-ban.

   2. Használhatja a `btmRight` és `topleft` paraméterek beállítása a határoló mezőbe a Keresés korlátozása egy adott területre a térképen.

   3. Befolyásolhatják a terület az eredmények relevancia, meghatározhatja a `lat`és `lon` koordinálja a paramétereket, és állítsa be a radius használatával keresési terület a `radius` paraméter.


   **Az intelligens keresési paraméterek**

   1. A `minFuzzyLevel` és `maxFuzzyLevel`, segít a megfelelő egyezések adja vissza, akkor is, ha a lekérdezési paraméterek nem teljesen felel meg a kívánt adatokat. Alapértelmezés szerint a legtöbb keresési lekérdezések `minFuzzyLevel=1` és `maxFuzzyLevel=2` teljesítményt és csökkentheti a szokatlan eredményeket. Igénybe vehet egy keresési kifejezést például "restrant", akkor megfeleltetett "éttermi" Ha a `maxFuzzyLevel` 2 értékre van állítva. Az alapértelmezett intelligens szintek felülbírálható kérelem igényeinek megfelelően. 

   2. A visszaadandó eredmény típusok pontos készletét is megadhat a `idxSet` paraméter. Erre a célra nyújthatja be vesszővel elválasztott listája indexek, az elemek sorrendje nem számít. A támogatott indexeket a következők:

       * `Addr` - **Címtartomány**: Az egyes utcák cím elején és végén az utca; a rendszer interpolált pontok vannak azokat a pontokat címtartományok helyettesítik.
       * `Geo` - **Földrajzi területeken**: Egy térkép, vagyis a föld közigazgatási képviselő, ország, állam, város területeket.
       * `PAD` - **Pont címe**:  Egy térképen, ahol az utca nevét és számát adott cím található egy index, például Soquel Dr 2501 pontok. A legmagasabb szintű rendelkezésre álló címek pontossága.  
       * `POI` - **Hasznos helyek**: Pontok egy térképen érdemes figyelmet és érdekes lehet.  [Keresés cím](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) POIs nem ad vissza.  
       * `Str` - **Utcák**: A térképen utcák ábrázolása.
       * `XStr` - **Adatbázisközi utcák/metszéspontjait**:  Csomópontokban; ábrázolása a helyek, az intersect két utcák.


       **Használati példák**:

       * idxSet KOORDINÁTÁIIG (csak a fontos pontok. keresése) = 

       * idxSet KITÖLTŐ, cím = (keresési megoldást csak KITÖLTŐ pont cím, cím = = címtartomány)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Fordított geocode és földrajzi entitás-szűrő

A fordított geocode keresés végrehajtása során [keresés cím fordított API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), a szolgáltatás a felügyeleti területek poligonok vissza lehetőséggel rendelkezik. A paraméter megadásával `entityType` a kérésben megadott földrajzi entitástípusok keresése szűkíthető. Az eredményül kapott válasz tartalmazni fogja a földrajzi azonosítója, valamint a megfelelő entitástípus. Ha több entitást ad meg, végpont adja vissza a **elérhető legkisebb entitás**. Geometriai azonosító használható az adott földrajzi helyen keresztül geometriája beolvasásához visszaadott [sokszög első szolgáltatás](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Minta-kérelem:**

```HTTP
https://atlas.microsoft.com/search/address/json?api-version=1.0&subscription-key={subscription-key}&query=MicrosoftWay&entityType=Municipality
```

**Válasz:**

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

### <a name="search-results-language"></a>Keresési eredmények nyelv

A `language` paraméter lehetővé teszi, hogy állítsa be a keresési nyelv eredményeket adja vissza. Ha a kérésben nincs beállítva nyelv, keresési szolgáltatás automatikusan alapértelmezett ebben az országban vagy régióban a leggyakrabban használt nyelv. Ezenkívül adatokat a megadott nyelv nem érhető el, ha használatos az alapértelmezett nyelv. Lásd: [támogatott nyelvek](https://docs.microsoft.com/azure/azure-maps/supported-languages) megállapodást ország/régió szerint az Azure Maps szolgáltatás támogatott nyelvek listáját.


### <a name="predictive-mode-auto-suggest"></a>Prediktív mód (automatikus kiegészítési)

Részleges lekérdezések több találat található `typeHead` lehet "true" paramétert kell beállítani. A lekérdezés értelmezi a részleges bemenetként, és a keresés prediktív üzemmódba lép. Ellenkező esetben a szolgáltatás feltételezi az összes kapcsolódó információt lett átadva.

A minta az alábbi lekérdezés látható, hogy a keresési szolgáltatás lekérik a "Microsoft" és a `typehead` paraméter beállítása **igaz**. A válasz megfigyelte, láthatja, hogy a keresési szolgáltatás értelmezi a lekérdezés as részleges lekérdezés és válasz automatikus által javasolt lekérdezés eredményeit tartalmazza.

**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Válasz:**

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


### <a name="uri-encoding-to-handle-special-characters"></a>Kezeli a különleges karakterek kódolása URI 

Található street közötti címek, azt jelenti, 1. sérülésre & Union utca, Seattle, speciális karaktert ' és ' értéket kódolni kell a kérelem elküldése előtt. Javasoljuk, hogy karakteres adatot egy URI-t, kódolás, ha az összes karaktert kódolt (%) karaktereket használ, és az UTF-8 karakter megfelelő két karakterből álló hexadecimális értékként.

**Használati példák**:

Keresés-címének lekéréséhez:

```
query=1st Avenue & E 111th St, New York shall be encoded as query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York 
```


Az alábbiakban a különböző nyelvekhez használható különböző módszereket: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Ugrás:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Ajánlott eljárások a KOORDINÁTÁIIG keresése

Pontok érdeklődés (KOORDINÁTÁIIG) keresés KOORDINÁTÁIIG eredmények kérelem neve, például keresési üzleti név szerint teszi lehetővé. Határozottan javasoljuk, hogy használja a `countrySet` paraméterrel adja meg a országban, ahol az alkalmazásigények lefedettség, mint az alapértelmezett működés lesz keressen az egész világ, potenciálisan a szükségtelen eredményt adnak vissza, és/vagy a keresési hosszabb időt eredményez.

### <a name="brand-search"></a>Márka keresése

Javíthatja hatékonyságát az eredményeket, és a válaszban szereplő információkat, az érdeklődés pont (KOORDINÁTÁIIG) keresési válasz tartalmazza a márka információkat, amelyek segítségével tovább a válasz elemzéséhez.

Ellenőrizze, egy [KOORDINÁTÁIIG kategória keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) benzinkútjainkkal közel Microsoft-Kampusz (Redmond, WA) vonatkozó kérés. Megfigyelte a választ, ha minden visszaadott KOORDINÁTÁIIG márka információkat láthatja.

**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Válasz:**

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
        {
            "type": "POI",
            "id": "US/POI/p0/7728133",
            "score": 5.663,
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
                "url": "www.76.com/",
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
                "streetName": "148th Ave NE",
                "municipalitySubdivision": "Redmond, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148th Ave NE, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313
                    }
                }
            ]
        },
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

KOORDINÁTÁIIG keresési támogatja a repülőterek keresése a hivatalos repülőtér kódok segítségével. Ha például **tenger** (Seattle-Tata nemzetközi repülőtér). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Közeli keresése

Egy adott helyen, csak KOORDINÁTÁIIG adatokat beolvasni a [közeli search API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) lehet, hogy a megfelelő választás. Ez a végpont csak KOORDINÁTÁIIG eredményeket ad vissza, és nem veszi a keresési lekérdezés paraméterben. Az eredmények korlátozására, ajánlott a radius beállítása.

## <a name="understanding-the-responses"></a>A válaszok ismertetése

Ellenőrizze, hogy az Azure Maps egy cím-keresési kérelem [keresési szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) budapesti cím. Gondosan tekintse az alábbi kérelem URL-címet, ha rendelkezik beállított a `countrySet` paramétert **USA** , keresse meg a címet az Egyesült Államok jogszabályaival.

**Mintalekérdezés:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400BroadSt,Seattle,WA&countrySet=US
```

További nézzük meg, a válasz az alábbi struktúrát. A válaszban az eredményobjektumok eredmény típusú eltérőek. Ha megfigyelte gondosan megjelenik az eredményül kapott objektumokat három különböző típusú rendelkezünk, amelyek pont cím, az utca és közötti Street. Figyelje meg, hogy cím a keresés nem ad vissza POIs. A `Score` minden válasz objektum paraméter azt jelzi, a relatív egyező pontszám pontszámok más objektumok ugyanazt a választ. Lásd: [keresés cím első](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) többet megtudni a válasz objektum paramétereket.

**Az eredmény a támogatott típusok:**

**Pont címe:** Az utca nevét és számát az adott cím a térképen pontok. A legmagasabb szintű rendelkezésre álló címek pontossága. 

**Címtartomány:**  Az egyes utcák cím elején és végén az utca; a rendszer interpolált pontok vannak azokat a pontokat címtartományok helyettesítik. 

**Földrajzi hely:** Egy térkép, vagyis a föld közigazgatási képviselő, ország, állam, város területeket. 

**KOORDINÁTÁIIG - (a lényeges pont):** Pontok egy térképen érdemes figyelmet és érdekes lehet.

**Utca:** A térképen utcák ábrázolása. Címek hozzárendelve az utca, amely tartalmazza a címet a szélességi és hosszúsági koordinátákkal koordinátáját. A házszámát nem dolgozható fel. 

**Utca eltérő:** Metszéspontjait. Csomópontokban; ábrázolása a helyek, az intersect két utcák.

**Válasz:**

```JSON
{
    "summary": {
        "query": "400 broad st seattle wa",
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

### <a name="geometry"></a>geometriai

Válasz típusa esetén **geometriai**, azt is tartalmazza a geometriai Azonosítót, a visszaadott a **adatforrások** objektum "geometry" és "id" alatt. Ha például [sokszög első szolgáltatás](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) lehetővé teszi a geometriai adatokat egy GeoJSON formátumú, például a város vagy repülőtér körvonalának entitásokban. A határ adatait használhatja [Geokerítések](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) vagy [keresési POIs belül a geometriai](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Keresés cím](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) vagy [intelligens keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API-válaszok tartalmazhatnak a **geometriai azonosítója** által visszaadott az adatforrások objektum "geometry" és "id" alatt.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>További lépések

* Ismerje meg, [hogyan hozhat létre az Azure Maps keresési kérelmeket](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Ismerkedés az Azure Maps [keresési szolgáltatás API-dokumentáció](https://docs.microsoft.com/rest/api/maps/search). 