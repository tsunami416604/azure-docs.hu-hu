---
title: Az Azure Maps átvitt adatok kérésének |} A Microsoft Docs
description: Az Azure Maps mobilitási szolgáltatással nyilvános átvitt adatokat kér.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e8250763153f7c5b71f3906a560365dadfd55694
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735571"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Az Azure Maps mobilitási szolgáltatással nyilvános átvitt adatok kérése 

Ez a cikk bemutatja, hogyan használható az Azure Maps [a mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService) igénylése nyilvános átvitt adatokat, beleértve az leáll, az útvonal-információk és az utazási idő becsléseket.

Ebben a cikkben bemutatjuk hogyan lehet:

* Első metro terület azonosítója használatával a [Metro terület API beszerzése](https://aka.ms/AzureMapsMobilityMetro)
* Kérelem közeli átvitel nem használja tovább [közeli átvitel első](https://aka.ms/AzureMapsMobilityNearbyTransit) szolgáltatás.
* Lekérdezés [első átvitel útvonalak API](https://aka.ms/AzureMapsMobilityTransitRoute) útvonal megtervezéséhez a nyilvános átvitel használatával.
* Tranzit útválasztási geometry és az útvonal használatára vonatkozó részletes ütemezése a [első átvitel Útiterv API](https://aka.ms/ https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Előfeltételek

Bármely hívásokat az Azure Maps nyilvános átviteli API-k, szüksége van egy Maps-fiók és a kulcsot. Hozzon létre egy fiókot, és a egy kulcs lekérése kapcsolatos tudnivalókat lásd: [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md).

Ez a cikk a [Postman alkalmazás](https://www.getpostman.com/apps) hozhat létre REST-hívások. Minden API fejlesztési környezetet, amely igény szerint is használhatja.


## <a name="get-a-metro-area-id"></a>Metro terület azonosító beszerzése

Annak érdekében, hogy egy adott nagyvárosi körzetében, szüksége lesz a átvitt adatok kérése a `metroId` a terület szeretne igényelni az átvitt adatokat. [Metro terület API beszerzésének](https://aka.ms/AzureMapsMobilityMetro) lehetővé teszi metro területek, amelyben az Azure Maps a mobilitási szolgáltatás érhető el. Válasz tartalmazza a részleteket például `metroId`, `metroName` és a egy GeoJSON formátumú metro terület geometriai reprezentációját.

Ellenőrizze, a Seattle-Tata metro terület azonosítója a Metro terület beolvasására irányuló kérelem Kérés azonosítója metro terület, hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy gyűjteményt, amely tárolja a kérelmeket. Válassza ki a Postman alkalmazást **új**. Az a **hozzon létre új** ablakban válassza **gyűjtemény**. A gyűjtemény neve, és válassza ki a **létrehozás** gombra.

2. A kérelem létrehozásához válassza **új** újra. Az a **hozzon létre új** ablakban válassza **kérelem**. Adjon meg egy **kérelem neve** a kéréshez, válassza ki a gyűjteményt, mint a helyet, ahol a mentse a kérést, majd válassza az előző lépésben létrehozott **mentése**.
    
    ![A Postmanben a kérelem létrehozása](./media/how-to-request-transit-data/postman-new.png)

3. Az első HTTP-metódus a jelentéskészítő lapon válassza ki, és adja meg a hozzon létre egy GET kérelmet a következő URL-CÍMRE.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Miután a kérelem sikeres a következő választ fog kapni:

    ```JSON
    {
        "results": [
            {
                "metroId": "522",
                "metroName": "Seattle–Tacoma–Bellevue, WA",
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
                            [
                                -121.95725,
                                47.18314
                            ],
                            ...,
                            ...,
                            ...,
                            ...,
                            [
                                -122.18711,
                                47.15571
                            ],
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
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                }
            }
        ]
    }
    ```

5. Másolás a `metroId`, a későbbi használat céljából.

## <a name="request-nearby-transit-stops"></a>Kérelem közeli átvitel során leáll

Az Azure Maps [közeli átvitel első](https://aka.ms/AzureMapsMobilityNearbyTransit) service tranzit objektumokat kereshet, melyek például nyilvános átvitel leáll, és megosztott kerékpárok körül egy adott helyen az átvitel során visszatérő objektum részletei. Ezután használunk egy kérelmet a szolgáltatást, hogy keresse meg a 300-mérőszámok radius belül leállítja közeli nyilvános átvitel körül adott helyen. A kérésben tartalmaznia kell a `metroId` a korábban kapott.

A kérheti a [közeli átvitel első](https://aka.ms/AzureMapsMobilityNearbyTransit), kövesse az alábbi lépéseket:

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **közeli első leállítja**.

2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, adja meg a következő kérelem URL-címe az API-végpont, és kattintson **küldése**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Miután a kérelem sikeres a válasz struktúra az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "results": [
            {
                "id": "2060603",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "Ne 24th St & 162nd Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.63241381296315,
                        "longitude": -122.12659096560266
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594172088166,
                        "longitude": -122.12395908007201
                    }
                }
            },
            {
                "id": "2061020",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318791818726,
                        "longitude": -122.12845199584025
                    },
                    "btmRightPoint": {
                        "latitude": 47.63049919323126,
                        "longitude": -122.12582004983427
                    }
                }
            },
            {
                "id": "2060604",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474784183636,
                        "longitude": -122.12912401149087
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655200865796,
                        "longitude": -122.12649203418405
                    }
                }
            }
        ]
    }    
    ```

A válasz struktúra gondosan figyelje meg, ha látható, hogy a benne szereplő paraméterek egyes átvitel objektum, például `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName` és a pozíció (a koordináták) objektum.

Understanding, céljából használjuk az `id` az egyik a busz leáll, az útvonal a következő szakaszban forrásként.  


## <a name="request-a-transit-route"></a>A tranzit útválasztási kérése

Az Azure Maps [első átvitel útvonalak API](https://aka.ms/AzureMapsMobilityTransitRoute) lehetővé teszi, hogy utazás tervezése a lehető legjobb útvonal beállítások visszaadása egy forrás és cél között. Service többféle közlekedési formára, beleértve a the walking, kerékpározáshoz és nyilvános átvitel biztosít. Ezután a budapesti azt fog keresni egy útvonal terület tű legközelebbi bus leállítása.

### <a name="get-location-coordinates-for-destination"></a>A rendeltetési hely koordinátái beolvasása

Hely lekérdezése céljából koordináták terület tű, lehetővé teszi, hogy az Azure Maps használata [intelligens keresési szolgáltatás](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Indítson egy az intelligens keresés szolgáltatás, kövesse az alábbi lépéseket:

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **beolvasása hely koordinátái**.

2.  A jelentéskészítő lapon válassza ki a **első** HTTP-metódus, adja meg a következő kérés URL-címet, és kattintson a **küldése**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Ha alaposan tekintse a választ, az eredmények egynél több helyen a hely tű tartalmazza, és mindegyikhez alatt a hely koordinátái információkat is tartalmaz **pozíció**. Másolás a `lat` és `lon` az első eredményhez pozíciótól.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 61,
            "numResults": 8,
            "offset": 0,
            "totalResults": 24,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/8309323",
                "score": 4.674,
                "info": "search:ta:840539000511573-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "monument"
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
                                    "name": "monument"
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
                    "country": "United States Of America",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
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
    

### <a name="request-route"></a>Kérés útvonala

Indítson egy útvonalat, hajtsa végre az alábbi lépéseket:

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **Get Route info**.

2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, adja meg a következő kérelem URL-címe az API-végpont, és kattintson **küldése**.

    Hogy kérelmezi bus nyilvános átvitel útvonalai megadásával a `modeType` és `transitType` paramétereket. A kérelem URL-címet tartalmaz a helyek, az előző szakaszokban beolvasni. Mint `originType` most **stopId** és az as `destionationType` rendelkezünk a **pozíció**.

    Tekintse meg a [URI-paramétereinek listáját](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) kérelem is használhatja a [első átvitel útvonalak API](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Sikeres kérelem esetén a válasz struktúra az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:0---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:15:53-07:00",
                "travelTimeInSeconds": 3259,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T14:59:47-07:00",
                        "caption": "545",
                        "lengthInMeters": 16441
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:59:48-07:00",
                        "legEndTime": "2019-04-03T15:03:53-07:00",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:03:53-07:00",
                        "legEndTime": "2019-04-03T15:07:26-07:00",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:07:26-07:00",
                        "legEndTime": "2019-04-03T15:12:12-07:00",
                        "caption": "8",
                        "lengthInMeters": 1057
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:13-07:00",
                        "legEndTime": "2019-04-03T15:15:53-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 268
                    }
                ]
            },
            ...,
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:2---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:19:18-07:00",
                "travelTimeInSeconds": 3464,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T15:01:00-07:00",
                        "caption": "545",
                        "lengthInMeters": 17400
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:01:01-07:00",
                        "legEndTime": "2019-04-03T15:04:59-07:00",
                        "caption": "3rd Avenue",
                        "lengthInMeters": 269
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:04:59-07:00",
                        "legEndTime": "2019-04-03T15:09:14-07:00",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:09:14-07:00",
                        "legEndTime": "2019-04-03T15:12:52-07:00",
                        "caption": "33,24",
                        "lengthInMeters": 947
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:53-07:00",
                        "legEndTime": "2019-04-03T15:19:18-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 474
                    }
                ]
            }
        ]
    }
    ```

4. Gondosan figyelje meg van-e több **bus** útvonalak a válaszban. Mindegyik útvonal tartalmaz egyedi **Útiterv azonosító** és összegzését, amelyek az útvonal minden szakasza ismerteti. Ezután azt kérni fogja a leggyorsabb útvonalat a részletek a `itineraryId` a válaszban.

## <a name="request-fastest-route-itinerary"></a>Kérelem leggyorsabb útvonalat Útiterv

Az Azure Maps [első átvitel Útiterv](https://aka.ms/AzureMapsMobilityTransitItinerary) szolgáltatás lehetővé teszi egy adott útvonalat az útvonal használatával az adatok **Útiterv azonosító** által visszaadott a [első átvitel útvonalak API](https://aka.ms/AzureMapsMobilityTransitRoute) a szolgáltatás. A kérést, hajtsa végre az alábbi lépéseket:

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **átvitel Get info**.

2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, adja meg a következő kérelem URL-címe az API-végpont, és kattintson **küldése**.

    Állítjuk be a `detailType` paramétert **geometriai** , hogy a válasz stop információkat tartalmaz, nyilvános átvitel és a navigáció kapcsolja kapcsolja be a megtudhatja, hogyan és kerékpár az útvonal szakaszán.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Sikeres kérelem esetén a válasz struktúra az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
    "departureTime": "2019-05-01T11:16:56-07:00",
    "arrivalTime": "2019-05-01T12:23:45-07:00",
    "legs": [
                {
                    "legType": "Walk",
                    "legStartTime": "2019-05-01T11:16:56-07:00",
                    "legEndTime": "2019-05-01T11:24:06-07:00",
                    "walkingSteps": [
                        {
                            "direction": {
                                "relativeDirection": "left"
                            },
                            "streetName": "Northeast 24th Street"
                        },
                        {
                            "direction": {
                                "relativeDirection": "right"
                            },
                            "streetName": "156th Avenue Northeast"
                        }
                    ],
                    "walkingOrigin": {
                        "latitude": 47.63096,
                        "longitude": -122.126
                    },
                    "walkingDestination": {
                        "latitude": 47.631843,
                        "longitude": -122.132294
                    },
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.126,
                                47.63096
                            ],
                            [
                                -122.12645,
                                47.63099
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.63184
                            ]
                        ]
                    }
                },
                {
                    "legType": "Wait",
                    "legStartTime": "2019-05-01T11:24:06-07:00",
                    "legEndTime": "2019-05-01T11:25:07-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "waitOnVehicle": "false"
                },
                {
                    "legType": "Bus",
                    "legStartTime": "2019-05-01T11:25:07-07:00",
                    "legEndTime": "2019-05-01T11:30:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        ...,
                        ...,
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.13235,
                                47.63184
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.64398
                            ]
                        ]
                    }
                },
                ...,
                ...,
                ...,
                {
                    "legType": "Tram",
                    "legStartTime": "2019-05-01T12:20:00-07:00",
                    "legEndTime": "2019-05-01T12:22:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 4083239,
                        "agencyId": 1360766,
                        "agencyName": "Seattle Monorail",
                        "lineNumber": "Monorail",
                        "caption1": "Seattle Center - Westlake Center",
                        "caption2": "MONORAIL Seattle Center - Westlake Center",
                        "color": "00AEEF",
                        "transitType": "Tram"
                    },
                    "line": {
                        "lineId": 3769726,
                        "lineGroupId": 4083239,
                        "direction": "backward",
                        "agencyId": 1360766,
                        "lineNumber": "Monorail",
                        "destination": "Seattle Center"
                    },
                    "stops": [
                        {
                            "stopId": 32962125,
                            "stopName": "Westlake Station",
                            "position": {
                                "latitude": 47.611417,
                                "longitude": -122.337089
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        },
                        {
                            "stopId": 32962134,
                            "stopName": "Seattle Center",
                            "position": {
                                "latitude": 47.62123,
                                "longitude": -122.349746
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.3369,
                                47.61201
                            ],
                            ...,
                            ...,
                            [
                                -122.34973,
                                47.6212
                            ]
                        ]
                    }
                },
                {
                    "legType": "PathWayWalk",
                    "legStartTime": "2019-05-01T12:22:00-07:00",
                    "legEndTime": "2019-05-01T12:23:45-07:00"
                }
          ]
    }
    ```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kérhetnek a mobilitási szolgáltatás használatával valós idejű adatokat:

> [!div class="nextstepaction"]
> [Hogyan kérhetnek a valós idejű adatok](how-to-request-real-time-data.md)

Ismerkedés az Azure Maps a mobilitási szolgáltatás API-dokumentáció

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatás API-dokumentáció](https://aka.ms/AzureMapsMobilityService)

