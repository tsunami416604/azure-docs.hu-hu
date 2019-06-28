---
title: Azure Maps-valós idejű adatok kérésének |} A Microsoft Docs
description: Az Azure Maps a mobilitási szolgáltatás használatával valós idejű adatokat kér.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329663"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Az Azure Maps mobilitási szolgáltatás használatával valós idejű adatok kérése

Ez a cikk bemutatja, hogyan használható az Azure Maps [a mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService) adatokat kérjen a valós idejű továbbításához.

Ez a cikk azt ismerteti, hogyan lehet:


 * A megadott stop érkező összes vonalához tovább valós idejű érkezők kérése
 * A megadott kerékpárt dokkolóegységről valós idejű adatok kérése.


## <a name="prerequisites"></a>Előfeltételek

Bármely hívásokat az Azure Maps nyilvános átviteli API-k, szüksége van egy Maps-fiók és a kulcsot. Hozzon létre egy fiókot, és a egy kulcs lekérése kapcsolatos tudnivalókat lásd: [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md).

Ez a cikk a [Postman alkalmazás](https://www.getpostman.com/apps) hozhat létre REST-hívások. Minden API fejlesztési környezetet, amely igény szerint is használhatja.


## <a name="request-real-time-arrivals-for-a-stop"></a>Valós idejű érkezők kérelem a stop

Annak érdekében, hogy egy adott nyilvános átvitel állítsa le a valós idejű beérkező kérelmek adatokat kér, kell kérheti a [valós idejű beérkező kérelmek API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) az Azure Maps [a mobilitási szolgáltatás](https://aka.ms/AzureMapsMobilityService). Szüksége lesz a **metroID** és **stopID** a kérés teljesítéséhez. Ezeket a paramétereket kérésének kapcsolatos további információkért lásd az útmutató végigvezeti [nyilvános átvitel útvonalak kérelem](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Használjuk a "522" lehetőséget, hogy metro azonosítója, amely a "Seattle – Tata – Bellevue, WA" területen, és használja a stop-azonosító "2060603", amely egy bus azonosítója állni metro "új 24th St & 162nd Ave Ne, Bellevue WA". A következő öt valós idejű beérkező kérelmek adatokat kér az összes következő élő beérkező kérelmek, a Leállítás, a következő lépéseket:

1. Hozzon létre egy gyűjteményt, amely tárolja a kérelmeket. Válassza ki a Postman alkalmazást **új**. Az a **hozzon létre új** ablakban válassza **gyűjtemény**. A gyűjtemény neve, és válassza ki a **létrehozás** gombra.

2. A kérelem létrehozásához válassza **új** újra. Az a **hozzon létre új** ablakban válassza **kérelem**. Adjon meg egy **kérelem neve** a kéréshez, válassza ki a gyűjteményt, mint a helyet, ahol a mentse a kérést, majd válassza az előző lépésben létrehozott **mentése**.

    ![A Postmanben a kérelem létrehozása](./media/how-to-request-transit-data/postman-new.png)

3. Az első HTTP-metódus a jelentéskészítő lapon válassza ki, és adja meg a hozzon létre egy GET kérelmet a következő URL-CÍMRE.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Miután a kérelem sikeres a következő választ fog kapni.  Figyelje meg, hogy scheduleType paramétert határoz meg, hogy statikus vagy valós idejű adatok alapján a becsült érkezési ideje.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Valós idejű adatok kerékpárt dokkolóegység

A [első átvitel Dock adatokat API](https://aka.ms/AzureMapsMobilityTransitDock) az Azure Maps mobilitási szolgáltatás lehetővé teszi, hogy a statikus és valós idejű információk, például a rendelkezésre állás és a egy adott kerékpárt vagy scooter dokkolásának Betöltetlen állás adatait. Valós idejű adatokat beolvasni a dokkolóegységről kerékpárok használunk.

Annak érdekében, hogy egy kérést az első átvitel Dock Info API-hoz, szüksége lesz a **dockId** ennél az állomásnál. Azáltal, hogy egy keresési kérelmet, hogy a rögzítési hely Azonosítójának lekéréséhez a [közeli átvitel API első](https://aka.ms/AzureMapsMobilityNearbyTransit) és a beállítás a **objectType** "bikeDock" paramétert. Kerékpár dokkolóegységről, valós idejű adatokat lekérni az alábbi lépésekkel.


### <a name="get-dock-id"></a>Állapotazonosító beolvasása

Az első **dockID**, használatával indítson egy közeli átvitel beolvasása API az alábbi lépésekkel:

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **Get dock azonosító**.

2.  A jelentéskészítő lapon válassza ki a **első** HTTP-metódus, adja meg a következő kérés URL-címet, és kattintson a **küldése**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Miután a kérelem sikeres a következő választ fog kapni. Figyelje meg, hogy most a **azonosító** a válaszban, amellyel később az első átvitel Dock Info API a kérelem lekérdezési paramétert.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>Valós idejű kerékpárt dock állapotának beolvasása

Indítson egy az első átvitel Dock Info API-t valós idejű adatok beszerzése a kijelölt dock az alábbi lépésekkel.

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **valós idejű dock adatokat**.

2.  A jelentéskészítő lapon válassza ki a **első** HTTP-metódus, adja meg a következő kérés URL-címet, és kattintson a **küldése**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. A kérelem sikeres, után kapni fog egy válasz az alábbi struktúra:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan igényelhető az átvitt adatok a mobilitási szolgáltatás használatával:

> [!div class="nextstepaction"]
> [Hogyan kérhetnek az átvitt adatok](how-to-request-transit-data.md)

Az Azure Maps a mobilitási szolgáltatás API-dokumentáció felfedezése:

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatás API-dokumentáció](https://aka.ms/AzureMapsMobilityService)
