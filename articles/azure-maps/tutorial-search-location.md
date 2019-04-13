---
title: Keresés az Azure Maps használatával | Microsoft Docs
description: Közeli hasznos hely keresése az Azure Maps használatával
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 125e0c4331eea105ffc201bd1f5f26bdbec1c553
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549388"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Közeli hasznos helyek keresése az Azure Maps használatával

Ez az oktatóanyag bemutatja, hogyan állíthat be egy fiókot az Azure Maps használatához, és hogyan használhatja a Maps API-kat egy hasznos hely kereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Maps-fiók létrehozása
> * A Maps-fiók elsődleges kulcsának lekérése
> * Új weblap létrehozása a térképkezelési API használatával
> * A Maps keresőszolgáltatásának használata egy közeli hasznos hely kereséséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps-fiók létrehozása

Hozzon létre egy új Maps-fiókot az alábbi lépésekkel:

1. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A *Keresés a Marketplace-en* mezőbe írja be a következőt: **Maps**.
3. Az *Eredmények* részben válassza a **Maps** lehetőséget. Kattintson a térkép alatt megjelenő **Létrehozás** gombra.
4. A **Maps-fiók létrehozása** lapon adja meg a következő értékeket:
    * A fiókhoz használni kívánt *előfizetés*.
    * A fiókhoz tartozó *erőforráscsoport* neve. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    * Az új fiók *neve*.
    * A *tarifacsomag* ehhez a fiókhoz.
    * Olvassa el a *licencfeltételeket* és az *adatvédelmi nyilatkozatot*, és jelölje be az azok elfogadását jelző jelölőnégyzetet.
    * Kattintson a **Létrehozás** gombra.

