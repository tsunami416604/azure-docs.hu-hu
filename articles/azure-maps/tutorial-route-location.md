---
title: Útvonalkeresés az Azure Mapsszel | Microsoft Docs
description: Útvonal keresése egy hasznos helyhez az Azure Mapsszel
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b17a9660e16a1cb05c088e97d4ad18dd20fd4216
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588789"
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
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>

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

3. Adja hozzá az alábbi JavaScript-kódot a `GetMap` függvényhez. A **\<Your Azure Maps Key\>** sztringet cserélje le a Maps-fiókból kimásolt elsődleges kulcsra.

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

    Az Azure Térkép vezérlőelem API **atlas.Map** összetevőjével egy vizuális és interaktív webes térkép vezérelhető.

4. Mentse a fájlt, és nyissa meg a böngészőben. Ekkor már rendelkezik egy egyszerű, fejleszthető térképpel.

   ![Egyszerű térkép megtekintése](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban a program egy egyszerű útvonalat jelenít meg. Az útvonal elejét és végét egy-egy szimbólum, az útvonalat pedig egy vonal jelöli.

1. A térkép inicializálása, után adja hozzá a következő JavaScript-kódot.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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

    A rendszer hozzáad a térképhez egy betöltési eseményt, amely a térkép erőforrásainak teljes betöltését követően aktiválódik. A térkép betöltésiesemény-kezelőjében létrejön egy, az útvonal, illetve az indulási és célpontok tárolására szolgáló adatforrás. Egy sor réteg létrehozása és csatlakoztatása a meghatározásához, hogy az útvonal sor jelenik meg az adatforráshoz. Az útvonal sor jelenik meg a árnyalatának 5 képpont és lekerekített sor illesztések és caps szélességének kék. A rendszer hozzáad egy szűrőt, hogy a réteg csak a GeoJSON LineString típusú adatokat jelenítse meg. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ezzel biztosítható, hogy az útvonal ne takarja ki az utakhoz tartozó feliratokat. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg az indulási és célpontok megjelenítését. Itt hozzáadott kifejezések kérik le az ikonképekkel és a szövegfeliratokkal kapcsolatos információkat az egyes pontobjektumok tulajdonságaiból.

2. A jelen oktatóanyag esetében a Microsoft-Kampusz, valamint a végpontot, egy budapesti benzinkutat kezdőpontjának beállítása. A térkép betöltésiesemény-kezelőjében adja hozzá az alábbi kódot.

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

    Ez a kód létrehozza a két [GeoJSON-pont objektumok](https://en.wikipedia.org/wiki/GeoJSON) , amelyek az útvonal indulási és vége, és a pontok hozzáadja az adatforrást. A rendszer minden ponthoz hozzáad egy-egy `title` és `icon` tulajdonságot. Az utolsó blokk beállítja a kamera-nézet az indulási és célpontját szélességi és hosszúsági adatait használva, a térkép segítségével [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonság.

3. Mentse a **MapRoute.html** fájlt, és frissítse a böngészőt. Most már a térkép felett Seattle középre igazított, és a kék PIN-kódot a kezdőpontja jelölés és a round kék PIN-kód a Befejezés pont jelölés látható.

   ![A kiindulási és végpontokat jelölő térkép megtekintése](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Útvonal keresése

Ez a szakasz bemutatja, hogyan használhatja az Azure Maps route service API-t az útvonal indulási pontról záró megkereséséhez. Az útvonal-szolgáltatás API-kat biztosít a két hely közötti *leggyorsabb*, *legrövidebb*, *leggazdaságosabb* vagy *leglátványosabb* útvonal megtervezéséhez. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. További információ: [Útvonal keresése](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Az alábbi funkciók mindegyikét fel kell vennie **a térképbetöltés eventListener elemébe**, hogy a térkép teljes betöltése után betöltődjenek.

1. A függvény a GetMap adja hozzá a következő Javascript-kódot.

    ```Javascript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```
   A **SubscriptionKeyCredential** létrehoz egy **SubscriptionKeyCredentialPolicy** az előfizetési kulcsot az Azure Maps HTTP-kérések hitelesítéséhez. A **atlas.service.MapsURL.newPipeline()** veszi a **SubscriptionKeyCredential** házirend, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) példány. A **routeURL** egy URL-címet jelöli az Azure Maps [útvonal](https://docs.microsoft.com/rest/api/maps/route) műveleteket.

2. Miután beállította a hitelesítő adatok és az URL-címet, adja hozzá a következő JavaScript-kódot az útvonal kezdő végpontot létrehozni. A **routeURL** kéréseket az Azure Maps route service útvonalat kiszámításához. A válaszból GeoJSON funkció gyűjtemény majd ki kell olvasni használatával a **geojson.getFeatures()** metódust, és hozzáadja az adatforráshoz.

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

Az oktatóanyag kódmintáját itt érheti el:

> [Útvonalkeresés az Azure Maps használatával](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[A minta megtekintése élőben](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

A következő oktatóanyag bemutatja, hogyan hozhat létre egy útvonal-lekérdezést olyan korlátozásokkal, mint az utazás módja vagy a rakomány típusa, majd megjelenít több útvonalat ugyanazon a térképen.

> [!div class="nextstepaction"]
> [Útvonalak keresése különböző utazási módokhoz](./tutorial-prioritized-routes.md)
