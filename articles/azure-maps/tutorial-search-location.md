---
title: Keresés az Azure Maps használatával | Microsoft Docs
description: Közeli hasznos hely keresése az Azure Maps használatával
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e879e096fb990e4567b43b1938909449820edd42
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412720"
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
   <html>
   <head>
      <title>Map Search</title>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

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

      #map {
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

3. Adja hozzá a következő JavaScript-kódot a HTML-fájl `GetMap` függvényéhez. A **\<Your Azure Maps Key\>** sztringet cserélje le a Maps-fiókból kimásolt elsődleges kulcsra.

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   Ez a szakasz inicializálja az Azure Maps-fiók kulcsához tartozó térképkezelési API-t. Az **atlas** az a névtér, amely az API-t és a hozzá tartozó vizuális összetevőket tartalmazza. Az **atlas.Map** biztosítja a vizuális és interaktív webes térkép vezérlőit. 

4. Mentse a fájl módosításait, és nyissa meg a HTML-oldalt egy böngészőben. Ez a legegyszerűbb térkép, amelyet az **atlas.map** meghívásával és a fiókkulcs használatával létrehozhat.

   ![A térkép megtekintése](./media/tutorial-search-location/basic-map.png)

5. A `GetMap` függvényben adja hozzá az alábbi JavaScript-kódot a térkép inicializálása után. 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

      //Create a popup but leave it closed so we can update it and display it later.
      popup = new atlas.Popup();

      //Add a mouse over event to the result layer and display a popup when this event fires.
      map.events.add('mouseover', resultLayer, showPopup);
   });
   ```

   A rendszer hozzáad a térképhez egy betöltési eseményt, amely a térkép erőforrásainak teljes betöltését követően aktiválódik. A térkép betöltésiesemény-kezelőjében létrejön egy adatforrás az eredményadatok tárolására. Létrejön egy szimbólumréteg, amelyet a rendszer az adatforráshoz csatol. Ez a réteg határozza meg az adatforrásban található eredményadatok megjelenítését. Esetünkben ez egy sötétkék, kerek gombostű ikon, amely az eredmény koordinátái fölött jelenik meg, és amelyet más ikonok átfedhetnek. 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Keresési képességek hozzáadása

Ez a szakasz bemutatja, hogyan használható a Maps Search API egy hasznos hely megkereséséhez a térképen. Ez az API egy fejlesztőknek szánt RESTful API, amely lehetővé teszi a címek, hasznos helyek és egyéb földrajzi adatok keresését. A Search szolgáltatás szélességi és hosszúsági koordinátákat rendel egy adott címhez. Az alább ismertetett **Szolgáltatásmodul** segítségével helyekre kereshet a Maps Search API használatával.

### <a name="service-module"></a>Szolgáltatásmodul

1. A térkép betöltésiesemény-kezelőjében adja hozzá a következő JavaScript-kódot az ügyfélszolgáltatás példányosításához.

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. Ezután adja hozzá a következő szkriptblokkot a keresőlekérdezés felépítéséhez. A kódrészlet a Search Service alapszintű Fuzzy Search Service (Intelligens keresés) keresési API-ját használja. A Fuzzy Search Service a legtöbb nem teljes egyezésű bemeneti adat, például címek, helyek vagy hasznos helyek (POI) kezelésére is képes. Ez a kód a közeli benzinkutakra keres rá egy megadott sugarú körön belül. A választ ezután GeoJSON formátumba szegmentálja és hozzáadja az adatforráshoz, amely következtében az adatok automatikusan megjelennek a térképen a szimbólumrétegen keresztül. A szkript utolsó része a térkép kameranézetét állítja be az eredmények határolókeretével, a térkép [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) tulajdonságával. A kód egy kitöltést is hozzáad, amely a szimbólumikonok képpontméreteit hivatott kompenzálni, mivel a határolókeret számítása a koordináták alapján történik. 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. Mentse a **MapSearch.html** fájlt, és frissítse a böngészőt. Most azt kell látnia, hogy Seattle van a térkép középpontjában, és a benzinkutak helyét kék gombostűk jelölik.

   ![A keresési eredményeket tartalmazó térkép megtekintése](./media/tutorial-search-location/pins-map.png)

4. A térkép által renderelt nyers adatok megtekintéséhez írja be a böngészőbe a következő HTTPRequest kérést. Cserélje le a \<Your Azure Maps Key\> értéket az Ön által használt elsődleges kulcsra.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Ezen a ponton a MapSearch lap meg tudja jeleníteni az intelligens keresési lekérdezés által visszaadott hasznos helyek helyét. Adjunk hozzá néhány interaktív képességet, valamint további információkat a helyekről.

## <a name="add-interactive-data"></a>Interaktív adatok hozzáadása

A létrehozott térkép ezen a ponton még csak a keresési eredmények hosszúsági/szélességi adatait vizsgálja. Ha azonban megtekinti a Maps Search szolgáltatás által visszaadott nyers JSON-fájlt, látni fogja, hogy az további információkat tartalmaz az egyes benzinkutakról, például a nevüket és a címüket. Ezeket az adatokat interaktív felugró ablakokkal beépítheti a térképbe.

1. Adja hozzá a következő kódsort a térkép betöltésiesemény-kezelőjében a fuzzy keresési szolgáltatás lekérdezéséhez. Ez létrehozza a felugró ablak egy példányát, és hozzáad egy rámutatási eseményt a szimbólumréteghez.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    Az **atlas.Popup** API egy információs ablakot biztosít, amely a térkép megfelelő pozíciójához van kötve. 
      
2. A *script* címkében, a `GetMap` függvény után adja hozzá az alábbi kódot, amely megjeleníti a rámutatott eredmény adatait a felugró ablakban. 

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

> [Helyek keresése az Azure Maps használatával](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[A minta megtekintése élőben](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

A következő oktatóanyag bemutatja, hogyan lehet megjeleníteni egy útvonalat két hely között.

> [!div class="nextstepaction"]
> [Útvonal egy adott úti célhoz](./tutorial-route-location.md)
