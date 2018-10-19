---
title: Több útvonal az Azure Maps használatával | Microsoft Docs
description: Útvonalak keresése különböző utazási módokhoz az Azure Maps használatával
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 340bf83f07b9e730cc43baccc60a39f5ba1f9942
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815307"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Útvonalak keresése különböző utazási módokhoz az Azure Maps használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Maps-fiókot és az útvonalkereső szolgáltatást a hasznos helyekre vezető útvonalak megkereséséhez és az utazási mód szerinti rendezéséhez. Két különböző útvonalat jelenít meg a térképen: egyet az autók, egyet pedig a teherautók számára, amelyekre útvonal-korlátozások vonatkozhatnak a magasság, a súly vagy a veszélyes rakomány miatt. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * A forgalom megjelenítése a térképen
> * Utazási módot deklaráló útvonal-lekérdezések létrehozása
> * Több útvonal megjelenítése a térképen

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt az első oktatóanyag lépéseit követve [hozza létre a saját Azure Maps-fiókját](./tutorial-search-location.md#createaccount), és [kérje le a fiókja előfizetési kulcsát](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Új térkép létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a térképkezelési API.

1. A helyi gépén hozzon létre egy új fájlt **MapTruckRoute.html** néven.
2. Adja a következő HTML-összetevőket a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
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
    A HTML-fejléc beágyazza a CSS- és a JavaScript-fájlok erőforráshelyeit az Azure Maps-kódtárba. A HTML törzsében lévő *script* szakasz tartalmazza a térképhez tartozó beágyazott JavaScript-kódot.
3. Adja hozzá a következő JavaScript-kódot a HTML-fájl *szkript* blokkjához. A **\<your account key\>** sztringet cserélje le a Maps-fiókból kimásolt elsődleges kulcsra. Ha nem határozza meg, hogy a térkép hova fókuszáljon, az egész világot látni fogja. Ez a kód beállítja a térkép középpontját, és deklarál egy nagyítási szintet, hogy alapértelmezés szerint egy adott területre fókuszálhasson.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
         center: [-118.2437, 34.0522],
         zoom: 12
    });
    ```
    Az Azure Térkép vezérlőelem API **atlas.Map** összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel. 

   ![Egyszerű térkép megtekintése](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>A forgalom megjelenítése

1. Adja hozzá a térképhez a forgalom megjelenítését.  A **map.addEventListener** biztosítja, hogy a térképhez hozzáadott összes térképfunkció betöltődjön a térkép teljes betöltése után.

    ```JavaScript
    map.addEventListener("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    Ez a kód beállítja `relative` adatforgalmat állít be, amely az üres úton elérhető sebességhez viszonyított érték. Az út `absolute` sebességére is állíthatja ezt, vagy a `relative-delay` sebességre, amely a relatív sebességet jeleníti meg, ha eltér az üres úton elérhető sebességtől.

2. Mentse a **MapTruckRoute.html** fájlt, és frissítse a lapot a böngészőben. Most Los Angeles utcáit kell látnia az aktuális forgalmi adatokkal.

   ![A forgalmi térkép megtekintése](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Kiindulási pontok és végpontok megadása

A jelen oktatóanyag esetében állítson be indulási pontnak egy Fabrikam nevű fiktív vállalatot Seattle-ben, célpontnak pedig a Microsoft irodáját.

1. Adja hozzá a következő JavaScript-kódot, amellyel létrehozza az útvonal indulási és célpontját jelölő gombostűket:

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

2. Adja hozzá a következő JavaScript-kódot, amellyel hozzáadja az indulási és célpontot a térképhez:

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
    
    map.addEventListener("load", function() { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    A **map.setCameraBounds** hívás a kiindulási és végpontok koordinátái alapján állítja be a térkép ablakát. A **map.addEventListener** biztosítja, hogy a térképhez hozzáadott összes térképfunkció betöltődjön a térkép teljes betöltése után. A **map.addPins** API vizuális összetevőként adja hozzá a pontokat a Térkép vezérlőelemhez.

3. Mentse a fájlt, majd frissítse a böngészőt, hogy a gombostűk megjelenjenek a térképen. Bár Los Angelest adta meg a térkép középpontjaként, a **map.setCameraBounds** úgy igazította a nézetet, hogy a kiindulási és végpontok jelenjenek meg.

   ![Az indulási és célpontokat jelölő térkép megtekintése](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>útvonalak megjelenítése utazási mód alapján rendezve.

Ez a szakasz bemutatja, hogyan kereshet egy indulási és célpont között több útvonalat az utazás módja alapján a Maps útvonal-szolgáltatás API-jával. Az útvonal-szolgáltatás API-kat biztosít a két hely közötti *leggyorsabb*, *legrövidebb*, *leggazdaságosabb* vagy *leglátványosabb* útvonal megtervezéséhez, az aktuális forgalmi viszonyokat figyelembe véve. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. További információ: [Útvonal keresése](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).  Az összes alábbi kódblokkot fel kell vennie **a térképbetöltés eventListener elemébe**, hogy a térkép teljes betöltése után betöltődjenek.

1. Először adjon egy új réteget a térképhez az útvonal vagy *linestring* megjelenítéséhez. Ebben az oktatóanyagban két különböző útvonal van, a **car-route** és a **truck-route**, és mindkettő saját stílussal rendelkezik. Adja hozzá a következő JavaScript-kódot a *script* blokkhoz:

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

2. Adja hozzá a következő JavaScript-kódot a *script* blokkhoz a teherautós útvonal lekéréséhez és az eredmények térképen való megjelenítéséhez:

    ```JavaScript
    // Instantiate the service client  
    var client = new atlas.service.Client(MapsAccountKey);

    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];

    // Execute the truck route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery, {
        travelMode: "truck",
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: "USHazmatClass2"
    }).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson
            .GeoJsonRouteDirectionsResponse(response);

        // Get the first in the array of routes and add it to the map
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: truckRouteLayerName
        });
    });
    ```
    A fenti kódrészlet példányosít egy szolgáltatásügyfelet és létrehoz egy útvonal-lekérdezési sztringet. A kód ezután a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódussal lekérdezést küld az Azure Maps útválasztási szolgáltatásnak, a kapott választ pedig a [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) metódussal elemzi, és GeoJSON-formátumban adja ki. Ezután létrehozza a visszaadott útvonal koordinátáinak gyűjteményét, és hozzáadja a térkép `truckRouteLayerName` rétegéhez.

3. Adja hozzá a következő JavaScript-kódot az autós útvonal lekéréséhez és az eredmények megjelenítéséhez:

    ```JavaScript
    // Execute the car route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new tlas.service.geojson
            .GeoJsonRouteDiraectionsResponse(response);

        // Get the first in the array of routes and add it to the map 
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: carRouteLayerName
        });
    });
    ```
    Ez a kódrészlet egy autóra alkalmazza ugyanezt a teherautós útvonal-lekérdezést. A kód a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódussal lekérdezést küld az Azure Maps útválasztási szolgáltatásnak, a kapott választ pedig a [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) metódussal elemzi, és GeoJSON-formátumban adja ki. Ezután létrehozza a visszaadott útvonal koordinátáinak gyűjteményét, és hozzáadja a térkép `carRouteLayerName` rétegéhez.

4. Mentse a **MapTruckRoute.html** fájlt, és frissítse a böngészőt az eredmény megtekintéséhez. A Maps API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg.

    ![Prioritás szerint rendezett útvonalak az Azure Route Service-szel](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A teherautós útvonal kék színű és vastagabb, az autós útvonal pedig lila és vékonyabb. Az autós útvonal az I-90-es autópályát igénybe véve keresztülhalad a Washington-tavon. Ez az autópálya lakóövezetek alatti alagutakon halad át, ezért korlátozza a veszélyes hulladékok szállítását. Az USHazmatClass2 rakománytípust megadó teherautós útvonalat a rendszer megfelelő módon egy másik autópályára irányítja.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * A forgalom megjelenítése a térképen
> * Utazási módot deklaráló útvonal-lekérdezések létrehozása
> * Több útvonal megjelenítése a térképen

Az oktatóanyag mintakódját itt érheti el:

> [Több útvonal az Azure Maps használatával](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/truckRoute.html)

További információ az Azure Maps lefedettségéről és képességeiről:

> [!div class="nextstepaction"]
> [Nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md)

További példakódok és az interaktív kódolási felület bemutatása:

> [!div class="nextstepaction"]
> [A térkép vezérlőelem használata](how-to-use-map-control.md)