![Maps-fiók létrehozása a portálon](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>A fiók elsődleges kulcsának lekérése

A Maps-fiók sikeres létrehozását követően kérje le azt a kulcsot, amely lehetővé teszi a Maps API-k lekérdezését.

1. Nyissa meg a Maps-fiókot a portálon.
2. A beállítások területen válassza ki a **hitelesítési**.
3. Másolja ki az **elsődleges kulcsot** a vágólapra. Mentse a helyi gépre, hogy később felhasználhassa ebben az oktatóanyagban.

![Elsődleges kulcs lekérése a portálon](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Új térkép létrehozása

A térképkezelési API egy kényelmes ügyféloldali kódtár, amely segítségével a Maps könnyedén integrálható a webalkalmazásokba. A kódtár elrejti a REST-szolgáltatás puszta hívásainak összetettségét, a stílusos és testre szabható összetevők segítségével pedig növeli a munkavégzés hatékonyságát. Az alábbi lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a térképkezelési API.

1. A helyi gépén hozzon létre egy új fájlt **MapSearch.html** néven.
2. Adja a következő HTML-összetevőket a fájlhoz:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Figyelje meg, hogy a HTML-fejléc tartalmazza az Azure Térkép vezérlőelem-kódtárban található CSS- és JavaScript-erőforrásfájlokat. Tekintse meg a laptörzs `onload` eseményét, amely a laptörzs betöltését követően meghívja a `GetMap` függvényt. A `GetMap` függvény tartalmazni fogja a beágyazott JavaScript-kódot az Azure Maps API-k elérésére.

3. Adja hozzá a következő JavaScript-kódot a HTML-fájl `GetMap` függvényéhez. Cserélje le a karakterláncot `<Your Azure Maps Key>` elsődleges kulccsal, a Maps-fiók fájlból kimásolt.

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

   Ez a szakasz inicializálja az Azure Maps-fiók kulcsához tartozó térképkezelési API-t. `atlas` a névtér, amely tartalmazza az API-t és a hozzá tartozó vizuális összetevőket van. `atlas.Map` a vezérlőelem vizuális és interaktív webes térkép biztosít.

4. Mentse a fájl módosításait, és nyissa meg a HTML-oldalt egy böngészőben. Ez az a lehető legegyszerűbb térkép, amelyeknek elvégezhetők meghívásával `atlas.Map` a fiók kulcsának használatával.

   ![A térkép megtekintése](./media/tutorial-search-location/basic-map.png)

5. A `GetMap` függvényben adja hozzá az alábbi JavaScript-kódot a térkép inicializálása után.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   A kód szegmens egy `ready` eseményt vesz fel a térképet, amely értesíti, ha a térkép erőforrások lett betöltve, és a térkép készen áll a érhető el. A térkép `ready` eseménykezelő, egy adatforrás létrehozása eredmény adatok tárolására. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg az adatforrásban található eredményadatok megjelenítését. Esetünkben ez egy sötétkék, kerek gombostű ikon, amely az eredmény koordinátái fölött jelenik meg, és amelyet más ikonok átfedhetnek. Az eredmény réteget a térkép rétegek kerül.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Keresési képességek hozzáadása

Ez a szakasz bemutatja, hogyan használható a Maps [Search API](https://docs.microsoft.com/rest/api/maps/search) található egy hasznos hely megkereséséhez a térképen. Ez az API egy fejlesztőknek szánt RESTful API, amely lehetővé teszi a címek, hasznos helyek és egyéb földrajzi adatok keresését. A Search szolgáltatás szélességi és hosszúsági koordinátákat rendel egy adott címhez. Az alább ismertetett **Szolgáltatásmodul** segítségével helyekre kereshet a Maps Search API használatával.

### <a name="service-module"></a>Szolgáltatásmodul

1. A térkép `ready` eseménykezelő, hozza létre a search szolgáltatás URL-CÍMÉT adja hozzá a következő Javascript-kódot.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   A `SubscriptionKeyCredential` létrehoz egy `SubscriptionKeyCredentialPolicy` az előfizetési kulcsot az Azure Maps HTTP-kérések hitelesítéséhez. A `atlas.service.MapsURL.newPipeline()` veszi a `SubscriptionKeyCredential` házirend, és létrehoz egy [folyamat](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) példány. A `searchURL` egy URL-címet jelöli az Azure Maps [keresési](https://docs.microsoft.com/rest/api/maps/search) műveleteket.

2. Ezután adja hozzá a következő szkriptblokkot a keresőlekérdezés felépítéséhez. A kódrészlet a Search Service alapszintű Fuzzy Search Service (Intelligens keresés) keresési API-ját használja. A Fuzzy Search Service a legtöbb nem teljes egyezésű bemeneti adat, például címek, helyek vagy hasznos helyek (POI) kezelésére is képes. Ez a kód a megadott hosszúsági és szélességi foktól a megadott sugarú közeli Benzinkút keres. A válaszból GeoJSON funkció gyűjtemény majd ki kell olvasni használatával a `geojson.getFeatures()` metódust, és hozzáadja az adatforráshoz, ami az adatok a térképen a szimbólum réteg keresztül rendereli a rendszer automatikusan eredményez. A szkript utolsó része a térkép kameranézetét állítja be az eredmények határolókeretével, a térkép [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Mentse a **MapSearch.html** fájlt, és frissítse a böngészőt. Meg kell jelennie, hogy a térkép van ingyenes Seattle a jelölés Benzinkút területén helyeinek ciklikus kék PIN-kódok.

   ![A keresési eredményeket tartalmazó térkép megtekintése](./media/tutorial-search-location/pins-map.png)

4. A térkép által renderelt nyers adatok megtekintéséhez írja be a böngészőbe a következő HTTPRequest kérést. Cserélje le a \<Your Azure Maps Key\> értéket az Ön által használt elsődleges kulcsra.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=2&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Ezen a ponton a MapSearch lap meg tudja jeleníteni az intelligens keresési lekérdezés által visszaadott hasznos helyek helyét. Adjunk hozzá néhány interaktív képességet, valamint további információkat a helyekről.

## <a name="add-interactive-data"></a>Interaktív adatok hozzáadása

A létrehozott térkép ezen a ponton még csak a keresési eredmények hosszúsági/szélességi adatait vizsgálja. Ha azonban megtekinti a Maps Search szolgáltatás által visszaadott nyers JSON-fájlt, látni fogja, hogy az további információkat tartalmaz az egyes benzinkutakról, például a nevüket és a címüket. Ezeket az adatokat interaktív felugró ablakokkal beépítheti a térképbe.

1. Adja hozzá a következő kódsorokat a térképen `ready` eseménykezelőt a kódot az intelligens keresés szolgáltatás lekérdezése után. Ez létrehozza a felugró ablak egy példányát, és hozzáad egy rámutatási eseményt a szimbólumréteghez.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Az API-t `sup` olyan információkat nyújt a megfelelő helyre a térképen rögzített ablak. 

2. Az a *parancsfájl* címke, miután a `GetMap` működik, adja hozzá a következő kódot az előugró eredmény információkat a moused megjelenítése.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Mentse a fájlt, és frissítse a böngészőt. A térkép most felugró információs ablakokat jelenít meg böngészőben, ha az egérmutatót valamelyik gombostű fölé viszi.

    ![Azure Térkép vezérlőelem és Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Maps-fiók létrehozása
> * A fiók elsődleges kulcsának lekérése
> * Új weblap létrehozása a Térkép vezérlőelem API használatával
> * A Search Service használata egy közeli hasznos hely kereséséhez

> [!div class="nextstepaction"]
> [Teljes forráskód megtekintése](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Élő mintájának megtekintése](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

A következő oktatóanyag bemutatja, hogyan lehet megjeleníteni egy útvonalat két hely között.

> [!div class="nextstepaction"]
> [Útvonal egy adott úti célhoz](./tutorial-route-location.md)
