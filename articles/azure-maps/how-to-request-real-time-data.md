---
title: Valós idejű továbbítási adatforgalom kérelmezése | Microsoft Azure térképek
description: A Microsoft Azure Maps mobilitási szolgáltatással valós idejű adatfeldolgozást igényelhet.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 169764f015f332d07c21ef815e6044c653489774
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911433"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Valós idejű adatkérés a Azure Maps mobilitási szolgáltatás használatával

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Maps [mobilitási szolgáltatást](https://aka.ms/AzureMapsMobilityService) a valós idejű adatátvitelek igényléséhez.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:


 * A következő valós idejű érkezések kérése az összes olyan sorra, amely az adott leállítás alkalmával érkezik
 * Valós idejű információk kérése egy adott Bike Docker-állomásról.


## <a name="prerequisites"></a>Előfeltételek

Ha bármilyen hívást szeretne tenni a Azure Maps nyilvános átviteli API-khoz, szüksége van egy Maps-fiókra és egy kulcsra. A fiókok létrehozásával és a kulcsok lekérésével kapcsolatos információkért kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) egy Azure Maps fiók előfizetésének létrehozásához című témakör útmutatását, és kövesse az [elsődleges kulcs lekérése](quick-demo-map-app.md#get-the-primary-key-for-your-account) a fiók elsődleges kulcsának lekérése című részben leírt lépéseket. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).


Ez a cikk a [Poster alkalmazást](https://www.getpostman.com/apps) használja a REST-hívások létrehozásához. Bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.


## <a name="request-real-time-arrivals-for-a-stop"></a>Valós idejű érkezések igénylése leállításhoz

Egy adott nyilvános továbbítási időszakra vonatkozó valós idejű beérkező adatok igényléséhez a Azure Maps [mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService) [valós idejű beérkezési API-](https://aka.ms/AzureMapsMobilityRealTimeArrivals) ját kell kérnie. A kérelem elvégzéséhez szüksége lesz a **metroID** és a **stopID** . Ha többet szeretne megtudni ezekről a paraméterekről, tekintse meg az útmutató a [nyilvános átviteli útvonalak igényléséhez](https://aka.ms/AMapsHowToGuidePublicTransitRouting)című témakört. 

A "522" Metro ID-t használjuk, amely a "Seattle – Tacoma – Bellevue, WA" területen található Metro ID, és a "522---2060603" leállítási AZONOSÍTÓval rendelkezik, amely egy buszmegálló a "ne 24 St & 162nd Ave ne, Bellevue WA" címen. Ha a következő öt valós idejű beérkező adatbevitelt szeretné leállítani a következő élő beérkezések esetében, hajtsa végre a következő lépéseket:

1. Hozzon létre egy gyűjteményt, amelyben tárolni szeretné a kérelmeket. A Poster alkalmazásban válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény**elemet. Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget. Adja meg a kérelem **nevét** , válassza ki az előző lépésben létrehozott gyűjteményt a kérelem mentési helyeként, majd kattintson a **Mentés**gombra.

    ![Kérelem létrehozása a Poster-ban](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a HTTP beolvasása metódust a Builder (szerkesztő) lapon, majd a GET kérelem létrehozásához adja meg a következő URL-címet.

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

Az Azure Maps mobilitási szolgáltatás [továbbítási Dock info API](https://aka.ms/AzureMapsMobilityTransitDock) -ját lehetővé teszi a statikus és valós idejű információk, például a rendelkezésre állási és a rendelkezésre állási információk kérését egy adott kerékpár vagy robogós dokkolóegység számára. A rendszer bekéri a beérkező állomások valós idejű adatgyűjtését a kerékpárok számára.

Ahhoz, hogy kérést kapjon a tranzit Dock info API-hoz, szüksége lesz az adott állomás **dockId** . A Dock ID-t úgy érheti el, hogy keresési kérelmet küld a [közeli tranzit API](https://aka.ms/AzureMapsMobilityNearbyTransit) -hoz, és a **objektumtípus** paramétert "bikeDock" értékre állítja. Az alábbi lépéseket követve szerezzen be egy Docker-állomást a Bikes szolgáltatáshoz.


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
