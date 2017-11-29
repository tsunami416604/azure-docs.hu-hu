---
title: "Több útvonal rendelkezik Azure-alapú helyszolgáltatás |} Microsoft Docs"
description: "Az Azure-alapú helyszolgáltatás használatával utazás különböző módokban útvonalak keresése"
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
ms.openlocfilehash: 3631bab8e5cb505689e92d2862c6863bcd56404d
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Az Azure-alapú helyszolgáltatás használatával utazás különböző módokban útvonalak keresése

Ez az oktatóanyag bemutatja, hogyan használja az Azure-alapú helyszolgáltatás fiókját és az útválasztási szolgáltatás SDK iránt, a módban az utazás előrébb pontjának az útvonal kereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az útvonal-szolgáltatás lekérdezés konfigurálása
> * Útvonalak utazás módban előrébb leképezése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, győződjön meg arról, hogy [létre Azure-alapú helyszolgáltatás fiókját](./tutorial-search-location.md#createaccount), és [fiókja előfizetés kulcs lekérése](./tutorial-search-location.md#getkey). Is láthatja, akkor előfordulhat, hogy a térkép vezérlőelem és a keresési szolgáltatás API-k használata az oktatóanyag leírtaknak megfelelően [keresési közelben pont használatával, Azure-alapú helyszolgáltatás](./tutorial-search-location.md), valamint megtudhatja, az alapvető használatát, az útvonal Service API-k az oktatóanyag tárgyalt [használatával, Azure-alapú helyszolgáltatás pontra útvonal](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Az útvonal-szolgáltatás lekérdezés konfigurálása

Az alábbi lépések segítségével hozzon létre egy statikus HTML-lapot a hely alapú szolgáltatások térkép vezérlő API a beágyazott. 

1. A helyi gépén, hozzon létre egy új fájlt, és adjon neki nevet **MapTruckRoute.html**. 
2. Az alábbi HTML-összetevők hozzáadása a fájlhoz:

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
    Vegye figyelembe, hogy a HTML-fejléc beágyazza a CSS- és JavaScript fájlok az Azure-alapú helyszolgáltatás szalagtár erőforrás helyeit. Figyelje meg is a *parancsfájl* a HTML-KÓDBAN, magában foglalja a beágyazott JavaScript-kód az Azure térkép vezérlőelem API eléréséhez törzsébe szegmens.
3. Adja hozzá a következő JavaScript-kódot a *parancsfájl* blokk a HTML-fájl. Cserélje le a helyőrző *< insert-kulcs >* a hely alapú Services-fiók elsődleges kulccsal.

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    A **atlas. Térkép** vezérlő biztosít a vizuális és interaktív webes térképre, és az Azure térkép vezérlőelem API összetevője.

4. Adja hozzá a következő JavaScript-kódot a *parancsfájl* blokkot, a forgalom folyamata megjelenítési hozzáadása a térkép:

    ```HTML
            // Add Traffic Flow to the Map
            map.setTraffic({
                flow: "relative"
            });
    ```
    Ez a kód értékűre állítja be a forgalom áramlását `relative`, ez az a sebesség szabad folyamata mappától kiinduló relatív elérési út. Is beállíthatja `absolute` sebessége utazás közben, vagy `relative-delay` jelenít meg a relatív sebesség ahol során is tapasztalhatunk eltéréseket ingyenes-adatfolyamban. 

5. Adja hozzá a következő JavaScript-kódot a PIN-kódok a kezdő- és útvonal végpontok létrehozásához:

    ```HTML
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
    Ez a kód létrehoz két [GeoJSON objektumok](https://en.wikipedia.org/wiki/GeoJSON) kezdő- és végpontok útvonal képviseli. 

6. Adja hozzá a következő JavaScript-kódot adja hozzá a rétegek *linestring* a térkép vezérlőelem megjelenítéséhez útvonalak szállítási, például mód alapján történő _car_ és _teherautó_.

    ```HTML
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

7. Adja hozzá a kezdő- és végpontok hozzáadása a leképezés a következő JavaScript-kódot:

    ```HTML
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
    Az API-t **map.setCameraBounds** beállítja a térkép ablakban a kezdő- és végpontjainak koordinátáival megfelelően. Az API-t **map.addPins** a térkép vezérlőelem visual összetevőként a pontok hozzáadása.

8. Mentse a **MapTruckRoute.html** fájlt a számítógépre. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Útvonalak utazás módban előrébb leképezése

Ez a szakasz bemutatja, hogyan használatát az Azure-alapú helyszolgáltatás útvonal Service API egy adott indítás több útvonal egy helyhez, az átviteli mód alapján mutasson. Az útvonal-szolgáltatás API-k segítségével tervezhető a leggyorsabb, a lehető legrövidebb, vagy között két helyen, figyelembe véve a valós idejű forgalom feltételek ökocímkével útvonalat biztosít. Azt is lehetővé teszi a felhasználóknak tervezze meg a jövőben útvonalak Azure kiterjedt történelmi forgalom adatbázis használatával, és előrejelzésére útvonal trendelemzés céljából, bármely napját és időpontját. 

1. Nyissa meg a **MapTruckRoute.html** fájl az előző szakaszban létrehozott, és adja hozzá a következő JavaScript-kódot a *parancsfájl* letiltása, az útvonal lekérése egy teherautó az útvonal-szolgáltatás segítségével.

    ```HTML
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
            truckRouteUrl += "&subscription-key=" + subscriptionKey;
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
    A kódrészletet létrehoz egy [XMLHttpRequest](https://xhr.spec.whatwg.org/), és hozzáadja a bejövő válasz elemzése eseménykezelő. A sikeres válasz, a koordináták az útvonal visszaadott tömb hoz létre, és hozzáadja azt a térkép `truckRouteLayerName` réteg. 
    
    A kódrészletet a lekérdezést is küld az útvonal-szolgáltatás, az útvonal lekérése az adott kezdő és záró pontot, a fiók előfizetés kulcs. A következő kötelező paraméterek használatosak nehéz teherautó útvonal:-a paraméter `travelMode=truck` , utazás mód *teherautó*. Egyéb támogatott utazás módok a következők *taxi*, *bus*, *van*, *motorkerékpárja*, és az alapértelmezett *autó* . 
        -A paraméterek `vehicleWidth`, `vehicleHeight`, és `vehicleLength` adhatók meg a vehicle szemlélteti, és csak minősülnek, hogy van-e a mód utazás *teherautó*. 
        -A `vehicleLoadType` veszélyes és néhány utakon korlátozott rakomány osztályozza. Ez csak a jelenleg is előnyben a *teherautó* mód. 

2. Adja hozzá a következő JavaScript-kódot az útvonal lekérése egy autó az útvonal-szolgáltatás segítségével:

    ```HTML
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
            carRouteUrl += "&subscription-key=" + subscriptionKey;
            carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

            xhttpCar.open("GET", carRouteUrl, true);
            xhttpCar.send();
    ```
    A kódrészletet hoz létre egy másik [XMLHttpRequest](https://xhr.spec.whatwg.org/), és hozzáadja a bejövő válasz elemzése eseménykezelő. A sikeres válasz, a koordináták az útvonal visszaadott tömb hoz létre, és hozzáadja azt a térkép `carRouteLayerName` réteg. 
    
    A kódrészletet a lekérdezést is küld az útvonal-szolgáltatás, az útvonal lekérése az adott kezdő és záró pontot, a fiók előfizetés kulcs. Mivel nincs más paraméterek szerepelnek, az alapértelmezett mód utazás útvonal *autó* adja vissza. 

3. Mentse a **MapTruckRoute.html** fájlt helyben, majd nyissa meg az Ön által választott webböngészőben, és tekintse meg az eredménye. A sikeres csatlakozáshoz a hely alapú szolgáltatások API-khoz meg kell jelennie az alábbihoz hasonló térképet. 

    ![Rangsorolt útvonalak Azure útvonal szolgáltatással](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Vegye figyelembe, hogy a teherautó útvonal kék színnel, míg a car útvonal lila.

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az útvonal-szolgáltatás lekérdezés konfigurálása
> * Útvonalak utazás módban előrébb leképezése

Folytassa a **fogalmak** és **útmutató** cikkekből tudhat meg az Azure hely alapú szolgáltatások SDK részletesen. 
