---
title: Valós idejű továbbítási adatforgalom kérelmezése | Microsoft Azure térképek
description: A Microsoft Azure Maps mobilitási szolgáltatással valós idejű adatfeldolgozást igényelhet.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9710366bdb7d8e86c8abb54b29b8dde3cc315692
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209902"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Valós idejű adatkérés a Azure Maps mobilitási szolgáltatás használatával

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Maps [mobilitási szolgáltatást](https://aka.ms/AzureMapsMobilityService) a valós idejű adatátvitelek igényléséhez.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:


 * A következő valós idejű érkezések kérése az adott leállítás során beérkező összes sorra
 * Valós idejű információk kérése egy adott Bike Docker-állomásról.


## <a name="prerequisites"></a>Előfeltételek

Először rendelkeznie kell egy Azure Maps-fiókkal és egy előfizetési kulccsal, hogy bármilyen hívást lehessen kezdeményezni a Azure Maps nyilvános átviteli API-khoz. További információért kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) Azure Maps fiók létrehozásához című témakör utasításait. A fiók elsődleges kulcsának beszerzéséhez kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakör lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).


Ez a cikk a [Poster alkalmazást](https://www.getpostman.com/apps) használja a REST-hívások létrehozásához. Bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.


## <a name="request-real-time-arrivals-for-a-stop"></a>Valós idejű érkezések igénylése leállításhoz

Egy adott nyilvános adatátviteli leálláshoz tartozó valós idejű beérkező adatok igényléséhez a Azure Maps [mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService) [valós idejű beérkezési API-](https://aka.ms/AzureMapsMobilityRealTimeArrivals) ját kell kérnie. A kérelem elvégzéséhez szüksége lesz a **metroID** és a **stopID** . Ha többet szeretne megtudni ezekről a paraméterekről, tekintse meg a [nyilvános átviteli útvonalak igénylését](https://aka.ms/AMapsHowToGuidePublicTransitRouting)ismertető útmutatót. 

A "522" a Metro ID-ként használjuk, amely a "Seattle – Tacoma – Bellevue, WA" területen található Metro-azonosító. A "522---2060603" érték leállítási AZONOSÍTÓként való használata esetén ez a buszmegálló a következő címen érhető el: "ne 24 St & 162nd Ave ne, Bellevue WA". Ha a következő öt valós idejű beérkező adatbevitelt szeretné kérelmezni, az összes következő élő érkezésnél végezze el a következő lépéseket:

1. Nyissa meg a Poster alkalmazást, és hozzon létre egy gyűjteményt a kérések tárolásához. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény**elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget. Adja meg a kérelem **nevét** . Válassza ki azt a gyűjteményt, amelyet az előző lépésben hozott létre, a kérelem mentési helyeként. Ezt követően válassza a **Mentés** lehetőséget.

    ![Kérelem létrehozása a Poster-ban](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a http **beolvasása** metódust a Builder (szerkesztő) lapon, majd a Get kérelem létrehozásához adja meg a következő URL-címet. Cserélje le a `{subscription-key}`t a Azure Maps elsődleges kulcsára.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Sikeres kérés után a következő válasz jelenik meg.  Figyelje meg, hogy a "Scheduletype értéke" paraméter határozza meg, hogy a becsült beérkezési idő valós idejű vagy statikus adatértéken alapul-e.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>A bike Docker-állomás valós idejű adatvédelme

A [továbbítási Dock info API](https://aka.ms/AzureMapsMobilityTransitDock) -val a felhasználók statikus és valós idejű információkat igényelhetnek. A felhasználók például igényelhetnek rendelkezésre állási és üresedési információt egy kerékpárhoz vagy egy robogós dokkolóegységhez. A [Transit Dock info API](https://aka.ms/AzureMapsMobilityTransitDock) a Azure Maps [mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService)részét képezi.

Ahhoz, hogy kérést [kapjon a tranzit Dock info API](https://aka.ms/AzureMapsMobilityTransitDock)-hoz, szüksége lesz az adott állomás **dockId** . A Dock ID-t úgy érheti el, hogy keresési kérelmet küld a [közeli tranzit API](https://aka.ms/AzureMapsMobilityNearbyTransit) -hoz a "bikeDock" elemhez rendelt **objektumtípus** paraméterrel. Az alábbi lépéseket követve szerezzen be egy Docker-állomást a Bikes szolgáltatáshoz.


### <a name="get-dock-id"></a>Dock AZONOSÍTÓjának lekérése

A **dockID**beszerzéséhez kövesse az alábbi lépéseket, hogy kérést kapjon a közeli tranzit API-hoz:

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és NEVEZZE el a **Dock ID**-t.

2.  A Builder (szerkesztő) lapon válassza a http **beolvasása** metódust, adja meg a kérelem URL-címét, majd kattintson a **Küldés**gombra.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Sikeres kérés után a következő válasz jelenik meg. Figyelje meg, hogy most már rendelkezik a válaszban szereplő **azonosítóval** , amely később lekérdezési paraméterként is használható a továbbítási Dock info API-nak küldött kérelemben.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Valós idejű Bike Dock-állapot beolvasása

Az alábbi lépésekkel kérheti le a tranzit Dock info API beszerzését, hogy valós idejű adatokat kapjon a kiválasztott dockhoz.

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze el a **valós idejű Dock-** adatkérést.

2.  A Builder (szerkesztő) lapon válassza a http **beolvasása** metódust, adja meg a kérelem URL-címét, majd kattintson a **Küldés**gombra.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Sikeres kérés után a következő struktúra választ kap:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kérhet továbbítási információkat a mobilitási szolgáltatással:

> [!div class="nextstepaction"]
> [Adatátviteli adatkérés](how-to-request-transit-data.md)

Ismerje meg a Azure Maps mobilitási szolgáltatás API-dokumentációját:

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatás API-dokumentációja](https://aka.ms/AzureMapsMobilityService)
