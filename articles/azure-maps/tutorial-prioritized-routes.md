---
title: Több útvonal az Azure Maps használatával | Microsoft Docs
description: Útvonalak keresése különböző utazási módokhoz az Azure Maps használatával
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0a278eb1612ec9573c4d12611ccce2d1b5b971bc
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705278"
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
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Figyelje meg, hogy a HTML-fejléc tartalmazza az Azure Térkép vezérlőelem-kódtárban található CSS- és JavaScript-erőforrásfájlokat. Tekintse meg a laptörzs `onload` eseményét, amely a laptörzs betöltését követően meghívja a `GetMap` függvényt. Ez a függvény tartalmazza az Azure Maps API-k elérésére szolgáló beágyazott JavaScript-kódot.

3. Adja hozzá az alábbi JavaScript-kódot a `GetMap` függvényhez. A **\<Your Azure Maps Key\>** sztringet cserélje le a Maps-fiókból kimásolt elsődleges kulcsra.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    Az Azure Térkép vezérlőelem API **atlas.Map** összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>A forgalom megjelenítése

1. Adja hozzá a térképhez a forgalom megjelenítését. A `map.events.add` gondoskodik róla, hogy a térképhez hozzáadott összes térképfunkció betöltődjön a térkép teljes betöltése után.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     A rendszer hozzáad a térképhez egy betöltési eseményt, amely a térkép erőforrásainak teljes betöltését követően aktiválódik. A térkép betöltésiesemény-kezelőjében a térképen `relative` forgalom van beállítva, amely az üres úton elérhető sebességhez viszonyított értéket jeleníti meg. Az út `absolute` sebességére is állíthatja ezt, vagy a `relative-delay` sebességre, amely a relatív sebességet jeleníti meg, ha eltér az üres úton elérhető sebességtől.

