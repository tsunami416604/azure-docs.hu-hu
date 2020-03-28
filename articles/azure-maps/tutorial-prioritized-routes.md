---
title: 'Bemutató: Keressen több útvonalat utazási mód szerint | Microsoft Azure Maps'
description: Ebben az oktatóanyagban megtudhatja, hogyan keresheti meg a különböző utazási módok útvonalait a Microsoft Azure Maps használatával.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333795"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Oktatóanyag: Útvonalak keresése különböző utazási módokhoz az Azure Maps használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Maps-fiókot és az útvonalszolgáltatást. Az útvonalszolgáltatás megtalálja az ön érdeklődési pontjához vezető útvonalat, az ön utazási módja szerint rangsorolva. Két különböző útvonalat jeleníthet meg a térképen, egyet az autókhoz és egyet a teherautókhoz. Az útvonal-irányító szolgálat figyelembe veszi a jármű magassága és súlya miatt immúnis korlátozásokat, vagy ha a jármű veszélyes rakományt szállít. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * A forgalom megjelenítése a térképen
> * Utazási módot deklaráló útvonal-lekérdezések létrehozása
> * Több útvonal megjelenítése a térképen

## <a name="prerequisites"></a>Előfeltételek
Mielőtt folytatna, kövesse a [Fiók létrehozása című](quick-demo-map-app.md#create-an-account-with-azure-maps) útmutató utasításait, és válassza ki az S1 tarifacsomagot. Kövesse az [elsődleges kulcs bekésezésének](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseit a fiók elsődleges kulcsának lekérni. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](how-to-manage-authentication.md)című témakörben talál.

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

3. Adja hozzá az alábbi JavaScript-kódot a `GetMap` függvényhez. Cserélje le `<Your Azure Maps Key>` a karakterláncot a Térkép-fiókból másolt elsődleges kulcsra.

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

    Az `atlas.Map` osztály egy vizuális és interaktív webtérkép vezérlését biztosítja, és az Azure Map Control API összetevője.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>A forgalom megjelenítése

1. Adja hozzá a térképhez a forgalom megjelenítését. A `ready` térképek esemény megvárja, amíg a térképek erőforrások betöltése, és készen áll a biztonságos interakcióra.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    A térkép `ready` eseménykezelőjében a térképen a `relative`forgalomáramlás beállítása a , amely az út szabad áramláshoz viszonyított sebessége. Az út `absolute` sebességére is állíthatja ezt, vagy a `relative-delay` sebességre, amely a relatív sebességet jeleníti meg, ha eltér az üres úton elérhető sebességtől.

2. Mentse a **MapTruckRoute.html** fájlt, és frissítse a lapot a böngészőben. Ha kapcsolatba lép a térképpel, és ráközelít Los Angelesre, akkor az utcákat az aktuális forgalmi adatokkal kell látnia.

   ![Forgalom megtekintése a térképen](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban két útvonalat számítunk ki és jelenítünk meg a térképen. Az egyik alapjául a személyautók, a másik alapjául a teherautók által használható utak szolgálnak. Rendereléskor megjelenik egy szimbólum ikon az útvonal kezdetéhez és végéhez, és különböző színű vonalakat az egyes útvonalútvonalakhoz.

1. A térkép inicializálása után adja `ready` hozzá a következő JavaScript-kódot a térképek eseménykezelőjéhez.

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
    
    A térképek `ready` eseménykezelőjében egy adatforrás jön létre az útvonalvonalak, valamint a kezdő- és végpontok tárolására. A rendszer egy vonalréteget hoz létre, majd csatol az adatforráshoz az útvonal megjelenítési módjának meghatározásához. A vonalvastagságok és a színek kifejezésekkel kérhetők le az útvonal tulajdonságaiból. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ez biztosítja, hogy az útvonalvonal ne fedje le az útburkolati címkéket. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg a kezdő- és végpontok megjelenítésének módját. Ebben az esetben a program kifejezéseket adott hozzá az ikonkép és a szöveges címke adatainak beolvasásához az egyes pontobjektumok tulajdonságaiból. 
    
2. A jelen oktatóanyag esetében állítson be indulási pontnak egy Fabrikam nevű fiktív vállalatot Seattle-ben, célpontnak pedig a Microsoft irodáját. A térképek `ready` eseménykezelő, adja hozzá a következő kódot.

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

    Az indulási és célpontokat a rendszer hozzáadja az adatforráshoz. Az indulási és célpontokhoz tartozó határolókeret kiszámítása az `atlas.data.BoundingBox.fromData` függvénnyel történik. Ez a határolókeret segítségével beállíthatja a térképkamerák `map.setCamera` nézetét a teljes útvonalon a funkció használatával. A rendszer kitöltést is hozzáad a szimbólumikonok képpontban kifejezett méreteinek kompenzálásához.

4. Mentse a fájlt, majd frissítse a böngészőt, hogy a gombostűk megjelenjenek a térképen. Most a térkép seattle-i. Láthatja, hogy a kezdőpontot a kerek kék tű, a kék pedig a célpontot jelöli.

   ![Az indulási és célpontokat jelölő térkép megtekintése](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>útvonalak megjelenítése utazási mód alapján rendezve.

Ez a szakasz bemutatja, hogyan használhatja a Maps útvonalszolgáltatás API-t. Az útvonal API-t több útvonal megkeresésére használja egy adott kezdőponttól a végpontig, a szállítási mód alapján. Az útvonalszolgáltatás API-kat biztosít a *leggyorsabb,* *legrövidebb,* *öko*vagy *izgalmas* útvonalak megtervezéséhez. Nem csak az API-k terv útvonalak két hely között, de ők is figyelembe veszik az aktuális forgalmi feltételeket. 

Az útvonal API lehetővé teszi a felhasználók számára, hogy tervezzen útvonalakat a jövőben az Azure kiterjedt történelmi forgalmi adatbázis használatával. Az API képes megjósolni az útvonal időtartamát egy adott napon és időpontban. További információért lásd a [getRouteDirections API-t](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) ismertető témakört. 

Az alábbi kódblokkok mindegyike hozzá kell adni **a térkép terhelés eventListener** annak érdekében, hogy a terhelés után a térkép teljesen betöltődik.

1. A GetMap függvényben adja hozzá a következőket a Javascript-kódhoz.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   A `SubscriptionKeyCredential` létrehoz `SubscriptionKeyCredentialPolicy` egy hitelesítési HTTP-kérelmek et az Azure Maps az előfizetési kulcs. A `atlas.service.MapsURL.newPipeline()` házirend `SubscriptionKeyCredential` veszi, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) példányt. Az `routeURL` Azure Maps [Route-műveletek](https://docs.microsoft.com/rest/api/maps/route) URL-címét jelöli.

2. A hitelesítő adatok és az URL beállítása után adja hozzá a következő JavaScript-kódot, hogy az USHazmatClass2 osztályozott rakományt szállító teherautó hozlétre egy útvonalat az elejétől a végpontig, és jelenítse meg az eredményeket.

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

    Ez a kódrészlet a fenti lekérdezések az Azure Maps útválasztási szolgáltatás a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) metóduson keresztül. Az útvonalvonal ezután kinyeri a GeoJSON szolgáltatásgyűjteménya a `geojson.getFeatures()` metódussal kinyert válaszból. Ezután az útvonalvonal hozzáadódik az adatforráshoz. A 0 index biztosítja, hogy az adatforrás bármely más sora előtt jelenik meg. Ez azért történik, mert a teherautó útvonalának kiszámítása gyakran lassabb, mint egy autó útvonalának kiszámítása. Ha a teherautó útvonalvonala az autó útvonala után kerül az adatforráshoz, akkor felette jelenik meg. A teherautó útvonalvonalához két tulajdonság, egy szép kék árnyalatú körvonalszín és kilenc képpont körvonalszélesség kerül hozzáadásra.

3. Adja hozzá a következő JavaScript-kódot egy autó útvonalának létrehozásához és az eredmények megjelenítéséhez.

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

    Ez a kódrészlet a fenti lekérdezések az Azure Maps útválasztási szolgáltatás a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) metóduson keresztül. Az útvonalvonal ezután kinyeri a GeoJSON szolgáltatásgyűjteménya a `geojson.getFeatures()` metódussal kinyert válaszból. Ezután az útvonalvonal hozzáadódik az adatforráshoz. Az autó útvonalvonalához két tulajdonság, egy lila árnyalatú körvonalszín és öt képpont körvonalszélesség kerül hozzáadásra.  

4. Mentse a **MapTruckRoute.html** fájlt, és frissítse a böngészőt az eredmény megtekintéséhez. A Maps API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg.

    ![Prioritás szerint rendezett útvonalak az Azure Route Service-szel](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A teherautó útvonal a vastag kék, és az autó útvonal vékony lila. Az autó útvonal megy át Lake Washington keresztül I-90, amely megy keresztül alagutak alatt lakott területeken. Mivel az alagutak közel vannak a lakóterületekhez, a veszélyes hulladékrakomány korlátozott. A teherautó útvonala, amely meghatározza az USHazmatClass2 rakománytípust, egy másik autópálya használatára irányul.

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

A következő oktatóanyag bemutatja egy egyszerű tárolókereső létrehozásának folyamatát az Azure Maps használatával.

> [!div class="nextstepaction"]
> [Üzletkereső létrehozása az Azure Maps használatával](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)