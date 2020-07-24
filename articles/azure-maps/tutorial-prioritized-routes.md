---
title: 'Oktatóanyag: több útvonal megkeresése utazási mód alapján | Microsoft Azure térképek'
description: Ebből az oktatóanyagból megtudhatja, hogyan találhat útvonalakat különböző utazási módokhoz Microsoft Azure térképek használatával.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 94ad89c29ac3a9193b81e4d64e6253b826c1eda1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075389"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Oktatóanyag: útvonalak keresése különböző utazási módokhoz Azure Maps használatával

Ez az oktatóanyag bemutatja, hogyan használhatja a Azure Maps-fiókját és a Route szolgáltatást. Az útvonal-szolgáltatás megkeresi az adott pontra mutató útvonalat, amelyet az utazási mód rangsorol. Két különböző útvonalat jeleníthet meg a térképen, egyet az autók és egy a teherautók számára. Az útválasztási szolgáltatás figyelembe veszi a korlátozásokat a jármű magassága és súlya miatt, vagy ha a jármű veszélyes árut hordoz. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * A forgalom megjelenítése a térképen
> * Utazási módot deklaráló útvonal-lekérdezések létrehozása
> * Több útvonal megjelenítése a térképen

## <a name="prerequisites"></a>Előfeltételek
A folytatás előtt kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account) és az S1 árképzési csomag utasításait. A fiók elsődleges kulcsának lekéréséhez kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakör lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="create-a-new-map"></a>Új térkép létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a térképkezelési API.

1. A helyi gépén hozzon létre egy új fájlt **MapTruckRoute.html** néven.
2. Adja a következő HTML-összetevőket a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

3. Adja hozzá az alábbi JavaScript-kódot a `GetMap` függvényhez. Cserélje le a karakterláncot `<Your Azure Maps Key>` arra az elsődleges kulcsra, amelyet a Maps-fiókból másolt.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    Az `atlas.Map` osztály a vizuális és interaktív webes Térkép vezérlőelemét biztosítja, és az Azure TÉRKÉPKEZELÉS API egyik összetevője.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>A forgalom megjelenítése

1. Adja hozzá a térképhez a forgalom megjelenítését. A Maps `ready` esemény addig várakozik, amíg a Maps-erőforrások betöltődik, és készen áll a biztonságos interakcióra.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    A térképi eseménykezelőben a térképen a forgalmi folyamat beállítása a (z) értékre `ready` van állítva `relative` , amely az út sebessége a szabad folyamathoz képest. Az út `absolute` sebességére is állíthatja ezt, vagy a `relative-delay` sebességre, amely a relatív sebességet jeleníti meg, ha eltér az üres úton elérhető sebességtől.