2. Mentse a **MapTruckRoute.html** fájlt, és frissítse a lapot a böngészőben. Most Los Angeles utcáit kell látnia az aktuális forgalmi adatokkal.

   ![A forgalmi térkép megtekintése](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban két útvonalat számítunk ki és jelenítünk meg a térképen. Az egyik alapjául a személyautók, a másik alapjául a teherautók által használható utak szolgálnak. A megjelenített utakon egy-egy szimbólum jelzi az útvonal elejét és végét, és különböző színű vonalak jelzik az egyes útvonalakat.

1. A GetMap függvényben adja hozzá az alábbi JavaScript-kódot a térkép inicializálása után.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    A rendszer hozzáad a térképhez egy betöltési eseményt, amely a térkép erőforrásainak teljes betöltését követően aktiválódik. A térkép betöltésiesemény-kezelőjében létrejön egy, az útvonalak, illetve az indulási és célpontok tárolására szolgáló adatforrás. A rendszer egy vonalréteget hoz létre, majd csatol az adatforráshoz az útvonal megjelenítési módjának meghatározásához. A vonalvastagságok és a színek kifejezésekkel kérhetők le az útvonal tulajdonságaiból. A rendszer hozzáad egy szűrőt, hogy a réteg csak a GeoJSON LineString típusú adatokat jelenítse meg. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ezzel biztosítható, hogy az útvonal ne takarja ki az utakhoz tartozó feliratokat. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg az indulási és célpontok megjelenítését. Itt hozzáadott kifejezések kérik le az ikonképekkel és a szövegfeliratokkal kapcsolatos információkat az egyes pontobjektumok tulajdonságaiból. 
    
2. A jelen oktatóanyag esetében állítson be indulási pontnak egy Fabrikam nevű fiktív vállalatot Seattle-ben, célpontnak pedig a Microsoft irodáját. A térkép betöltésiesemény-kezelőjében adja hozzá az alábbi kódot.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    Ez a kód két [GeoJSON-objektumot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre, amelyek az útvonal indulási és célpontját jelzik. A rendszer minden ponthoz hozzáad egy-egy `title` és `icon` tulajdonságot.

3. Ezután adja hozzá a következő JavaScript-kódot, amellyel hozzáadja az indulási és célpontokat jelölő gombostűket a térképhez:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Az indulási és célpontokat a rendszer hozzáadja az adatforráshoz. Az indulási és célpontokhoz tartozó határolókeret kiszámítása az `atlas.data.BoundingBox.fromData` függvénnyel történik. Ezzel a határolókerettel állítható be a térkép kameranézete az indulási és a célpontra a `map.setCamera` függvénnyel. A rendszer kitöltést is hozzáad a szimbólumikonok képpontban kifejezett méreteinek kompenzálásához.

4. Mentse a fájlt, majd frissítse a böngészőt, hogy a gombostűk megjelenjenek a térképen. Most Seattle látható a térkép középpontjában, a kezdőpontot a kerek kék gombostű jelzi, a végpontot pedig a kék gombostű.

   ![Az indulási és célpontokat jelölő térkép megtekintése](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>útvonalak megjelenítése utazási mód alapján rendezve.

Ez a szakasz bemutatja, hogyan kereshet egy indulási és célpont között több útvonalat az utazás módja alapján a Maps útvonal-szolgáltatás API-jával. Az útvonal-szolgáltatás API-kat biztosít a két hely közötti *leggyorsabb*, *legrövidebb*, *leggazdaságosabb* vagy *leglátványosabb* útvonal megtervezéséhez, az aktuális forgalmi viszonyokat figyelembe véve. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. További információért lásd a [getRouteDirections API-t](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) ismertető témakört. Az összes alábbi kódblokkot fel kell vennie **a térképbetöltés eventListener elemébe**, hogy a térkép teljes betöltése után betöltődjenek.

1. A szolgáltatásügyfél példányosításához adja hozzá az alábbi JavaScript-kódot a térkép betöltésiesemény-kezelőjében.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Adja hozzá a következő kódblokkot az útvonal-lekérdezési sztring létrehozásához.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Adja hozzá a következő JavaScript-kódot, amely USHazmatClass2 osztályú rakományt szállító teherautók számára kéri le az útvonalat, és megjeleníti az eredményeket:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    A fenti kódrészlet a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódussal lekérdezést küld az Azure Maps útválasztási szolgáltatásnak, a kapott választ pedig a [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) metódussal elemzi, és GeoJSON-formátumban adja ki. Ezután létrehozza a visszaadott útvonal koordinátáinak gyűjteményét, és hozzáadja az adatforráshoz egy 0-s index kíséretében, hogy az adatforrás egyéb vonalai előtt legyen megjelenítve. Ez azért hasznos, mert a teherautós útvonalakat a rendszer általában lassabban számítja ki, mint a személyautókét, és ha egy teherautós útvonal egy személyautós után adódik hozzá az adatforráshoz, akkor fölötte fog megjelenni. A kód két tulajdonságot rendel a teherautós útvonalhoz: egy kék árnyalatú vonalszínt és 9 képpontos vonalvastagságot. 

4. Adja hozzá a következő JavaScript-kódot az autós útvonal lekéréséhez és az eredmények megjelenítéséhez:

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    Ez a kódrészlet egy autóra alkalmazza ugyanezt a teherautós útvonal-lekérdezést. A kód a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metódussal lekérdezést küld az Azure Maps útválasztási szolgáltatásnak, a kapott választ pedig a [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) metódussal elemzi, és GeoJSON-formátumban adja ki. Ezután létrehozza a visszaadott útvonal koordinátáinak gyűjteményét, és hozzáadja az adatforráshoz. A kód két tulajdonságot rendel a személyautós útvonalhoz: egy lila árnyalatú vonalszínt és 5 képpontos vonalvastagságot. 

5. Mentse a **MapTruckRoute.html** fájlt, és frissítse a böngészőt az eredmény megtekintéséhez. A Maps API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg.

    ![Prioritás szerint rendezett útvonalak az Azure Route Service-szel](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A teherautós útvonal kék színű és vastagabb, az autós útvonal pedig lila és vékonyabb. Az autós útvonal az I-90-es autópályát igénybe véve keresztülhalad a Washington-tavon. Ez az autópálya lakóövezetek alatti alagutakon halad át, ezért korlátozza a veszélyes hulladékok szállítását. Az USHazmatClass2 rakománytípust megadó teherautós útvonalat a rendszer megfelelő módon egy másik autópályára irányítja.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * A forgalom megjelenítése a térképen
> * Utazási módot deklaráló útvonal-lekérdezések létrehozása
> * Több útvonal megjelenítése a térképen

Az oktatóanyag kódmintáját itt érheti el:

> [Több útvonal az Azure Maps használatával](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[A minta megtekintése élőben](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

A következő oktatóanyag azt ismerteti, az Azure Maps használatával egy egyszerű store kereső létrehozásának folyamatán.

> [!div class="nextstepaction"]
> [Az Azure Maps store lokátorok létrehozásához](./tutorial-create-store-locator.md)