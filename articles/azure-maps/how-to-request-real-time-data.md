---
title: Valós idejű árutovábbítási adatok kérése | Microsoft Azure Maps
description: Valós idejű adatok at kérhet a Microsoft Azure Maps mobilitási szolgáltatásával.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335486"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Valós idejű adatok kérése az Azure Maps mobilitási szolgáltatásával

Ez a cikk bemutatja, hogyan használhatja az Azure Maps [mobilitási szolgáltatást](https://aka.ms/AzureMapsMobilityService) valós idejű átviteli adatok kéréséhez.

Ebben a cikkben megtudhatja, hogyan:


 * A következő valós idejű érkezés kérése az adott megállóba érkező összes vonalra
 * Valós idejű információk kérése egy adott kerékpárdokkoló állomásról.


## <a name="prerequisites"></a>Előfeltételek

Először rendelkeznie kell egy Azure Maps-fiókkal és egy előfizetési kulccsal az Azure Maps tömegközlekedési API-khoz való hívásához. További információkért kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) egy Azure Maps-fiók létrehozásához található utasításokat. Kövesse az [elsődleges kulcs beszerzése](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseket a fiók elsődleges kulcsának beszerzéséhez. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.


Ez a cikk a [Postman alkalmazást](https://www.getpostman.com/apps) használja rest-hívások létrehozásához. Bármilyen API-fejlesztői környezetet használhat, amelyet szeretne.


## <a name="request-real-time-arrivals-for-a-stop"></a>Valós idejű érkezések kérése a megálláshoz

Ahhoz, hogy egy adott tömegközlekedési megálló valós idejű érkezési adatait kérhesse, kérelmet kell benyújtania az Azure Maps mobilitási szolgáltatás [valós idejű érkezési API-jára.](https://aka.ms/AzureMapsMobilityRealTimeArrivals) [Mobility Service](https://aka.ms/AzureMapsMobilityService) A kérelem teljesítéséhez szüksége lesz a **metroazonosítóra** és a **stopId-ra.** Ha többet szeretne megtudni aparaméterek igényléséről, olvassa el útmutatónkat a [tömegközlekedési útvonalak igényléséről.](https://aka.ms/AMapsHowToGuidePublicTransitRouting) 

Használjuk az "522"-t metróazonosítóként, amely a "Seattle-Tacoma–Bellevue, WA" terület metróazonosítója. Használja az "522---2060603"-ot megállóazonosítóként, ez a buszmegálló a "Ne 24th St & 162nd Ave Ne, Bellevue WA" megállónál található. A következő öt valós idejű érkezési adat igényléséhez a következő élő érkezések hez hajtsa végre a következő lépéseket:

1. Nyissa meg a Postman alkalmazást, és hozzon létre egy gyűjteményt a kérések tárolásához. A Postman alkalmazás tetején válassza az **Új**lehetőséget. Az **Új létrehozása** ablakban válassza a **Gyűjtemény**lehetőséget.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza ismét az **Új** lehetőséget. Az **Új létrehozása** ablakban válassza a **Kérés lehetőséget.** Adja meg a **kérelem nevét.** Válassza ki az előző lépésben létrehozott gyűjteményt, mint a kérés mentésének helyét. Ezután válassza a **Mentés gombot.**

    ![Kérelem létrehozása a Postman ben](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a **GET** HTTP metódust a szerkesztő lapon, és írja be a következő URL-címet a GET-kérelem létrehozásához. Cserélje `{subscription-key}`le az Azure Maps elsődleges kulcsát.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Sikeres kérés után a következő választ kapja.  Figyelje meg, hogy a "scheduleType" paraméter határozza meg, hogy a becsült érkezési idő valós idejű vagy statikus adatokon alapul-e.

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


## <a name="real-time-data-for-bike-docking-station"></a>Valós idejű adatok a kerékpárdokkoló állomáshoz

A [Szállítási dokkoló adatok bekérése API](https://aka.ms/AzureMapsMobilityTransitDock) lehetővé teszi a felhasználók számára, hogy statikus és valós idejű információkat kérjenek. A felhasználók például kérhetnek rendelkezésre állási és üresedési információkat egy kerékpárhoz vagy egy robogó-dokkolóállomáshoz. A [Kézbesítési dock-adatok beszerezése API](https://aka.ms/AzureMapsMobilityTransitDock) szintén az Azure Maps [mobilitási szolgáltatásrésze.](https://aka.ms/AzureMapsMobilityService)

A [Szállítási dokkoló adatok bekérése API-hoz](https://aka.ms/AzureMapsMobilityTransitDock)való kérelem benyújtásához az adott állomás **dokkolóazonosítójára** lesz szüksége. A dock-id beszerezhető, ha keresési kérelmet küld a [Közeli tranzit API bekérése](https://aka.ms/AzureMapsMobilityNearbyTransit) a "bikeDock"-hoz rendelt **objectType** paraméterrel. Az alábbi lépéseket követve valós idejű adatokat kaphat a kerékpárdokkolóállomásról.


### <a name="get-dock-id"></a>Dokkolóazonosító beszerezése

A **dockID**lekéréséhez kövesse az alábbi lépéseket a Közeli tranzit bekérése API-hoz:

1. A Postman alkalmazásban kattintson **az Új kérelem** | **get kérésére,** és nevezze el **a Dock ID-t.**

2.  A Szerkesztő lapon válassza a **GET** HTTP metódust, írja be a következő kérelem URL-címét, majd kattintson a **Küldés gombra.**
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Sikeres kérés után a következő választ kapja. Figyelje meg, hogy most már rendelkezik az **azonosítót** a válaszban, amely később használható lekérdezési paraméterként a kérelemben a Get Transit Dock Info API-ban.

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


### <a name="get-real-time-bike-dock-status"></a>Valós idejű kerékpárdokk-állapot

Az alábbi lépéseket követve kérjen kérelmet a Szállítási dokkoló adatainak bekérése API-hoz a kiválasztott dokkvalós idejű adatainak bekéréséhez.

1. A Postman alkalmazásban kattintson **az Új kérelem** | **get kérésére,** és nevezze **el, hogy valós idejű dokkolóadatokat kapjon.**

2.  A Szerkesztő lapon válassza a **GET** HTTP metódust, írja be a következő kérelem URL-címét, majd kattintson a **Küldés gombra.**
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. A sikeres kérés után a következő struktúra választ kap:

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


## <a name="next-steps"></a>További lépések

További információ az átviteli adatok mobilszolgáltatóval történő igényléséhez:

> [!div class="nextstepaction"]
> [Az átutazási adatok kérése](how-to-request-transit-data.md)

Ismerje meg az Azure Maps mobilitási szolgáltatás API dokumentációját:

> [!div class="nextstepaction"]
> [A Mobility Service API dokumentációja](https://aka.ms/AzureMapsMobilityService)