2. Mentse a **MapTruckRoute.html** fájlt, és frissítse a lapot a böngészőben. Ha együttműködik a térképpel, és megközelíti a Los Angelesbe, a jelenlegi forgalmi adatokat tartalmazó utcákat kell látnia.

   ![Térkép forgalmának megtekintése](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban két útvonalat számítunk ki és jelenítünk meg a térképen. Az egyik alapjául a személyautók, a másik alapjául a teherautók által használható utak szolgálnak. Megjelenítéskor a rendszer megjelenít egy szimbólum ikont az útvonal elejéhez és végéhez, valamint az egyes útvonalak elérési útjainak különböző színes sorait.

1. A Térkép inicializálását követően adja hozzá a következő JavaScript-kódot a Maps-eseménykezelőben `ready` .

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
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
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    A Maps- `ready` eseménykezelőben létrejön egy adatforrás az útválasztási sorok és a kezdő és záró pontok tárolására. A rendszer egy vonalréteget hoz létre, majd csatol az adatforráshoz az útvonal megjelenítési módjának meghatározásához. A vonalvastagságok és a színek kifejezésekkel kérhetők le az útvonal tulajdonságaiból. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ezzel biztosíthatja, hogy az útválasztási sor ne fedje fel az utak címkéit. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg a kezdő és a záró pontok megjelenítésének módját. Ebben az esetben a kifejezések hozzá lettek adva, hogy beolvassák az ikon képének és a szöveg feliratának adatait az egyes pontok objektumainak tulajdonságaiból. 
    
2. A jelen oktatóanyag esetében állítson be indulási pontnak egy Fabrikam nevű fiktív vállalatot Seattle-ben, célpontnak pedig a Microsoft irodáját. A Maps- `ready` eseménykezelőben adja hozzá a következő kódot.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
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

    Az indulási és célpontokat a rendszer hozzáadja az adatforráshoz. Az indulási és célpontokhoz tartozó határolókeret kiszámítása az `atlas.data.BoundingBox.fromData` függvénnyel történik. Ezzel a határolókeret használatával állíthatja be a térképes kamerák nézetét a teljes útvonalon a `map.setCamera` függvénnyel. A rendszer kitöltést is hozzáad a szimbólumikonok képpontban kifejezett méreteinek kompenzálásához.

4. Mentse a fájlt, majd frissítse a böngészőt, hogy a gombostűk megjelenjenek a térképen. A Térkép középpontba került a Seattle-ben. A kiindulási pontot és a kék PIN-kódot jelölő kék PIN-kód megadásával megtekintheti a befejezési pontot.

   ![Az indulási és célpontokat jelölő térkép megtekintése](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>útvonalak megjelenítése utazási mód alapján rendezve.

Ez a szakasz bemutatja, hogyan használhatja a Maps Route Service API-t. Az útvonal API a szállítási mód alapján több útvonal megkeresésére szolgál egy adott kezdőponttól a végpontig. Az útválasztási szolgáltatás API-kat biztosít a *leggyorsabb*, *legrövidebb*, *Eco*vagy *izgalmas* útvonalak tervezéséhez. Az API-k nem csupán két helyszín közötti útvonalakat terveznek, hanem az aktuális forgalmi feltételeket is figyelembe veszik. 

A Route API lehetővé teszi, hogy a felhasználók a jövőben tervezzék meg az útvonalakat az Azure kiterjedt történelmi forgalmú adatbázisának használatával. Az API előre jelezheti az útvonal időtartamait egy adott napra és időpontra vonatkozóan. További információért lásd a [getRouteDirections API-t](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) ismertető témakört. 

Az alábbi kódrészleteket fel kell venni **a Térkép betöltési eventListener** , hogy a Térkép teljes terhelése után is betölthető legyen.

1. A GetMap függvényben adja hozzá a következőt a JavaScript-kódhoz.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   A `SubscriptionKeyCredential` létrehoz egy `SubscriptionKeyCredentialPolicy` -t az előfizetési kulccsal Azure Maps HTTP-kérések hitelesítéséhez. A `atlas.service.MapsURL.newPipeline()` veszi a `SubscriptionKeyCredential` szabályzatot, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -példányt. A a `routeURL` Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) műveletekhez tartozó URL-címet jelöli.

2. A hitelesítő adatok és az URL-cím beállítása után adja hozzá a következő JavaScript-kódot egy olyan útvonal létrehozásához az elejétől a végéig, amely egy USHazmatClass2 osztályozott rakományt szállít, és megjeleníti az eredményeket.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Ez a kódrészlet a Azure Maps útválasztási szolgáltatást a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) metódussal kérdezi le. Ezután a rendszer kinyeri az GeoJSON a metódus használatával kinyert válaszból `geojson.getFeatures()` . Ekkor a rendszer hozzáadja az útválasztási sort az adatforráshoz. A 0 index biztosítja, hogy az adatforrásban lévő többi sor előtt megjelenítve legyen. Ez azért történik, mert a tehergépkocsi-útvonal kiszámítása gyakran lassabb lesz, mint az autó útvonalának kiszámítása. Ha az autó útvonala után hozzáadja az adatforráshoz a Truck Route sort, akkor azt a rendszer megjeleníti. A rendszer két tulajdonságot ad hozzá a Truck Route-sorhoz, amely a kék árnyalatú körvonal színe, valamint a 9 képpont vastagsága.

3. Adja hozzá a következő JavaScript-kódot egy útvonal létrehozásához az autóhoz, és jelenítse meg az eredményeket.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Ez a kódrészlet a Azure Maps útválasztási szolgáltatást a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) metódussal kérdezi le. Ezután a rendszer kinyeri az GeoJSON a metódus használatával kinyert válaszból `geojson.getFeatures()` . Ekkor a rendszer hozzáadja az útválasztási sort az adatforráshoz. A rendszer két tulajdonságot ad hozzá az autós útvonalak sorához, amely a lila árnyalatú körvonal színe, valamint az öt képpont vastagsága.  

4. Mentse a **MapTruckRoute.html** fájlt, és frissítse a böngészőt az eredmény megtekintéséhez. A Maps API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg.

    ![Prioritás szerint rendezett útvonalak az Azure Route Service-szel](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A teherautó útvonala sűrű kék, az autó útvonala pedig vékony lila. Az autó útvonala az I-90-n keresztül halad végig Washington államban, amely a lakossági területeken található alagutakon halad át. Mivel az alagutak megközelítik a lakossági területeket, a veszélyes hulladékok mennyisége korlátozott. A tehergépkocsi-útvonal, amely egy USHazmatClass2-típusú rakományt határoz meg, egy másik autópálya használatára van irányítva.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * A forgalom megjelenítése a térképen
> * Utazási módot deklaráló útvonal-lekérdezések létrehozása
> * Több útvonal megjelenítése a térképen

> [!div class="nextstepaction"]
> [Teljes forráskód megtekintése](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Élő minta megtekintése](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

A következő oktatóanyag azt mutatja be, hogyan hozható létre egy egyszerű tároló-lokátor a Azure Maps használatával.

> [!div class="nextstepaction"]
> [Tároló-lokátor létrehozása Azure Maps használatával](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)