---
title: Gyakorlati tanácsok az Azure Maps keresési szolgáltatásához | Microsoft Azure Maps
description: Ismerje meg, hogyan alkalmazhatja az ajánlott eljárásokat a Microsoft Azure Maps keresési szolgáltatásának használata kor.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335304"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Gyakorlati tanácsok az Azure Maps keresési szolgáltatásához

Az Azure Maps [keresési szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) különböző képességeket kínáló API-kat tartalmaz. Például a Keresési cím API-t lehet keresni érdekes pontok (POI) vagy adatok körül egy adott helyen. 

Ez a cikk bemutatja, hogyan alkalmazhatja a hangeljárásokat, amikor adatokat hív meg az Azure Maps keresési szolgáltatásból. A következőket fogja megtanulni:

* Lekérdezések létrehozása a megfelelő egyezések visszaadásához.
* Korlátozza a keresési eredményeket.
* Ismerje meg az eredménytípusok közötti különbségeket.
* Olvassa el a címkeresési válasz struktúrát.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Maps szolgáltatás API-k hívásához szüksége van egy Azure Maps-fiókra és egy kulcsra. További információt a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) és az elsődleges kulcs [benyomása](quick-demo-map-app.md#get-the-primary-key-for-your-account)című témakörben talál. 

Az Azure Maps hitelesítésével kapcsolatos további tudnivalókért olvassa el a Hitelesítés kezelése az Azure Mapsben című [témakört.](./how-to-manage-authentication.md)

> [!TIP]
> A keresési szolgáltatás lekérdezéséhez használhatja a [Postman alkalmazást](https://www.getpostman.com/apps) REST-hívások létrehozásához. Vagy használhatja bármilyen API-fejlesztési környezet, amely et szeretne.

## <a name="best-practices-to-geocode-addresses"></a>Gyakorlati tanácsok a címek geokódolásához

Ha teljes vagy részleges címet keres az Azure Maps search service használatával, az API beolvassa a kulcsszavakat a keresési lekérdezésből. Ezután visszaadja a cím hosszúsági és szélességi koordinátáit. Ezt a folyamatot *geokódolásnak nevezzük.* 

Egy országban a geokódolnivaló képessége a közúti adatok rendelkezésre állásától és a geokódolási szolgáltatás pontosságától függ. Az Azure Maps országonkénti vagy régiónkénti geokódolási képességeiről a [Geokódolási lefedettség](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)című témakörben olvashat bővebben.

### <a name="limit-search-results"></a>Keresési eredmények korlátozása

 Az Azure Maps Search API segítségével megfelelően korlátozhatja a keresési eredményeket. Korlátozza az eredményeket, hogy releváns adatokat jeleníthessen meg a felhasználók számára.

> [!NOTE]
> A keresési API-k több paramétert támogatnak, mint a cikkben tárgyalt paraméterek.

#### <a name="geobiased-search-results"></a>Geobiased keresési eredmények

Ahhoz, hogy geobias eredményeket a megfelelő területen a felhasználó, mindig adjunk meg annyi hely adatait, amennyire csak lehetséges. Előfordulhat, hogy bizonyos beviteli típusok megadásával korlátozni szeretné a keresési eredményeket:

* Állítsa `countrySet` be a paramétert. Beállíthatja például `US,FR`a beállítását. Alapértelmezés szerint az API az egész világon keres, így szükségtelen eredményeket adhat vissza. Ha a lekérdezés `countrySet` nem rendelkezik paraméterrel, akkor a keresés pontatlan eredményeket adhat vissza. Például egy *Bellevue* nevű város keresése az USA-ból és Franciaországból származó találatokat ad vissza, mivel mindkét ország tartalmaz egy *Bellevue*nevű várost.

* A és `topleft` `btmRight` a paraméterek segítségével beállíthatja a határolókeretet. Ezek a paraméterek a keresést a térkép egy adott számára korlátozzák.

* Az eredmények szempontjából releváns terület befolyásolásához `lon` határozza meg a és a `lat` koordináta-paramétereket. A `radius` paraméter segítségével állítsa be a keresési terület sugarát.


#### <a name="fuzzy-search-parameters"></a>Fuzzy keresési paraméterek

Azt javasoljuk, hogy használja az Azure Maps [Search Fuzzy API-t,](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) ha nem ismeri a felhasználói bemenetek egy keresési lekérdezéshez. Az API egyesíti a POI keresést és a geokódolást egy gyűjtő *egysoros keresésben:* 

* A `minFuzzyLevel` `maxFuzzyLevel` és a paraméterek segítenek a releváns egyezések visszaadásában, még akkor is, ha a lekérdezési paraméterek nem pontosan egyeznek meg a felhasználó által kívánt információkkal. A teljesítmény maximalizálása és a szokatlan eredmények `minFuzzyLevel=1` csökkentése `maxFuzzyLevel=2`érdekében állítsa a keresési lekérdezéseket a és a érték re. 

    Ha például `maxFuzzyLevel` a paraméter 2-re van állítva, a *restrant* keresési kifejezés *az étteremhez*igazodik. Szükség esetén felülbírálhatja az alapértelmezett intelligens szinteket. 

* A `idxSet` paraméter segítségével rangsorolja az eredménytípusok pontos készletét. Az eredmények pontos készletének fontossági sorrendbe adása érdekében vesszővel tagolt indexlistát küldhet be. A listában a tétel sorrendje nem számít. Az Azure Maps a következő indexeket támogatja:

* `Addr` - **Címtartományok**: Az utca elejétől és végétől interpolált címpontok. Ezek a pontok címtartományként jelennek meg.
* `Geo` - **Földrajz :** Közigazgatási felosztása föld. A földrajzi lehet például ország, állam vagy város.
* `PAD` - **Pontcímek:** Utcanevet és számot tartalmazó címek. A pontcímek egy indexben találhatók. Egy példa a *Soquel Dr. 2501*. A pontcím a címekhez elérhető legmagasabb pontosságot biztosítja.  
* `POI` - **Érdekességek**: Pontok a térképen, hogy érdemes figyelmet, vagy hogy érdekes lehet. A [keresési cím API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nem ad vissza ÉP-ok.  
* `Str` - **Utcák**: Utcák a térképen.
* `XStr` - **Utcák vagy kereszteződések keresztezése**: Olyan csomópontok vagy helyek, ahol két utca metszik egymást.


#### <a name="usage-examples"></a>Használati példák

* `idxSet=POI`- Csak a POI-k keresése. 

* `idxSet=PAD,Addr`- Csak a címeken. `PAD`a pontcímet, és `Addr` a címtartományt jelzi.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Fordított geokód és szűrés földrajzi entitástípushoz

Ha fordított geokód-keresést végez a [Search Address Reverse API-ban,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)a szolgáltatás sokszögeket adhat vissza a felügyeleti területekhez.Ha a keresést adott földrajzi entitástípusokra `entityType` szeretné szűkíteni, adja meg a paramétert a kérelmekben. 

Az eredményül kapott válasz tartalmazza a földrajzi azonosítót és az egyeztetett entitástípust. Ha egynél több entitást ad meg, akkor a végpont a *rendelkezésre álló legkisebb entitást*adja vissza. A visszaadott geometriaazonosító segítségével a Keresés [sokszög szolgáltatáson](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)keresztül kaphatja le a földrajz geometriáját.

#### <a name="sample-request"></a>Mintakérelem

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Válasz

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

### <a name="set-the-results-language"></a>Az eredménynyelv beállítása

A `language` paraméter segítségével állítsa be a visszaadott keresési eredmények nyelvét. Ha a kérelem nem állítja be a nyelvet, akkor alapértelmezés szerint a Keresési szolgáltatás az ország vagy régió leggyakoribb nyelvét használja. Ha a megadott nyelven nem áll rendelkezésre adat, a rendszer az alapértelmezett nyelvet használja. 

További információt az [Azure Maps által támogatott nyelvek című témakörben talál.](https://docs.microsoft.com/azure/azure-maps/supported-languages)


### <a name="use-predictive-mode-automatic-suggestions"></a>Prediktív mód használata (automatikus javaslatok)

Ha további egyezéseket szeretne `typeahead` találni `true`a részleges lekérdezésekhez, állítsa a paramétert a beállításra. Ezt a lekérdezést a rendszer részleges bemenetként értelmezi, és a keresés prediktív módba lép. Ha nem állítja `typeahead` be `true`a paramétert a beállításra, akkor a szolgáltatás feltételezi, hogy az összes vonatkozó információ átlett adva.

A következő mintalekérdezésben a rendszer lekérdezi a Search Address szolgáltatást a *Microso*számára. Itt a `typeahead` paraméter `true`beállítása . A válasz azt mutatja, hogy a keresési szolgáltatás a lekérdezést részleges lekérdezésként értelmezte. A válasz egy automatikusan javasolt lekérdezés eredményeit tartalmazza.

#### <a name="sample-query"></a>Mintalekérdezés

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Válasz

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Uri kódolása speciális karakterek kezeléséhez 

A keresztutcai címek megkereséséhez kódolnia kell az URI-t a cím speciális karaktereinek kezeléséhez. Tekintsük ezt a címet példa: *1st Avenue & Union Street, Seattle*. Itt kódolja az ampersand`&`karaktert ( ) a kérelem elküldése előtt. 

Azt javasoljuk, hogy a karakteradatokat egy URI-ban kódolja. Az URI-ban az összes karaktert százalékjellel (`%`) és kétkarakteres hexadecimális értékkel kódolhatja, amely megfelel a karakterek UTF-8 kódjának.

#### <a name="usage-examples"></a>Használati példák

Kezdje ezzel a címmel:

```
query=1st Avenue & E 111th St, New York
```

Kódolja a címet:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Az alábbi módszereket használhatja.

JavaScript vagy TypeScript:
```Javascript
encodeURIComponent(query)
```

C# vagy Visual Basic:
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

Php:
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

Megy:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Gyakorlati tanácsok az ÉP-kereséshez

Az ÉP-keresésben név szerint kérhet ÉP-eredményeket. Például név szerint kereshet egy vállalkozást. 

Javasoljuk, hogy használja a `countrySet` paramétert azon országok megadásához, ahol az alkalmazásnak fedezetre van szüksége. Az alapértelmezett viselkedés az, hogy keressen az egész világot. Ez az általános keresés szükségtelen eredményeket adhat vissza, és a keresés hosszú időt vehet igénybe.

### <a name="brand-search"></a>Márkakeresés

Az eredmények és a válaszban szereplő információk relevanciájának javítása érdekében a POI-keresési válasz tartalmazza a márkaadatokat. Ezen információk segítségével tovább elemezheti a választ.

A kérelemben vesszővel tagolt márkaneveket küldhet be. A lista segítségével a `brandSet` paraméter beállításával az eredményeket adott márkákra korlátozhatja. A listában az elemsorrend nem számít. Ha több márkalistát ad meg, a visszaadott eredményeknek legalább az egyik listához kell tartozniuk.

A márkakeresés felfedezéséhez készítsünk [EGY ÉP-kategória keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) kérelmet. A következő példában a Microsoft campus közelében, a washingtoni Redmondban keresünk benzinkutakat. A válasz a visszaadott ép-ok márkaadatait jeleníti meg.

#### <a name="sample-query"></a>Mintalekérdezés

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Válasz

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


### <a name="airport-search"></a>Repülőtéri keresés

A Search POI API használatával a repülőtereket a hivatalos kódjuk alapján keresheti meg. A *SEA* segítségével például megkeresheti a Seattle-Tacoma nemzetközi repülőteret: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Közeli keresés

Ha egy adott hely körül szeretné beolvasni az ÉP-találatokat, próbálja meg használni a [Közeli keresés API-t.](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) A végpont csak épép-eredményeket ad vissza. Nem veszi figyelembe a keresési lekérdezési paramétert. 

Az eredmények korlátozása érdekében azt javasoljuk, hogy állítsa be a sugarat.

## <a name="understanding-the-responses"></a>A válaszok megértése

Keressünk egy seattle-i címet, ha címkeresési kérelmet küldünk az Azure Maps keresési szolgáltatásához. A következő kérelem URL-címében úgy állítjuk be a `countrySet` paramétert, hogy `US` az USA-ban keresse meg a címet.

### <a name="sample-query"></a>Mintalekérdezés

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Támogatott eredménytípusok

* **Pont címe:** Pontok a térképen, amelyek egy adott címet egy utcanév és szám. A Pontcím a címek legmagasabb pontosságát biztosítja. 

* **Címtartomány:** Az utca elejétől és végétől interpolált címpontok tartománya.  

* **Földrajz**: A térképen olyan területek, amelyek egy földterület közigazgatási felosztását jelölik, például ország, állam vagy város. 

* **POI**: Pontok a térképen, hogy érdemes a figyelmet, és hogy érdekes lehet.

* **Utca**: Utcák a térképen. A címek feloldása a címet tartalmazó utca szélességi és hosszúsági koordinátáira lesz feloldva. Lehet, hogy a házszámot nem dolgozza fel a házszám. 

* **Kereszt utca**: kereszteződések. A keresztutcák olyan csomópontokat jelentenek, ahol két utca keresztezi egymást.

### <a name="response"></a>Válasz

Nézzük meg a válaszszerkezetet. Az ezt követő válaszban az eredményobjektumok típusai eltérőek. Ha figyelmesen megnézed, háromféle eredményobjektum jelenik meg:

* Pont címe
* Utca
* Kereszt utca

Figyelje meg, hogy a címkeresés nem ad vissza ÉP-ot.  

Az `Score` egyes válaszobjektumok paramétere azt jelzi, hogy az egyező pontszám hogyan kapcsolódik az ugyanabban a válaszban lévő többi objektum pontszámához. A válaszobjektum paramétereiről a Keresési cím beolvassa a [témakört.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)

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

A *geometria* választípusa tartalmazhatja a geometriaazonosítót, `geometry` amely `id`a `dataSources` csoport alatt található objektumban visszaad. A [Sokszög keresése szolgáltatással](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) például GeoJSON formátumban kérheti a geometriai adatokat. Ezzel a formátummal lekaphatja az entitások egy csoportjának város- vagy repülőtéri vázlatát. Ezután használhatja ezt a határadatokat [geokerítés](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) vagy keresési ÉP-ok beállításához [a geometrián belül.](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)


A Search [Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) API-ra vagy a [Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API-ra adott válaszok `dataSources` tartalmazhatják az objektumban a következő `geometry` csoportban visszaadott geometriaazonosítót: `id`


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni, kérjük, olvassa el:

> [!div class="nextstepaction"]
> [Az Azure Maps keresési szolgáltatásra vonatkozó kérelmek létrehozása](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Keresési szolgáltatás API-dokumentációja](https://docs.microsoft.com/rest/api/maps/search)