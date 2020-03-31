---
title: Az Azure Maps térbeli IO-moduljának használata | Microsoft Azure Maps
description: Ismerje meg, hogyan használhatja az Azure Maps Web SDK által biztosított térbeli I/o-modult. Ez a modul robusztus funkciókat biztosít, amelyek megkönnyítik a fejlesztők számára a térbeli adatok integrálását az Azure Maps web sdk-vel.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcfeebc1fcb96cfdf6ea802293eb4027f339815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335212"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Az Azure Maps térbeli IO-moduljának használata

Az Azure Maps Web SDK biztosítja a **térbeli IO-modult,** amely integrálja a térbeli adatokat az Azure Maps web SDK JavaScript vagy TypeScript használatával. A modul robusztus funkciói lehetővé teszik a fejlesztők számára, hogy:

- [Gyakori téradatfájlok olvasása és írása](spatial-io-read-write-spatial-data.md). A támogatott fájlformátumok a következők: KML, KMZ, GPX, GeoRSS, GML, GeoJSON és CSV fájlok, amelyek térbeli információkat tartalmazó oszlopokat tartalmaznak. Támogatja a jól ismert szöveget is (WKT).
- [Csatlakozzon az Open Geospatial Consortium (OGC) szolgáltatásaihoz, és integrálja az Azure Maps webes SDK-val. Az Overlay Web Map Services (WMS) és a Web Map Tile Services (WMTS) rétegekként a térképen](spatial-io-add-ogc-map-layer.md).
- [Adatok lekérdezése webszolgáltatásban (WFS).](spatial-io-connect-wfs-service.md)
- [A stílusadatokat tartalmazó összetett adatkészletek átfedése, amelyek minimális kód használatával automatikusan lehetővé teszik azok megjelenítését.](spatial-io-add-simple-data-layer.md)
- [Használja ki a nagy sebességű XML- és tagolt fájlolvasó és íróosztályokat.](spatial-io-core-operations.md)

Ebben az útmutatóban megtudhatja, hogyan integrálhatja és használhatja a térbeli IO-modult egy webalkalmazásban.

> [!WARNING]
> Csak olyan adatokat és szolgáltatásokat használjon, amelyek megbízható forrásból származnak, különösen akkor, ha egy másik tartományból hivatkozik rá. A térbeli IO-modul lépéseket tesz a kockázat minimalizálása érdekében, azonban a legbiztonságosabb megközelítés túl nem teszi lehetővé semmilyen danagerous adatokat az alkalmazás kezdeni. 

## <a name="prerequisites"></a>Előfeltételek

A térbeli IO-modul használata előtt [azure Maps-fiókot kell készítenie,](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) és be kell [szereznie a fiók elsődleges előfizetési kulcsát.](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>A térbeli IO modul telepítése

Az Azure Maps térbeli I/o modult a következő két lehetőség egyikével töltheti be:

* A globálisan üzemeltetett Azure CDN az Azure Maps térbeli IO-modulhoz. Ehhez a beállításhoz a HTML-fájl `<head>` elemében a JavaScriptre mutató hivatkozást kell hozzáadni.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Az [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) forráskódja helyileg betölthető, majd az alkalmazással üzemeltethető. Ez a csomag TypeScript-definíciókat is tartalmaz. Ehhez a beállításhoz használja a következő parancsot a csomag telepítéséhez:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Ezután adjon hozzá egy hivatkozást `<head>` a JavaScriptre a HTML-dokumentum elemében:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>A Térbeli IO modul használata

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be az Azure Maps Web SDK-t, és inicializálja a térképvezérlőt. Tekintse meg az [Azure Maps térképvezérlési](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) útmutató a részleteket. Ha végzett ezzel a lépéssel, a HTML-fájlnak így kell kinéznie:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Töltse be az Azure Maps térbeli IO-modult. Ebben a gyakorlatban használja a CDN az Azure Maps térbeli IO-modul. Adja hozzá az `<head>` alábbi hivatkozást a HTML-fájl eleméhez:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicializálja a `datasource`, és adja hozzá az adatforrást a térképhez. Inicializálja a `layer`, és adja hozzá az adatforrást a térképréteghez. Ezután jelenítse meg az adatforrást és a réteget is. Mielőtt lefelé görgetne a teljes kód megtekintéséhez a következő lépésben, gondolja át, hogy mi a legjobb hely az adatforrás- és rétegkódrészletek elhelyezéséhez. Emlékezzünk vissza, hogy mielőtt programozottmódon manipulálnánk a térképet, meg kell várnunk, amíg a térkép erőforrás készen áll.

    ```javascript
    var datasource, layer;
    ```

    és

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Összerakva az egészet, a HTML-kódnak a következő kódnak kell kinéznie. Ez a minta bemutatja, hogyan lehet olvasni egy XML-fájlt egy URL-címről. Ezután töltse be és jelenítse meg a fájl jellemzőadatait a térképen. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Ne felejtse el helyettesíteni `<Your Azure Maps Key>` az elsődleges kulcsot. Nyissa meg a HTML-fájlt, és az alábbihoz hasonló eredmények jelennek meg:

    <center>

    ![Példa térbeli adatokra](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>További lépések

Az itt bemutatott funkció csak egyike a térbeli IO modul számos funkciójának. Az alábbi útmutatókból megtudhatja, hogyan használhatja a térbeli IO modul egyéb funkcióit:

> [!div class="nextstepaction"]
> [Egyszerű adatréteg hozzáadása](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Téradatok olvasása és írása](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC-térképréteg hozzáadása](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Csatlakozás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Az alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)

Tekintse meg az Azure Maps térbeli IO dokumentációját:

> [!div class="nextstepaction"]
> [Az Azure Maps térbeli IO-csomagja](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
