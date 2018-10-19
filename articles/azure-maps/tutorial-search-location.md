---
title: Keresés az Azure Maps használatával | Microsoft Docs
description: Közeli hasznos hely keresése az Azure Maps használatával
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 761674c5839f0513532355116db07604f9e9d9dc
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816820"
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
    * Az új fiók *neve*.
    * A fiókhoz használni kívánt *előfizetés*.
    * A fiókhoz tartozó *erőforráscsoport* neve. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    * Válassza ki az *erőforráscsoport helyét*.
    * Olvassa el a *licencfeltételeket* és az *adatvédelmi nyilatkozatot*, és jelölje be az azok elfogadását jelző jelölőnégyzetet.
    * Kattintson a **Létrehozás** gombra.

    ![Maps-fiók létrehozása a portálon](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>A fiók elsődleges kulcsának lekérése

A Maps-fiók sikeres létrehozását követően kérje le azt a kulcsot, amely lehetővé teszi a Maps API-k lekérdezését.

1. Nyissa meg a Maps-fiókot a portálon.
2. A beállítások szakaszában válassza a **Kulcsok** lehetőséget.
3. Másolja ki az **elsődleges kulcsot** a vágólapra. Mentse a helyi gépre, hogy később felhasználhassa ebben az oktatóanyagban.

    ![Elsődleges kulcs lekérése a portálon](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Új térkép létrehozása

A térképkezelési API egy kényelmes ügyféloldali kódtár, amely segítségével a Maps könnyedén integrálható a webalkalmazásokba. A kódtár elrejti a REST-szolgáltatás puszta hívásainak összetettségét, a stílusos és testre szabható összetevők segítségével pedig növeli a munkavégzés hatékonyságát. Az alábbi lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a térképkezelési API.

1. A helyi gépén hozzon létre egy új fájlt **MapSearch.html** néven.
2. Adja a következő HTML-összetevőket a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>

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
    Figyelje meg, hogy a HTML-fejléc tartalmazza az Azure Térkép vezérlőelem-kódtárban található CSS- és JavaScript-erőforrásfájlokat. Figyelje meg a HTML-fájl *törzséhez* adott *szkript* szakaszt is. Ez a szakasz tartalmazza az Azure Maps API-k elérésére szolgáló beágyazott JavaScript-kódot.

3. Adja hozzá a következő JavaScript-kódot a HTML-fájl *szkript* blokkjához. A **\<your account key\>** sztringet cserélje le a Maps-fiókból kimásolt elsődleges kulcsra.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Ez a szakasz inicializálja az Azure Maps-fiók kulcsához tartozó térképkezelési API-t. Az **Atlas** az a névtér, amely az API-t és a hozzá tartozó vizuális összetevőket tartalmazza. Az **Atlas.Map** biztosítja a vizuális és interaktív webes térkép vezérlőit.

4. Mentse a fájl módosításait, és nyissa meg a HTML-oldalt egy böngészőben. Ez a legegyszerűbb térkép, amelyet az **atlas.map** meghívásával és a fiókkulcs használatával létrehozhat.

   ![A térkép megtekintése](./media/tutorial-search-location/basic-map.png)

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Keresési képességek hozzáadása

Ez a szakasz bemutatja, hogyan használható a Maps Search API egy hasznos hely megkereséséhez a térképen. Ez az API egy fejlesztőknek szánt RESTful API, amely lehetővé teszi a címek, hasznos helyek és egyéb földrajzi adatok keresését. A Search szolgáltatás szélességi és hosszúsági koordinátákat rendel egy adott címhez. Az alább ismertetett **Szolgáltatásmodul** segítségével helyekre kereshet a Maps Search API használatával.

### <a name="service-module"></a>Szolgáltatásmodul

1. Adjon hozzá egy új réteget a térképhez, hogy megjelenítse a keresési eredményeket. Adja hozzá a következő JavaScript-kódot a „script” blokkhoz a térképet inicializáló kód után.

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    ```

2. Az ügyfélszolgáltatás példányosításához adja hozzá a következő JavaScript-kódot a szkriptblokkhoz a térképet inicializáló kód után.

    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. A térkép betöltése után az összes függvénynek be kell töltődnie. Ezt úgy biztosíthatja, ha az összes térképfüggvényt a térkép eseményfigyelő blokkjába helyezi. A következő kódsorok hozzáadásával adjon hozzá a térképhez egy [eseményfigyelőt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener), hogy a térkép teljesen betöltődjön, mielőtt funkciókat ad hozzá.
    
    ```JavaScript
         map.addEventListener("load", function() {
         });
    ```

4. A lekérdezés felépítéséhez adja hozzá a következő szkriptblokkot **a térképbetöltő eseményfigyelőhöz**. A kódrészlet a Search Service alapszintű Fuzzy Search Service (Intelligens keresés) keresési API-ját használja. A Fuzzy Search Service a legtöredékesebb bemeneti adatokat is kezeli, például a címek vagy hasznos helyet (POI-t) jelölő tokenek bármely kombinációját. Rákeres a közeli benzinkutakra egy megadott sugarú körön belül. A választ ezután GeoJSON formátumba szegmentálja, és pontelemekké alakítja, amelyeket azután gombostűkként felvesz a térképre. A szkript utolsó része kamerahatárokat ad a térképhez a Map [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) tulajdonságával.

    ```JavaScript

            // Execute a POI search query then add pins to the map for each result once a response is received
            client.search.getSearchFuzzy("gasoline station", {
            lat: 47.6292,
            lon: -122.2337,
            radius: 100000
            }).then(response => {
       
            // Parse the response into GeoJSON 
            var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);

            // Create the point features that will be added to the map as pins
            var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => {
               var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat];
               return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                name: poiResult.properties.poi.name,
                address: poiResult.properties.address.freeformAddress,
                position: poiPosition[1] + ", " + poiPosition[0]
               });
            });

            // Add pins to the map for each POI
            map.addPins(searchPins, {
               name: searchLayerName,
               cluster: false, 
               icon: "pin-round-darkblue" 
            });

            // Set the camera bounds
            map.setCameraBounds({
               bounds: geojsonResponse.getGeoJsonResults().bbox,
               padding: 50
            );
        });
    ```
5. Mentse a **MapSearch.html** fájlt, és frissítse a böngészőt. Most azt kell látnia, hogy Seattle van a térkép középpontjában, és a benzinkutak helyét kék gombostűk jelölik.

   ![A keresési eredményeket tartalmazó térkép megtekintése](./media/tutorial-search-location/pins-map.png)

6. A térkép által renderelt nyers adatok megtekintéséhez írja be a böngészőbe a következő HTTPRequest kérést. Cserélje le a \<your account key\> értéket az Ön által használt elsődleges kulcsra.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Ezen a ponton a MapSearch lap meg tudja jeleníteni az intelligens keresési lekérdezés által visszaadott hasznos helyek helyét. Adjunk hozzá néhány interaktív képességet, valamint további információkat a helyekről.

## <a name="add-interactive-data"></a>Interaktív adatok hozzáadása

A létrehozott térkép ezen a ponton még csak a szélességi/hosszúsági adatokat vizsgálja meg a keresési eredményekhez. Ha azonban megtekinti a Maps Search szolgáltatás által visszaadott nyers JSON-fájlt, látni fogja, hogy az további információkat tartalmaz az egyes benzinkutakról, például a nevüket és a címüket. Ezeket az adatokat interaktív felugró ablakok használatával beépítheti a térképbe.

1. Adja hozzá a *szkript* blokkhoz a következő sorokat, amelyek felugró ablakokat hoznak létre a Search Service által visszaadott hasznos helyekhez:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    Az **atlas.Popup** API egy információs ablakot biztosít, amely a térkép megfelelő pozíciójához van kötve. Ez a kódrészlet állítja be a felugró ablak tartalmát és pozícióját. Hozzáad egy eseményfigyelőt is a `map` vezérlőhöz, amely arra vár, hogy az _egérmutatót_ a felugró ablak fölé vigyék.

2. Mentse a fájlt, és frissítse a böngészőt. A térkép most felugró információs ablakokat jelenít meg böngészőben, ha az egérmutatót valamelyik gombostű fölé viszi.

    ![Azure Térkép vezérlőelem és Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Maps-fiók létrehozása
> * A fiók elsődleges kulcsának lekérése
> * Új weblap létrehozása a Térkép vezérlőelem API használatával
> * A Search Service használata egy közeli hasznos hely kereséséhez

Az oktatóanyag kódmintáját itt érheti el:

> [Helyek keresése az Azure Maps használatával](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/search.html)

A következő oktatóanyag bemutatja, hogyan lehet megjeleníteni egy útvonalat két hely között.

> [!div class="nextstepaction"]
> [Útvonal egy adott úti célhoz](./tutorial-route-location.md)
