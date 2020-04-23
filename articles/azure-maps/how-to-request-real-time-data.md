---
title: Valós idejű tömegközlekedési adatok kérése | Microsoft Azure Maps
description: Valós idejű tömegközlekedési adatok kérése a Microsoft Azure Maps mobilitási szolgáltatásával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086077"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Valós idejű tömegközlekedési adatok kérése az Azure Maps mobilitási szolgáltatásával

Ez a cikk bemutatja, hogyan használhatja az Azure Maps [mobilitási szolgáltatást](https://aka.ms/AzureMapsMobilityService) valós idejű tömegközlekedési adatok kéréséhez.

Ebben a cikkben megtudhatja, hogyan kérheti a következő valós idejű érkezéseket az adott megállóba érkező összes vonalra

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

## <a name="next-steps"></a>További lépések

További információ az átviteli adatok mobilszolgáltatóval történő igényléséhez:

> [!div class="nextstepaction"]
> [Az átutazási adatok kérése](how-to-request-transit-data.md)

Ismerje meg az Azure Maps mobilitási szolgáltatás API dokumentációját:

> [!div class="nextstepaction"]
> [A Mobility Service API dokumentációja](https://aka.ms/AzureMapsMobilityService)
