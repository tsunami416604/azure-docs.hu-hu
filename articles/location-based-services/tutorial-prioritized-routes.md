---
title: "Több útvonal az Azure Location Based Services használatával | Microsoft Docs"
description: "Útvonalak keresése különböző utazási módokhoz az Azure Location Based Services használatával"
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
ms.openlocfilehash: 78e911d17fe8c468cf89ec1477f1c5144e6669b6
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Útvonalak keresése különböző utazási módokhoz az Azure Location Based Services használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Location Based Services-fiókot és a Route Service SDK-t a hasznos helyekre vezető útvonalak megkereséséhez, az utazási mód szerint rendezve. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * a Route Service-lekérdezés konfigurálása;
> * útvonalak megjelenítése utazási mód alapján rendezve.

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt [hozza létre a saját Azure Location Based Services-fiókját](./tutorial-search-location.md#createaccount), és [kérjen le egy kulcsot a fiókból](./tutorial-search-location.md#getkey). Azt is megfigyelheti, hogyan használhatók a Térkép vezérlőelem és a Search szolgáltatás API-jai a [Közeli hasznos helyek keresése az Azure Location Based Services használatával](./tutorial-search-location.md) című oktatóanyag szerint, valamint megismerheti a Route Service API-k alapvető használatát az [Útvonal egy hasznos helyhez az Azure Location Based Services használatával](./tutorial-route-location.md) című oktatóanyag szerint.


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>a Route Service-lekérdezés konfigurálása;

A következő lépésekkel hozzon létre egy statikus HTML-oldalt, amelybe be van ágyazva a Location Based Services Térkép vezérlőelem API-ja. 

1. A helyi gépén hozzon létre egy új fájlt **MapTruckRoute.html** néven. 
2. Adja a következő HTML-összetevőket a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    Látható, hogy a HTML-fejléc beágyazza a CSS- és a JavaScript-fájlok erőforráshelyeit az Azure Location Based Services-kódtárba. Figyelje meg a HTML törzséhez adott *szkript* szakaszt is, amely tartalmazza az Azure Térkép vezérlőelem API elérésére szolgáló beágyazott JavaScript-kódot.
3. Adja hozzá a következő JavaScript-kódot a HTML-fájl *szkript* blokkjához. Cserélje le az *<insert-key>* helyőrzőt a Location Based Services-fiók elsődleges kulcsára.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Az Azure Térkép vezérlőelem API **atlas.Map** összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Adja hozzá a következő JavaScript-kódot a *szkript* blokkhoz, hogy a térképen megjelenjen a forgalom megjelenítése:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Ez a kód beállítja `relative` adatforgalmat állít be, amely az üres úton elérhető sebességhez viszonyított érték. Az út `absolute` sebességére is állíthatja ezt, vagy a `relative-delay` sebességre, amely a relatív sebességet jeleníti meg, ha eltér az üres úton elérhető sebességtől. 

5. Adja hozzá a következő JavaScript-kódot, amellyel létrehozza az útvonal indulási és célpontját jelölő gombostűket:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Ez a kód két [GeoJSON-objektumot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre, amelyek az útvonal indulási és célpontját jelzik. 

6. A következő JavaScript-kód hozzáadásával *linestring* karakterláncok rétegeit adhatja hozzá adja a Térkép vezérlőelemhez az útvonalak utazási mód szerinti megjelenítéséhez (például _autó_ és _teherautó_).

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

7. Adja hozzá a következő JavaScript-kódot, amellyel hozzáadja az indulási és célpontot a térképhez:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    A **map.setCameraBounds** API az indulási és célpontok koordinátái alapján állítja be a térkép ablakát. A **map.addPins** API vizuális összetevőként adja hozzá a pontokat a Térkép vezérlőelemhez.

8. Mentse a **MapTruckRoute.html** fájlt a gépén. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>útvonalak megjelenítése utazási mód alapján rendezve.

Ez a szakasz bemutatja, hogyan kereshet egy indulási és célpont között több útvonalat az utazás módja alapján az Azure Location Based Services Route Service API-jával. A Route Service API-kat biztosít a két hely közötti leggyorsabb, legrövidebb vagy leggazdaságosabb útvonal megtervezéséhez, a valós idejű forgalmi viszonyokat figyelembe véve. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. 

1. Nyissa meg az előző szakaszban létrehozott **MapTruckRoute.html** fájlt, és adja hozzá a következő JavaScript-kódot a *szkript* blokkhoz a teherautós útvonal Route Service-szel való lekéréséhez.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Ez a kódrészlet létrehoz egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) kérést, és hozzáad egy eseménykezelőt a bejövő válasz elemzéséhez. A sikeres válasz érdekében létrehozza a visszaadott útvonal koordinátáinak gyűjteményét, és hozzáadja a térkép `truckRouteLayerName` rétegéhez. 
    
    Ez a kódrészlet a Route Service-nek is elküldi a lekérdezést, hogy a fiókkulcshoz kapcsolva lekérje a megadott kiindulási és célpont útvonalát. A következő választható paraméterekkel jelezhető egy nehéz teherautó útvonala: – A `travelMode=truck` paraméter *teherautóként* határozza meg az utazás módját. A többi támogatott utazási mód a *taxi*, *busz*, *kisteherautó*, *motorkerékpár* és az alapértelmezett *autó*.  
        – A `vehicleWidth`, `vehicleHeight` és `vehicleLength` paraméterek határozzák meg a jármű méretét méterben, és a rendszer csak akkor veszi figyelembe ezeket, ha az utazási mód a *teherautó*.  
        – A `vehicleLoadType` veszélyesként sorolja be a rakományt, és egyes utakon korlátozott. Jelenleg ez is csak a *teherautó* módban érvényes.  

2. Adja hozzá a következő JavaScript-kódot egy autós útvonal Route Service-szel való lekéréséhez:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Ez a kódrészlet egy másik [XMLHttpRequest](https://xhr.spec.whatwg.org/) kérést hoz létre, és hozzáad egy eseménykezelőt a bejövő válasz elemzéséhez. A sikeres válasz érdekében létrehozza a visszaadott útvonal koordinátáinak gyűjteményét, és hozzáadja a térkép `carRouteLayerName` rétegéhez. 
    
    Ez a kódrészlet a Route Service-nek is elküldi a lekérdezést, hogy a fiókkulcshoz kapcsolva lekérje a megadott kiindulási és célpont útvonalát. Mivel nem használ más paramétereket, a rendszer az alapértelmezett *autó* utazási mód útvonalát adja vissza. 

3. Mentse helyben a **MapTruckRoute.html** fájlt, majd nyissa meg egy tetszőleges webböngészőben, és vizsgálja meg az eredményt. A Location Based Services API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg. 

    ![Prioritás szerint rendezett útvonalak az Azure Route Service-szel](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Vegye figyelembe, hogy a teherautó útvonala kék, az autó útvonala pedig lila.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * a Route Service-lekérdezés konfigurálása;
> * útvonalak megjelenítése utazási mód alapján rendezve.

Ha alaposabban szeretné megismerni az Azure Location Based Services SDK-t, tekintse át az **alapfogalmakat** és az **útmutatókat** tartalmazó cikkeket. 
