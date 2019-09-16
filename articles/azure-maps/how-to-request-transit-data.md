---
title: Adatátviteli információ kérése Azure Mapsban | Microsoft Docs
description: A Azure Maps mobilitási szolgáltatással nyilvános adatátviteli szolgáltatásokat igényelhet.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b28788ac7b3ce4e1997b71c683f8e0445406a391
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915616"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>A Azure Maps mobilitási szolgáltatással nyilvános árutovábbítási adatai igényelhetők 

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Maps [mobilitási szolgáltatást](https://aka.ms/AzureMapsMobilityService) a nyilvános árutovábbítási adatok kérésére, beleértve a leállásokat, az útvonal-információkat és az utazási idő becsléseit.

Ebben a cikkben a következőket fogja elsajátítani:

* Metro-körzet AZONOSÍTÓjának beolvasása a [Metro-körzet API](https://aka.ms/AzureMapsMobilityMetro) beolvasása használatával
* A közeli átvitel a [közeli tranzit](https://aka.ms/AzureMapsMobilityNearbyTransit) szolgáltatás használatával leáll.
* A lekéréses [útvonalak API](https://aka.ms/AzureMapsMobilityTransitRoute) -val történő lekérdezésével megtervezheti az útvonalakat a nyilvános átvitel használatával.
* Tranzit útválasztási geometry és az útvonal használatára vonatkozó részletes ütemezése a [első átvitel Útiterv API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Előfeltételek

Ha bármilyen hívást szeretne tenni a Azure Maps nyilvános átviteli API-khoz, szüksége van egy Maps-fiókra és egy kulcsra. A fiókok létrehozásával kapcsolatos információkért kövesse a [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) című témakör útmutatását, és kövesse az [elsődleges kulcs lekérése](./tutorial-search-location.md#getkey) a fiókhoz elsődleges előfizetési kulcs lekéréséhez című témakör lépéseit.

Ez a cikk a [Poster alkalmazást](https://www.getpostman.com/apps) használja a REST-hívások létrehozásához. Bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.


## <a name="get-a-metro-area-id"></a>Metro-körzet AZONOSÍTÓjának beolvasása

Egy adott nagyvárosi terület továbbítási információinak igényléséhez szüksége `metroId` lesz arra a területre, amelyre a továbbítási adatokat szeretné kérni. A [Metro Area API beolvasása](https://aka.ms/AzureMapsMobilityMetro) lehetővé teszi, hogy olyan metró területeket kérjen, amelyeken elérhető a Azure Maps mobilitási szolgáltatás. A válasz olyan adatokat tartalmaz `metroId`, `metroName` mint a, és a Metro terület geometriájának ábrázolása GeoJSON formátumban.

Tegyük fel, hogy beolvassák a Seattle-Tacoma Metro területi AZONOSÍTÓhoz tartozó Metro területet. A Metro-területek AZONOSÍTÓjának kéréséhez hajtsa végre a következő lépéseket:

1. Hozzon létre egy gyűjteményt, amelyben tárolni szeretné a kérelmeket. A Poster alkalmazásban válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény**elemet. Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget. Adja meg a kérelem **nevét** , válassza ki az előző lépésben létrehozott gyűjteményt a kérelem mentési helyeként, majd kattintson a **Mentés**gombra.
    
    ![Kérelem létrehozása a Poster-ban](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a HTTP beolvasása metódust a Builder (szerkesztő) lapon, majd a GET kérelem létrehozásához adja meg a következő URL-címet.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Sikeres kérés után a következő választ fogja kapni:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Másolja a `metroId`, hogy később használhassa.

## <a name="request-nearby-transit-stops"></a>A közeli tranzit leállási kérések

A Azure Maps a [közeli tranzit](https://aka.ms/AzureMapsMobilityNearbyTransit) szolgáltatás lehetővé teszi, hogy áttekintse az átvitt objektumokat, például a nyilvános tranzit leáll, és egy adott hely körüli, az árutovábbítási objektum részleteit visszaadó kerékpárokat. A következő lépésben egy kérést küldünk a szolgáltatásnak, hogy megkeresse a közeli nyilvános átvitelt az adott helyen 300 méteres sugarú körön belül. A kérelemben szerepelnie kell a `metroId` korábban lekértnek.

Az alábbi lépések végrehajtásával teheti meg a lekéréses [továbbítást a közeli átvitelre](https://aka.ms/AzureMapsMobilityNearbyTransit):

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** és név lehetőségre, amely a **közeli leáll**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz az alábbi kérelem URL-címét, majd kattintson a **Küldés**gombra.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Sikeres kérés után a válasz struktúrájának az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Ha körültekintően figyeli a válasz struktúráját, láthatja, hogy minden egyes tranzit objektum, `id`például `type` `stopName` `mainTransitType` `mainAgencyName` a,,, és az objektum pozíciója (koordinátái) tartalmaz paramétereket.

A következő szakaszban azt fogjuk használni `id` , hogy az egyik busz leáll az útvonal forrásaként.  


## <a name="request-a-transit-route"></a>Átviteli útvonal kérése

A Azure Maps [Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) lehetővé teszi, hogy az utazás megtervezése a lehető legjobb útvonalat adja vissza a forrás és a cél között. A szolgáltatás számos utazási módot kínál, többek között a gyaloglást, a kerékpározást és a nyilvános átvitelt. Ezután a legközelebbi buszmegállóból keresünk egy útvonalat Seattle-ben.

### <a name="get-location-coordinates-for-destination"></a>Hely koordinátáinak beolvasása célhelyként

A Space tű helyének koordinátáinak beszerzéséhez használja a Azure Maps [fuzzy Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Az alábbi lépések végrehajtásával teheti meg a kérést a fuzzy Search szolgáltatásnak:

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze el a **hely koordinátáit**.

2.  A Builder (szerkesztő) lapon válassza a http **beolvasása** metódust, adja meg a kérelem URL-címét, majd kattintson a **Küldés**gombra.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Ha alaposan megtekinti a választ, az a Space tű eredményei között több helyet is tartalmaz, és a hely koordinátáinak adatait is tartalmazza, a **pozíció**alatt. Másolja a `lat` és `lon` a feladó pozícióját az első eredményre.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Kérelem útvonala

Egy útvonal-kérelem elvégzéséhez hajtsa végre az alábbi lépéseket:

1. A Poster lapon kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze el az **útválasztási adatokat**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz az alábbi kérelem URL-címét, majd kattintson a **Küldés**gombra.

    A és `modeType` `transitType` a paraméterek megadásával nyilvános átviteli útvonalakat fogunk kérni a buszhoz. A kérelem URL-címe tartalmazza az előző szakaszban lekért helyet. Mivel `originType` most már rendelkezünk a `destionationType` **stopId** és a **pozícióval**.

    Tekintse meg a kérésben használható [URI-paraméterek listáját](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) a [Transit Routes API beszerzéséhez](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Sikeres kérés esetén a válasz struktúrájának az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Ha alaposan megfigyel, több **busz** útvonala is van a válaszban. Minden egyes útvonal egyedi **útvonal-azonosítóval** és az útvonal minden egyes szakaszát leíró összegzéssel rendelkezik. Ezután a válaszban a `itineraryId` leggyorsabb útvonal részleteit fogjuk kérni.

## <a name="request-fastest-route-itinerary"></a>Leggyorsabb útvonal-útvonal kérése

A Azure Maps [Transit útvonal](https://aka.ms/AzureMapsMobilityTransitItinerary) -szolgáltatás lehetővé teszi, hogy egy adott útvonalra vonatkozó adatokat igényeljen az [átviteli útvonalak API](https://aka.ms/AzureMapsMobilityTransitRoute) szolgáltatása által visszaadott útvonal útvonal- **azonosítójának** használatával. A kérelem végrehajtásához hajtsa végre az alábbi lépéseket:

1. A Poster lapon kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze el az **átvitelhez szükséges adatokat**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz az alábbi kérelem URL-címét, majd kattintson a **Küldés**gombra.

    A `detailType` paramétert a **geometriai** értékre állítja be, hogy a válasz tartalmazza a nyilvános átvitel leállítási információit, és kapcsolja be a navigálást az útvonalon a gyaloglás és a kerékpáros lábak számára.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Sikeres kérés esetén a válasz struktúrájának az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan kérhet valós idejű adatgyűjtést a mobilitási szolgáltatás használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatkérések igénylése](how-to-request-real-time-data.md)

A Azure Maps mobilitási szolgáltatás API dokumentációjának megismerése

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatás API-dokumentációja](https://aka.ms/AzureMapsMobilityService)

