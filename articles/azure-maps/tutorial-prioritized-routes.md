---
title: Több útvonal az Azure Maps használatával | Microsoft Docs
description: Útvonalak keresése különböző utazási módokhoz az Azure Maps használatával
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d78f46d2d62ca9db9400e0f436a8c0358734a54e
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540516"
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
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css">
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

2. Mentse a **MapTruckRoute.html** fájlt, és frissítse a lapot a böngészőben. Ha használhatja a térképet, és a Los Angeles-i nagyítás megtekintheti a jelenlegi forgalom adatokkal az utcák.

   ![A forgalmi térkép megtekintése](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Az útvonal megjelenítésének meghatározása

Ebben az oktatóanyagban két útvonalat számítunk ki és jelenítünk meg a térképen. Az egyik alapjául a személyautók, a másik alapjául a teherautók által használható utak szolgálnak. A megjelenített utakon egy-egy szimbólum jelzi az útvonal elejét és végét, és különböző színű vonalak jelzik az egyes útvonalakat.

1. Inicializálása a térképen, után adja hozzá a következő JavaScript-kódot a térkép betöltési esemény.

    ```JavaScript
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
    ```

    A rendszer hozzáad a térképhez egy betöltési eseményt, amely a térkép erőforrásainak teljes betöltését követően aktiválódik. A térkép betöltésiesemény-kezelőjében létrejön egy, az útvonalak, illetve az indulási és célpontok tárolására szolgáló adatforrás. Egy sor réteg létrehozása és csatlakoztatása a meghatározásához, hogy az útvonal sor jelenik meg az adatforráshoz. A vonalvastagságok és a színek kifejezésekkel kérhetők le az útvonal tulajdonságaiból. A rendszer hozzáad egy szűrőt, hogy a réteg csak a GeoJSON LineString típusú adatokat jelenítse meg. A réteg térképhez való hozzáadásakor a rendszer átad egy `'labels'` értékű második paramétert is, amely azt határozza meg, hogy ez a réteg a térképfeliratok alatt jelenjen meg. Ezzel biztosítható, hogy az útvonal ne takarja ki az utakhoz tartozó feliratokat. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg az indulási és célpontok megjelenítését. Itt hozzáadott kifejezések kérik le az ikonképekkel és a szövegfeliratokkal kapcsolatos információkat az egyes pontobjektumok tulajdonságaiból.

2. Ebben az oktatóanyagban be a kezdő pont, mert az egy fiktív cég, a Seattle nevű Fabrikam, és a végpontot a Microsoft központjában épülethez. A térkép betöltésiesemény-kezelőjében adja hozzá az alábbi kódot.

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

    Az indulási és célpontokat a rendszer hozzáadja az adatforráshoz. Az indulási és célpontokhoz tartozó határolókeret kiszámítása az `atlas.data.BoundingBox.fromData` függvénnyel történik. A határolókeret használja, mint a teljes útvonalat a térképnézet kamerák beállításához a `map.setCamera` függvény. A rendszer kitöltést is hozzáad a szimbólumikonok képpontban kifejezett méreteinek kompenzálásához.

4. Mentse a fájlt, majd frissítse a böngészőt, hogy a gombostűk megjelenjenek a térképen. Most Seattle látható a térkép középpontjában, a kezdőpontot a kerek kék gombostű jelzi, a végpontot pedig a kék gombostű.

   ![Az indulási és célpontokat jelölő térkép megtekintése](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>útvonalak megjelenítése utazási mód alapján rendezve.

Ez a szakasz bemutatja, hogyan keresse meg a végpontot az utazás módja alapján több útvonal indulási pontról a Maps route service API használatával. Az útvonal-szolgáltatás API-kat biztosít a két hely közötti *leggyorsabb*, *legrövidebb*, *leggazdaságosabb* vagy *leglátványosabb* útvonal megtervezéséhez, az aktuális forgalmi viszonyokat figyelembe véve. A felhasználók előre is megtervezhetik az útvonalakat az Azure széles körű forgalmi adatbázisával, amely előre jelzi az útvonalak menetidejét bármely napon és időpontban. További információért lásd a [getRouteDirections API-t](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) ismertető témakört. Az összes alábbi kódblokkot fel kell vennie **a térképbetöltés eventListener elemébe**, hogy a térkép teljes betöltése után betöltődjenek.

1. A függvény a GetMap adja hozzá a következő Javascript-kódot.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   A **SubscriptionKeyCredential** létrehoz egy **SubscriptionKeyCredentialPolicy** az előfizetési kulcsot az Azure Maps HTTP-kérések hitelesítéséhez. A **atlas.service.MapsURL.newPipeline()** veszi a **SubscriptionKeyCredential** házirend, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) példány. A **routeURL** egy URL-címet jelöli az Azure Maps [útvonal](https://docs.microsoft.com/rest/api/maps/route) műveleteket.

2. Beállítása után hitelesítő adatok és az URL-címet, adja hozzá a következő JavaScript kódot létrehozni egy útvonalat a teherautó USHazmatClass2 végző célpont kezdetektől osztályba sorolt rakományt, és megjeleníti az eredményeket.

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

    A fenti kódrészletben keresztül az Azure Maps útválasztási szolgáltatás lekérdezi a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metódust. Az útvonal sor majd ki kell olvasni a válaszból, amelyet ki kell olvasni használatával GeoJSON funkció gyűjteményből a **geojson.getFeatures()** metódust. Az útvonal sor kerül az adatforráshoz. Hozzáadja a 0 – Győződjön meg arról, hogy így jelenik meg az adatforrás minden olyan sort előtt index is. Ez azért hasznos, mert a teherautós útvonalakat a rendszer általában lassabban számítja ki, mint a személyautókét, és ha egy teherautós útvonal egy személyautós után adódik hozzá az adatforráshoz, akkor fölötte fog megjelenni. A kód két tulajdonságot rendel a teherautós útvonalhoz: egy kék árnyalatú vonalszínt és 9 képpontos vonalvastagságot.

3. Adja hozzá a következő JavaScript-kódot egy autós útvonal létrehozni, és megjeleníti az eredményeket.

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

    A fenti kódrészletben keresztül az Azure Maps útválasztási szolgáltatás lekérdezi a [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metódust. Az útvonal sor majd ki kell olvasni a válaszból, amelyet ki kell olvasni használatával GeoJSON funkció gyűjteményből a **geojson.getFeatures()** metódust. Az útvonal sor kerül az adatforráshoz. A kód két tulajdonságot rendel a személyautós útvonalhoz: egy lila árnyalatú vonalszínt és 5 képpontos vonalvastagságot.  

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

Az oktatóanyag kódmintáját itt érheti el:

> [Több útvonal az Azure Maps használatával](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[A minta megtekintése élőben](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

A következő oktatóanyag azt ismerteti, az Azure Maps használatával egy egyszerű store kereső létrehozásának folyamatán.

> [!div class="nextstepaction"]
> [Az Azure Maps store lokátorok létrehozásához](./tutorial-create-store-locator.md)
