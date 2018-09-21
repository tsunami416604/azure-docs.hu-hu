---
title: Útvonalkeresés az Azure Mapsszel | Microsoft Docs
description: Útvonal keresése egy hasznos helyhez az Azure Mapsszel
author: dsk-2015
ms.author: dkshir
ms.date: 09/04/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 68d7df575e3d413780b8181c11dd59a22469708b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578937"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Útvonal keresése egy hasznos helyhez az Azure Mapsszel

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Maps-fiókot és a Route Service SDK-t a hasznos helyekre vezető útvonalak megkereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * Címkoordináták beállítása
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt az előző útmutató lépéseit követve [hozza létre saját Azure Maps-fiókját](./tutorial-search-location.md#createaccount), és [kérje le a fiókja előfizetési kulcsát](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Új térkép létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a térképkezelési API.

1. A helyi gépén hozzon létre egy új fájlt **MapRoute.html** néven.
2. Adja a következő HTML-összetevőket a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css"/>
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
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
    A HTML-fejléc beágyazza a CSS- és a JavaScript-fájlok erőforráshelyeit az Azure Maps-kódtárba. A HTML törzsében lévő *szkript* szakasz tartalmazza az Azure Maps API-jainak elérésére szolgáló beágyazott JavaScript-kódot.

3. Adja hozzá a következő JavaScript-kódot a HTML-fájl *szkript* blokkjához. A **\<your account key\>** sztringet cserélje le a Maps-fiókból kimásolt elsődleges kulcsra.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Az Azure Térkép vezérlőelem API **atlas.Map** összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Kiindulási pontok és végpontok megadása

A jelen oktatóanyag esetében a Microsoftot állítsa be kiindulási pontnak, célpontnak pedig egy seattle-i benzinkutat.

1. A **MapRoute.html** fájlnak ugyanabban a *szkriptblokkjában* adja hozzá a következő JavaScript-kódot, amellyel létrehozza az útvonal kiindulási és végpontját:

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
    Ez a kód két [GeoJSON-objektumot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre, amelyek az útvonal indulási és célpontját jelzik.

2. Adja hozzá a következő JavaScript-kódot, amellyel hozzáadja az indulási és célpontot jelölő gombostűket a térképhez:

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
    A **map.setCameraBounds** a kiindulási és végpontok koordinátái alapján állítja be a térkép ablakát. A **map.addPins** API vizuális összetevőként adja hozzá a pontokat a Térkép vezérlőelemhez.

3. Mentse a **MapRoute.html** fájlt, és frissítse a böngészőt. Most Seattle látható a térkép középpontjában, a kezdőpontot a kerek kék gombostű jelzi, a végpontot pedig a kék gombostű.

   ![A kiindulási és végpontokat jelölő térkép megtekintése](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Útvonal keresése

Ez a szakasz bemutatja, hogyan kereshet egy kiindulási és célpont között útvonalat a Maps útvonal-szolgáltatási API-jával. Az útvonal-szolgáltatás API-kat biztosít a két hely közötti *leggyorsabb*, *legrövidebb*, *leggazdaságosabb* vagy *leglátványosabb* útvonal megtervezéséhez. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. További információ: [Útvonal keresése](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

1. Először adjon egy új réteget a térképhez az útvonal vagy *linestring* megjelenítéséhez. Adja hozzá a következő JavaScript-kódot a *szkript* blokkhoz.

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

2. Az ügyfélszolgáltatás példányosításához adja hozzá a következő JavaScript-kódot a szkriptblokkhoz.
    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. Adja hozzá a következő kódblokkot az útvonal-lekérdezési sztring létrehozásához.
    ```JavaScript
    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];
    ```

4. Az útvonal lekéréséhez adja hozzá az alábbi kódot a szkripthez. A kód a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódussal lekérdezést küld az Azure Maps útválasztási szolgáltatásnak, a kapott választ pedig a [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metódussal elemzi, és GeoJSON-formátumban adja ki. Ezután a válaszvonalakat hozzáadja a térképhez az útvonal rendereléséhez. További információért lásd a [vonal térképhez való hozzáadását](./map-add-shape.md#addALine) ismertető cikket.

    ```JavaScript
    // Execute the query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
         // Parse the response into GeoJSON
         var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

         // Get the first in the array of routes and add it to the map
         map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
             name: routeLinesLayerName
         });
    });
    ```

5. Mentse a **MapRoute.html** fájlt, és frissítse a webböngészőt. A Maps API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg.

    ![Azure Térkép vezérlőelem és Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * Címkoordináták beállítása
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

A következő oktatóanyag bemutatja, hogyan hozhat létre egy útvonal-lekérdezést olyan korlátozásokkal, mint az utazás módja vagy a rakomány típusa, majd megjelenít több útvonalat ugyanazon a térképen.

> [!div class="nextstepaction"]
> [Útvonalak keresése különböző utazási módokhoz](./tutorial-prioritized-routes.md)
