---
title: 'Oktatóanyag: útvonal megkeresése egy helyhez | Microsoft Azure térképek'
description: Ebből az oktatóanyagból megtudhatja, hogyan teheti át az útvonalat egy adott helyre (érdekes hely) a Microsoft Azure Maps útválasztási szolgáltatás használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3a9304b5135ca682008cb3ff045b43ae297dc950
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133265"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Oktatóanyag: egy hasznos ponthoz való továbbítás Azure Maps használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Maps-fiókot és a Route Service SDK-t a hasznos helyekre vezető útvonalak megkereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * Címkoordináták beállítása
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)című témakör utasításait S1 díjszabási csomaggal. A fiók elsődleges kulcsának lekéréséhez kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakör lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Új térkép létrehozása

Az alábbi lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a térképkezelési API.

1. A helyi gépén hozzon létre egy új fájlt **MapRoute.html** néven.
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

    Az Azure Maps-vezérlő API `atlas.Map` összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban a program egy egyszerű útvonalat jelenít meg. Az útvonal elejét és végét egy-egy szimbólum, az útvonalat pedig egy vonal jelöli.

1. A Térkép inicializálását követően adja hozzá a következő JavaScript-kódot.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    A Maps- `ready` eseménykezelőben létrejön egy adatforrás, amely az útvonal vonalát, valamint a kezdő és záró pontokat tárolja. A rendszer egy vonalréteget hoz létre, majd csatol az adatforráshoz az útvonal megjelenítési módjának meghatározásához. Az útvonal vonalát a kék szín szép árnyalata jeleníti meg a rendszer. Szélessége öt képpont, lekerekített vonal és Caps. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ezzel biztosítható, hogy az útvonal ne takarja ki az utakhoz tartozó feliratokat. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg a kezdő és a záró pontok megjelenítésének módját. Ebben az esetben a kifejezések hozzá lettek adva, hogy beolvassák az ikon képének és a szöveg feliratának adatait az egyes pontok objektumainak tulajdonságaiból. 
    
2. A jelen oktatóanyag esetében a Microsoftot állítsa be indulási pontnak, célpontnak pedig egy seattle-i benzinkutat. A Maps- `ready` eseménykezelőben adja hozzá a következő kódot.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Ez a kód két [GeoJSON pontot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre, amelyek az útvonal kezdő és záró pontjait jelölik, és hozzáadja a pontokat az adatforráshoz. A rendszer minden ponthoz hozzáad egy-egy `title` és `icon` tulajdonságot. Az utolsó blokk a Térkép [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával állítja be a kamera nézetét a kezdő-és végpontok szélességi és hosszúsági pontjainak használatával.

3. Mentse a **MapRoute.html** fájlt, és frissítse a böngészőt. A Térkép középpontba került a Seattle-ben, és láthatja, hogy a kiindulási pontot és a kerek kék PIN-kódot jelölő kék PIN-kód megjelöli a befejezési pontot.

   ![Útvonalak megnézetének kezdete és vége a térképen](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Útvonal keresése

Ez a szakasz bemutatja, hogyan használhatja a Azure Maps Route Service API-t. Az útvonal-szolgáltatás API megkeresi az útvonalat egy adott kezdőponttól a végpontig. Ezen a szolgáltatáson belül vannak olyan API-k, amelyekkel megtervezheti a *leggyorsabb*, *legrövidebb*, *Eco*vagy *izgalmas* útvonalakat két helyszín között. Ez a szolgáltatás azt is lehetővé teszi, hogy a felhasználók a jövőben is tervezzenek útvonalakat az Azure kiterjedt történelmi forgalmú adatbázisának használatával. A felhasználók megtekinthetik az útvonal időtartamának előrejelzését bármely kiválasztott napon és időpontban. További információ: [Útvonal keresése](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Az alábbi funkciók mindegyikét hozzá kell adni **a Térkép kész eventListener** , hogy azok bekerüljenek a Térkép erőforrásainak eléréséhez.

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

2. A hitelesítő adatok és az URL-cím beállítása után adja hozzá a következő JavaScript-kódot az útvonal létrehozásához az elejétől a végéig. A a `routeURL` Azure Maps Route szolgáltatást kéri az útvonal irányának kiszámításához. Ezután a válaszból Kinyer egy GeoJSON-gyűjteményt, `geojson.getFeatures()` és hozzáadja az adatforráshoz.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Mentse a **MapRoute.html** fájlt, és frissítse a webböngészőt. A Maps API-jaival való sikeres kapcsolat esetén a következőhöz hasonló térkép jelenik meg.

    ![Azure Térkép vezérlőelem és Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * Címkoordináták beállítása
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

> [!div class="nextstepaction"]
> [Teljes forráskód megtekintése](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Élő minta megtekintése](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

A következő oktatóanyag bemutatja, hogyan hozhat létre egy útvonal-lekérdezést olyan korlátozásokkal, mint az utazás módja vagy a rakomány típusa, majd megjelenít több útvonalat ugyanazon a térképen.

> [!div class="nextstepaction"]
> [Útvonalak keresése különböző utazási módokhoz](./tutorial-prioritized-routes.md)
