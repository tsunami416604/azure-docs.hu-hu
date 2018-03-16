---
title: "Útvonal keresése az Azure Location Based Services használatával | Microsoft Docs"
description: "Útvonal egy hasznos helyhez az Azure Location Based Services használatával"
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b54d97afb4e762a7d252acf7cf53a5deac01d43f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Útvonal egy hasznos helyhez az Azure Location Based Services használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Location Based Services-fiókot és a Route Service SDK-t a hasznos helyekre vezető útvonalak megkereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Címkoordináták lekérése
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt [hozza létre a saját Azure Location Based Services-fiókját](./tutorial-search-location.md#createaccount), és [kérje le a fiókja előfizetési kulcsát](./tutorial-search-location.md#getkey). Azt is megfigyelheti, hogyan használhatók a Térkép vezérlőelem és a Search szolgáltatás API-jai a [Közeli hasznos helyek keresése az Azure Location Based Services használatával](./tutorial-search-location.md) című oktatóanyag szerint.


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Címkoordináták lekérése

A következő lépésekkel hozzon létre egy statikus HTML-oldalt, amelybe be van ágyazva a Location Based Services Térkép vezérlőelem API-ja. 

1. A helyi gépén hozzon létre egy új fájlt **MapRoute.html** néven. 
2. Adja a következő HTML-összetevőket a fájlhoz:

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
    Látható, hogy a HTML-fejléc beágyazza a CSS- és a JavaScript-fájlok erőforráshelyeit az Azure Location Based Services-kódtárba. Figyelje meg a HTML törzsében lévő *szkript* szakaszt is, amely tartalmazza az Azure Location Based Service API-jának elérésére szolgáló beágyazott JavaScript-kódot.

3. Adja hozzá a következő JavaScript-kódot a HTML-fájl *szkript* blokkjához. Használja a Location Based Services-fiók elsődleges kulcsát a szkriptben.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Az Azure Térkép vezérlőelem API **atlas.Map** összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Adja hozzá a következő JavaScript-kódot a *szkript* blokkhoz. Ez *linestrings* karakterláncok rétegét adja hozzá a Térkép vezérlőelemhez az útvonal megjelenítése érdekében:

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

5. Adja hozzá a következő JavaScript-kódot, amellyel létrehozza az útvonal indulási és célpontját:

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
    Ez a kód két [GeoJSON-objektumot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre, amelyek az útvonal indulási és célpontját jelzik. A végpont az előző [Közeli hasznos helyek keresése az Azure Location Based Services használatával](./tutorial-search-location.md) című oktatóanyagban megkeresett egyik *benzinkút* szélesség/hosszúság kombinációja.

6. Adja hozzá a következő JavaScript-kódot, amellyel hozzáadja az indulási és célpontot jelölő gombostűket a térképhez:

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
    A **map.setCameraBounds** API az indulási és célpontok koordinátái alapján állítja be a térkép ablakát. A **map.addPins** API vizuális összetevőként adja hozzá a pontokat a Térkép vezérlőelemhez.

7. Mentse a **MapRoute.html** fájlt a gépén. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

Ez a szakasz bemutatja, hogyan kereshet egy indulási és célpont közötti útvonalat az Azure Location Based Services Route Service API-jával. A Route Service API-kat biztosít a két hely közötti leggyorsabb, legrövidebb vagy leggazdaságosabb útvonal megtervezéséhez, a valós idejű forgalmi viszonyokat figyelembe véve. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. 

1. Nyissa meg az előző szakaszban létrehozott **MapRoute.html** fájlt, és adja hozzá a következő JavaScript-kódot a *szkript* blokkhoz a Route Service ábrázolása érdekében.

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
    Ez a kódrészlet létrehoz egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) kérést, és hozzáad egy eseménykezelőt a bejövő válasz elemzéséhez. A sikeres válasz érdekében létrehozza az első visszaadott útvonal vonalszakaszainak koordinátagyűjteményét. Ezután hozzáadja az útvonal koordinátagyűjteményét a térkép *linestrings* rétegéhez.

2. Adja hozzá a *szkript* blokkhoz a következő kódot, amely elküldi az XMLHttpRequest kérést az Azure Location Based Services Search Service szolgáltatásának:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    A fenti kérés a kötelező paramétereket mutatja be, amelyek a fiókkulcs, illetve az indulási és célpontok, ebben a sorrendben. 

3. Mentse helyben a **MapRoute.html** fájlt, majd nyissa meg egy tetszőleges webböngészőben, és vizsgálja meg az eredményt. A Location Based Services API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg. 

    ![Azure Térkép vezérlőelem és Route Service](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Címkoordináták lekérése
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

Folytassa az [Útvonalak keresése különböző utazási módokhoz az Azure Location Based Services használatával](./tutorial-prioritized-routes.md) című oktatóanyaggal, amelyből megtudhatja, hogyan rendezheti a hasznos helyekhez vezető útvonalakat az utazás módja alapján az Azure Location Based Services használatával. 
