---
title: "Azure-alapú helyszolgáltatás keresési útvonalat |} Microsoft Docs"
description: "Útvonal-használatával, Azure-alapú helyszolgáltatás pontra"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f2be9ca98330866ac8b6fb12efd56efdc711eedf
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Útvonal-használatával, Azure-alapú helyszolgáltatás pontra

Ez az oktatóanyag bemutatja, hogyan használja az Azure-alapú helyszolgáltatás fiókját és az útválasztási szolgáltatás SDK érdeklő pontjának az útvonal kereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Cím koordináták beolvasása
> * Lekérdezési érdeklő ponthoz utasításokat az útvonal-szolgáltatás

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, győződjön meg arról, hogy [létre Azure-alapú helyszolgáltatás fiókját](./tutorial-search-location.md#createaccount), és [fiókja előfizetés kulcs lekérése](./tutorial-search-location.md#getkey). A térkép vezérlőelem és a keresési szolgáltatás API-k használata az oktatóanyag leírtaknak megfelelően is jelenhet meg [keresési közelben pont használatával, Azure-alapú helyszolgáltatás](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Cím koordináták beolvasása

Az alábbi lépések segítségével hozzon létre egy statikus HTML-lapot a hely alapú szolgáltatások térkép vezérlő API a beágyazott. 

1. A helyi gépén, hozzon létre egy új fájlt, és adjon neki nevet **MapRoute.html**. 
2. Az alábbi HTML-összetevők hozzáadása a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Vegye figyelembe, hogy hogyan a HTML-fejléc beágyazza a CSS- és JavaScript fájlok az Azure-alapú helyszolgáltatás szalagtár erőforrás helyeit. Figyelje meg is a *parancsfájl* szegmens a HTML-fájl, a beágyazott JavaScript-kód az Azure hely alapú szolgáltatás API-k elérésére tartalmazó törzsében.

3. Adja hozzá a következő JavaScript-kódot a *parancsfájl* blokk a HTML-fájl. Cserélje le a helyőrző *< insert-kulcs >* a hely alapú Services-fiók elsődleges kulccsal.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var subscriptionKey = "<insert-key>";
    var map = new atlas.Map("map", {
        "subscription-key": subscriptionKey
    });
    ```
    A **atlas. Térkép** vezérlő biztosít a vizuális és interaktív webes térképre, és az Azure térkép vezérlőelem API összetevője.

4. Adja hozzá a következő JavaScript-kódot a *parancsfájl* blokkot. Ez biztosítja, hogy a réteget *linestring* az útvonalat a térkép vezérlőelem számára:

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

5. Adja hozzá a következő JavaScript-kód létrehozása a kezdő- és végpontok az útvonal:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Ez a kód létrehoz két [GeoJSON objektumok](https://en.wikipedia.org/wiki/GeoJSON) kezdő- és végpontok útvonal képviseli. A végpont az egyik a szélesség/hosszúság kombináció a *üzemanyag állomások* keres az előző oktatóanyag [keresési közelben pont használatával, Azure-alapú helyszolgáltatás](./tutorial-search-location.md).

6. Adja hozzá a PIN-kódok a kezdő- és végpontok hozzáadása a leképezés a következő JavaScript-kódot:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    Az API-t **map.setCameraBounds** beállítja a térkép ablakban a kezdő- és végpontjainak koordinátáival megfelelően. Az API-t **map.addPins** a térkép vezérlőelem visual összetevőként a pontok hozzáadása.

7. Mentse a **MapRoute.html** fájlt a számítógépre. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Lekérdezési érdeklő ponthoz utasításokat az útvonal-szolgáltatás

Ez a szakasz bemutatja, hogyan használható az Azure-alapú helyszolgáltatás útvonal Service API a útvonalát egy adott kezdési pont található. Az útvonal-szolgáltatás API-k segítségével tervezhető a leggyorsabb, a lehető legrövidebb, vagy között két helyen, figyelembe véve a valós idejű forgalom feltételek ökocímkével útvonalat biztosít. Azt is lehetővé teszi a felhasználóknak tervezze meg a jövőben útvonalak Azure kiterjedt történelmi forgalom adatbázis használatával, és előrejelzésére útvonal trendelemzés céljából, bármely napját és időpontját. 

1. Nyissa meg a **MapRoute.html** fájl az előző szakaszban létrehozott, és adja hozzá a következő JavaScript-kódot a *parancsfájl* letiltása, hogy bemutassa a útvonal-szolgáltatás.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    A kódrészletet létrehoz egy [XMLHttpRequest](https://xhr.spec.whatwg.org/), és hozzáadja a bejövő válasz elemzése eseménykezelő. Sikeres választ akkor az első visszaadott útvonal sor szegmensek koordináták tömbje hoz létre. E koordináták útvonal csoportja majd hozzáadása a térkép *linestring* réteg.

2. Adja hozzá a következő kódot a *parancsfájl* blokkot, a XMLHttpRequest küldeni az Azure-alapú helyszolgáltatás útvonal-szolgáltatás:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + subscriptionKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    A fenti kérelmet a szükséges paramétereket, amelyek a fiókkulcs előfizetés, és a koordináták a kezdő és végpontja, a megadott sorrendben jeleníti meg. 

3. Mentse a **MapRoute.html** fájlt helyben, majd nyissa meg az Ön által választott webböngészőben, és tekintse meg az eredménye. A sikeres csatlakozáshoz a hely alapú szolgáltatások API-khoz meg kell jelennie az alábbihoz hasonló térképet. 

    ![Az Azure térkép vezérlőelem és útvonal-szolgáltatás](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Cím koordináták beolvasása
> * Lekérdezési érdeklő ponthoz utasításokat az útvonal-szolgáltatás

Az oktatóanyag folytassa [útvonalak található különböző üzemmódjainak, az Azure-alapú helyszolgáltatás használatával utazás](./tutorial-prioritized-routes.md) használata az Azure-alapú helyszolgáltatás rangsorolására útvonalak pontjának iránt, az átviteli mód alapján. 
