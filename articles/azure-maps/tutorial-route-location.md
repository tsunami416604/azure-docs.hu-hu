---
title: 'Oktatóanyag: Útvonal keresése egy helyre | Microsoft Azure Maps'
description: Ez az oktatóanyag bemutatja, hogyan jelenítheti meg az útvonalat egy térképen (érdekes hely) a Microsoft Azure Maps útválasztási szolgáltatás használatával.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98c36176ecd2996e5f735c52017162a076ef4bde
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333765"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Oktatóanyag: Útvonal egy érdekes ponthoz az Azure Maps használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Maps-fiókot és a Route Service SDK-t a hasznos helyekre vezető útvonalak megkereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új weblap létrehozása a térképkezelési API használatával
> * Címkoordináták beállítása
> * Hasznos helyre vezető útvonal lekérdezése a Route Service-ből

## <a name="prerequisites"></a>Előfeltételek

Mielőtt folytatna, kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps)című útmutató utasításait, és szüksége van egy S1-es tarifacsomaggal rendelkező előfizetésre. Kövesse az [elsődleges kulcs bekésezésének](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseit a fiók elsődleges kulcsának lekérni. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](how-to-manage-authentication.md)című témakörben talál.

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

    Az Azure Maps-vezérlő API `atlas.Map` összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban a program egy egyszerű útvonalat jelenít meg. Az útvonal elejét és végét egy-egy szimbólum, az útvonalat pedig egy vonal jelöli.

1. A térkép inicializálása után adja hozzá a következő JavaScript-kódot.

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
    
    A térképek `ready` eseménykezelőjében egy adatforrás jön létre az útvonalvonal, valamint a kezdő- és végpontok tárolására. A rendszer egy vonalréteget hoz létre, majd csatol az adatforráshoz az útvonal megjelenítési módjának meghatározásához. Az útvonal vonal lesz, mint egy szép árnyalatú kék. Öt képpont széles, lekerekített vonalillesztések és sapkák. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ezzel biztosítható, hogy az útvonal ne takarja ki az utakhoz tartozó feliratokat. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg a kezdő- és végpontok renderelésének módját. Ebben az esetben a program kifejezéseket adott hozzá az ikonkép és a szöveges címke adatainak beolvasásához az egyes pontobjektumok tulajdonságaiból. 
    
2. A jelen oktatóanyag esetében a Microsoftot állítsa be indulási pontnak, célpontnak pedig egy seattle-i benzinkutat. A térképek `ready` eseménykezelő, adja hozzá a következő kódot.

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

    Ez a kód két [GeoJSON-pontobjektumot](https://en.wikipedia.org/wiki/GeoJSON) hoz létre az útvonal kezdő- és végpontjainak ábrázolására, és hozzáadja a pontokat az adatforráshoz. A rendszer minden ponthoz hozzáad egy-egy `title` és `icon` tulajdonságot. Az utolsó blokk a kameranézetet a kezdő- és végpontok szélességi és hosszúsági foka alapján állítja be a Térkép [SetCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával.

3. Mentse a **MapRoute.html** fájlt, és frissítse a böngészőt. Most a térkép seattle-i középre van, és láthatja, hogy a kék tű jelöli a kezdőpontot, és a kerek kék tű, amely a célpontot jelöli.

   ![Útvonalak kezdő- és végpontjának megtekintése a térképen](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Útvonal keresése

Ez a szakasz bemutatja, hogyan használhatja az Azure Maps útvonalszolgáltatás API-t. Az útvonalszolgáltatás API-ja megkeresi az útvonalat egy adott kezdőponttól a végpontig. Ezen a szolgáltatáson belül vannak OLYAN API-k, amelyek két hely közötti *leggyorsabb,* *legrövidebb,* *eco*vagy *izgalmas* útvonalakat terveznek. Ez a szolgáltatás azt is lehetővé teszi a felhasználók számára, hogy tervezzen útvonalakat a jövőben az Azure kiterjedt történelmi forgalmi adatbázis használatával. A felhasználók láthatják az útvonal időtartamának előrejelzését bármely kiválasztott napon és időpontban. További információ: [Útvonal keresése](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Az alábbi funkciókat hozzá kell adni **a térképen kész eventListener** annak érdekében, hogy a betöltésután a térkép erőforrások készen állnak az elérésre.

1. A GetMap függvényben adja hozzá a következőket a JavaScript-kódhoz.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   A `SubscriptionKeyCredential` létrehoz `SubscriptionKeyCredentialPolicy` egy hitelesítési HTTP-kérelmek et az Azure Maps az előfizetési kulcs. A `atlas.service.MapsURL.newPipeline()` házirend `SubscriptionKeyCredential` veszi, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) példányt. Az `routeURL` Azure Maps [Route-műveletek](https://docs.microsoft.com/rest/api/maps/route) URL-címét jelöli.

2. A hitelesítő adatok és az URL beállítása után adja hozzá a következő JavaScript-kódot az útvonal létrehozásához az elejétől a végpontig. Az `routeURL` Azure Maps útvonalszolgáltatás az útvonalútvonal kiszámításához kéri. A válaszgeoJSON szolgáltatásgyűjteményét ezután kinyeri a `geojson.getFeatures()` metódus, és hozzáadja az adatforráshoz.

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
