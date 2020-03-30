---
title: Tranzitadatok kérése | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan kérhet tömegközlekedési adatokat a Microsoft Azure Térkép mobilitási szolgáltatáshasználatával.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335459"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Tömegközlekedési adatok kérése az Azure Maps mobilitási szolgáltatásával 

Ez a cikk bemutatja, hogyan használhatja az Azure Maps [mobilitási szolgáltatást](https://aka.ms/AzureMapsMobilityService) tömegközlekedési adatok kérésére. Az átutazási adatok közé tartoznak a tranzitmegállók, az útvonalinformációk és az utazási idő becslései.

Ebben a cikkben megtudhatja, hogyan:

* Metro area beszerez és kap egy metróterület-azonosítót a [Metro Terület beszerezése API-val](https://aka.ms/AzureMapsMobilityMetro)
* Igényeljön közeli tranzitmegállókat a [Közeli tömegközlekedési](https://aka.ms/AzureMapsMobilityNearbyTransit) szolgáltatás bekérése szolgáltatással.
* A [Tranzitútvonalak API behívása lekérdezése](https://aka.ms/AzureMapsMobilityTransitRoute) az útvonal tömegközlekedéssel történő megtervezéséhez.
* A Tranzitútvonal-útvonal geometriájának kérése és az útvonal részletes ütemezése a [Szállítási útvonal bekérése API használatával.](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)


## <a name="prerequisites"></a>Előfeltételek

Először rendelkeznie kell egy Azure Maps-fiókkal és egy előfizetési kulccsal az Azure Maps tömegközlekedési API-khoz való hívásához. További információkért kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) egy Azure Maps-fiók létrehozásához található utasításokat. Kövesse az [elsődleges kulcs beszerzése](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseket a fiók elsődleges kulcsának beszerzéséhez. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.


Ez a cikk a [Postman alkalmazást](https://www.getpostman.com/apps) használja rest-hívások létrehozásához. Bármilyen API-fejlesztői környezetet használhat, amelyet szeretne.


## <a name="get-a-metro-area-id"></a>Metróterület-azonosító beszerezni

Ahhoz, hogy tranzitinformációt kérjen egy adott nagyvárosi `metroId` területről, szüksége lesz az adott területre. A [Metro Terület beszerzése API](https://aka.ms/AzureMapsMobilityMetro) lehetővé teszi, hogy metro területeket kérjen, ahol az Azure Maps mobilitási szolgáltatás elérhető. A válasz olyan részleteket tartalmaz, mint a `metroId`, `metroName`és a metro terület geometriájának ábrázolása GeoJSON formátumban.

Kérjük, hogy a Seattle-Tacoma metróterület hez. Ha egy metróterület azonosítóját szeretné kérni, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Postman alkalmazást, és hozzon létre egy gyűjteményt a kérések tárolásához. A Postman alkalmazás tetején válassza az **Új**lehetőséget. Az **Új létrehozása** ablakban válassza a **Gyűjtemény**lehetőséget.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza ismét az **Új** lehetőséget. Az **Új létrehozása** ablakban válassza a **Kérés lehetőséget.** Adja meg a **kérelem nevét.** Jelölje ki az előző lépésben létrehozott gyűjteményt a kérelem mentési helyeként. Ezután válassza a **Mentés gombot.**
    
    ![Kérelem létrehozása a Postman ben](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a **GET** HTTP metódust a szerkesztő lapon, és írja be a következő URL-címet a GET-kérelem létrehozásához. Cserélje `{subscription-key}`le az Azure Maps elsődleges kulcsát.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. A sikeres kérés után a következő választ kapja:

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

5. Másolja `metroId`a , meg kell használni később.

## <a name="request-nearby-transit-stops"></a>Közeli tömegközlekedési megállók kérése

Az Azure Maps [Betöltő közeli tranzit](https://aka.ms/AzureMapsMobilityNearbyTransit) szolgáltatás lehetővé teszi a tranzitobjektumok keresését.  az API visszaadja a tranzitobjektum adatait, például a tömegközlekedési megállókat és a megosztott kerékpárokat egy adott hely körül. Ezután kérést küldünk a szolgálathoz, hogy 300 méteres sugarú körben keressenek a közeli tömegközlekedési megállókat az adott hely körül. A kérelemben a korábban `metroId` lekért kéréseket is bele kell foglalnunk.

Ha a Közeli [tömegközlekedés bekérése](https://aka.ms/AzureMapsMobilityNearbyTransit)című területre szeretne irányuló kérelmet benyújtani, kövesse az alábbi lépéseket:

1. A Postman alkalmazásban kattintson az Új kérelem | get**kérésére,** és nevezze el a Get **Nearby stops .Postman** **(Postman)**(Postman) (Postman) (Postman) (Postman) (Postman) (Postman) kattints

2. A Szerkesztő lapon válassza a **GET** HTTP metódust, írja be az API-végpont következő kérelem URL-címét, és kattintson a **Küldés gombra.**

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Sikeres kérés után a válaszstruktúrának az alábbihoz hasonlóan kell kinéznie:

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

Ha gondosan megfigyeli a válaszszerkezetet, látni fogja, hogy az minden tranzitobjektum paramétereit tartalmazza. Minden tranzitobjektumnak vannak `id`paraméterei, például `type`, , `stopName`, `mainTransitType`, `mainAgencyName`, és az objektum koordinátáiban elfoglalt helyzete.

A tanulás céljából a következő `id` szakaszban lévő útvonalunkhoz egy buszmegállót fogunk használni kiindulási pontként.  


## <a name="request-a-transit-route"></a>Tranzitútvonal kérése

Az Azure Maps [szállítási útvonalak bekerülési útvonalai API](https://aka.ms/AzureMapsMobilityTransitRoute) lehetővé teszi az utazás tervezését. A lehető legjobb útvonalbeállításokat adja vissza az orig a célig. A szolgáltatás különböző típusú utazási módokat kínál, beleértve a gyaloglást, a kerékpározást és a tömegközlekedést. Ezután átkutatunk egy útvonalat a legközelebbi buszmegállótól a Seattle-i Space Needle toronyig.

### <a name="get-location-coordinates-for-destination"></a>Helykoordináták beszerezése a célhoz

A Space Needle torony helykoordinátáinak beszerzéséhez használja az Azure Maps [Fuzzy Search Service szolgáltatást.](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Ha a Fuzzy keresési szolgáltatáshoz szeretne kérelmet benyújtani, kövesse az alábbi lépéseket:

1. A Postman alkalmazásban kattintson az **Új kérelem** | **get kérésére,** és nevezze el **a Helykoordináták bekérése .**

2.  A Szerkesztő lapon válassza a **GET** HTTP metódust, írja be a következő kérelem URL-címét, majd kattintson a **Küldés gombra.**
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Ha figyelmesen megnézi a választ, több helyet tartalmaz a Space Needle keresés eredményeiközött. Minden eredmény tartalmazza a helykoordinátákat a **pozíció**alatt. Másolja `lat` az `lon` első eredmény helyére és **helyére.**
    
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
    

### <a name="request-route"></a>Útvonal kérése

Az útvonalkéréshez hajtsa végre az alábbi lépéseket:

1. A Postman alkalmazásban kattintson az **Új kérelem** | **get kérésére,** és adja meg az **Útvonaladatainak bekérése**.

2. A Szerkesztő lapon válassza a **GET** HTTP metódust, írja be az API-végpont következő kérelem URL-címét, és kattintson a **Küldés gombra.**

    A busz tömegközlekedési útvonalait a paraméterek `modeType` `transitType` és a paraméterek megadásával kérjük. A kérelem URL-címe az előző szakaszokban beolvasott helyeket tartalmazza. A `originType`, most már van egy **stopId**. És a, `destionationType`megvan a **helyzet**.

    Tekintse meg a kérelemben a [Tranzitútvonalak bekérése](https://aka.ms/AzureMapsMobilityTransitRoute) [API-hoz használható URI-paraméterek listáját.](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Sikeres kérés esetén a válaszstruktúrának az alábbihoz hasonlóan kell kinéznie:

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

4. Ha figyelmesen megfigyeli, több **buszjárat** van a válaszban. Minden útvonal egyedi **útvonalazonosítóval** és egy összefoglalóval rendelkezik, amely leírja az útvonal egyes szakaszait. Az útvonalszakasz a két megállóútpont közötti útvonal része. Ezután a `itineraryId` válaszban a leggyorsabb útvonal adatait kérjük.

## <a name="request-fastest-route-itinerary"></a>Leggyorsabb útvonal-útvonal igénylése

Az Azure Maps [beérkeztési útvonala](https://aka.ms/AzureMapsMobilityTransitItinerary) szolgáltatás lehetővé teszi, hogy adatokat kérjen egy adott útvonalhoz az útvonal **útvonal-azonosítójának** használatával, amelyet a [Szállítási útvonalak bekérése API-szolgáltatás](https://aka.ms/AzureMapsMobilityTransitRoute) ad vissza. Kérés hez hajtsa végre az alábbi lépéseket:

1. A Postman, kattintson **az Új kérelem** | **GET kérelmet,** és nevet **kap Transit info**.

2. A Szerkesztő lapon válassza a **GET** HTTP metódust. Adja meg az API-végpont következő kérelem URL-címét, majd kattintson a **Küldés**gombra.

    A `detailType` paramétert **geometriára** állítjuk, hogy a válasz tartalmazza a tömegközlekedés stop információit és a csomóponti navigációt az útvonal séta- és kerékpárlábaihoz.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Sikeres kérés esetén a válaszstruktúrának az alábbihoz hasonlóan kell kinéznie:

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

Ismerje meg, hogyan kérhet valós idejű adatokat a Mobility Service használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatok igénylése](how-to-request-real-time-data.md)

Az Azure Maps Mobility Service API dokumentációjának felfedezése

> [!div class="nextstepaction"]
> [A Mobility Service API dokumentációja](https://aka.ms/AzureMapsMobilityService)

