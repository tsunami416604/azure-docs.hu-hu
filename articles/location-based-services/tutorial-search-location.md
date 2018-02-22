---
title: "Keresés az Azure Location Based Services használatával | Microsoft Docs"
description: "Közeli hasznos helyek keresése az Azure Location Based Services használatával"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 791992028d11633fc20f55ae1a34e7fcd442bf3a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="search-nearby-points-of-interest-using-azure-location-based-services"></a>Közeli hasznos helyek keresése az Azure Location Based Services használatával

Ez az oktatóanyag azt mutatja be, hogyan állatható be egy fiók az Azure Location Based Services használatához, majd hogyan használhatók a megadott API-k egy hasznos hely kereséséhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Location Based Services szolgáltatást használó fiók létrehozása
> * Az Azure Location Based Services-fiók elsődleges kulcsának lekérése
> * Új weblap létrehozása a Térkép vezérlőelem API használatával
> * A Search Service használata egy közeli hasznos hely kereséséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Azure Location Based Services szolgáltatást használó fiók létrehozása

Kövesse az alábbi lépéseket egy új Location Based Services-fiók létrehozásához:

1. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A *Keresés a piactéren* mezőbe írja be: **location based services**.
3. Az *Eredmények* közül válassza az **Azure Location Based Services (előzetes verzió)** elemet. Kattintson a térkép alatt megjelenő **Létrehozás** gombra. 
4. A **Location Based Services-fiók létrehozása** lapon adja meg a következő értékeket:
    - Az új fiók *neve*. 
    - A fiókhoz használni kívánt *előfizetés*.
    - A fiókhoz tartozó *erőforráscsoport* neve. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    - Válassza ki az *erőforráscsoport helyét*.
    - Olvassa el az *Előzetes verziójú szolgáltatás feltételeit* és jelölje be az azok elfogadását jelző jelölőnégyzetet. 
    - Végül kattintson a **Létrehozás** gombra.
   
    ![Location Based Services-fiók létrehozása a portálon](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>A fiók elsődleges kulcsának lekérése

A Location Based Services-fiók sikeres létrehozása után kövesse az alábbi lépéseket, amelyekkel hozzákapcsolhatja a fiókot a térképes kereső API-khoz.

1. Nyissa meg a Location Based Services-fiókját a portálon.
2. Lépjen a fiók **BEÁLLÍTÁSOK** lapjára, majd válassza a **Kulcsok** elemet.
3. Másolja ki az **elsődleges kulcsot** a vágólapra. Mentse helyileg a későbbi lépésekben való felhasználáshoz. 

    ![Elsődleges kulcs lekérése a portálon](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Új weblap létrehozása az Azure Térkép vezérlőelem API használatával
Az Azure Térkép vezérlőelem API egy kényelmes ügyféloldali kódtár, amely segítségével az Azure Location Based Services könnyedén integrálható a webalkalmazásokba. A kódtár elrejti a REST-szolgáltatás puszta hívásainak összetettségét, a stílusos és testre szabható összetevők segítségével pedig növeli a munkavégzés hatékonyságát. A következő lépések bemutatják, hogyan hozhat létre egy statikus HTML-oldalt, amelybe be van ágyazva a Location Based Services Térkép vezérlőelem API-ja. 

1. A helyi gépén hozzon létre egy új fájlt **MapSearch.html** néven. 
2. Adja a következő HTML-összetevőket a fájlhoz:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>

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
    Figyelje meg, hogy a HTML-fejléc tartalmazza az Azure Térkép vezérlőelem-kódtárban található CSS- és JavaScript-erőforrásfájlokat. Figyelje meg a HTML-fájl *törzséhez* adott *szkript* szakaszt is. Ez a szakasz tartalmazza az Azure Térkép vezérlőelem API elérésére szolgáló beágyazott JavaScript-kódot.
 
3.  Adja hozzá a következő JavaScript-kódot a HTML-fájl *szkript* blokkjához. Használja a Location Based Services-fiók elsődleges kulcsát a szkriptben. 

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Ez a szakasz indítja el az Azure Location Based Services-fiók kulcsához tartozó Térkép vezérlőelem API-t. Az **Atlas** az a névtér, amely az Azure Térkép vezérlőelem API-t és a hozzá tartozó vizuális összetevőket tartalmazza. Az **atlas.Map** biztosítja a vizuális és interaktív webes térkép vezérlőit. A térkép megtekintéséhez nyissa meg a HTML-oldalt a böngészőben. 

4. Adja hozzá a *szkript* blokkhoz a következő JavaScript-kódot, amely hozzáad egy gombostű réteget a Térkép vezérlőelemhez:

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

5. Mentse a fájlt a gépén. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>A Search Service használata egy közeli hasznos hely kereséséhez

Ez a szakasz bemutatja, hogyan használható az Azure Location Based Services Search Service API-ja egy hasznos hely megkereséséhez a térképen. Ez az API egy fejlesztőknek szánt RESTful API, amely lehetővé teszi a címek, hasznos helyek és egyéb földrajzi adatok keresését. A Search Service szélességi és hosszúsági koordinátákat rendel egy adott címhez. 

1. Nyissa meg az előző szakaszban létrehozott **MapSearch.html** fájlt, és adja hozzá a következő JavaScript-kódot a *szkript* blokkhoz a Search Service ábrázolásához. 
    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```
    Ez a kódrészlet létrehoz egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) kérést, és hozzáad egy eseménykezelőt a bejövő válasz elemzéséhez. A sikeres válaszhoz összegyűjti az egyes visszaadott helyek címét, nevét, valamint a szélességi és hosszúsági koordinátáit a `searchPins` változóban. Végül hozzáadja ezt a helypont-gyűjteményt a `map` vezérlőhöz gombostűk formájában. 

2. Adja hozzá a *szkript* blokkhoz a következő kódot, amely elküldi az XMLHttpRequest kérést az Azure Location Based Services Search Service szolgáltatása felé:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "&api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Ez a kódrészlet a Search Service alapszintű kereső API-ját, az **intelligens keresést** használja. Ez a legtöredékesebb bemeneti adatokból is képes címeket vagy *POI* tokeneket kinyerni. Rákeres a legközelebbi **benzinkútra**, valamint a megadott címre a szélességi és hosszúsági koordináta szerint, illetve keresést végez a megadott sugarú körön belül. A fiók a példafájlban korábban megadott elsődleges kulcsát használja a Location Based Services meghívásához. Eredményként a megtalált helyekhez tartozó szélességi/hosszúsági koordinátapárokat adja vissza. A gombostűk megtekintéséhez nyissa meg a HTML-oldalt a böngészőben. 

3. Adja hozzá a *szkript* blokkhoz a következő sorokat, amelyek felugró ablakokat hoznak létre a Search Service által visszaadott hasznos helyekhez:

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
    Az **atlas.Popup** API egy információs ablakot biztosít, amely a térkép megfelelő pozíciójához van kötve. Ez a kódrészlet állítja be a felugró ablak tartalmát és pozícióját, valamint hozzáad egy eseményfigyelőt a `map` vezérlőhöz, amely arra vár, hogy az _egérmutatót_ a felugró ablak fölé vigyék. 

4. Mentse a fájlt, majd nyissa meg a **MapSearch.html** fájlt egy tetszőleges webböngészőben, és tekintse meg az eredményt. A térkép felugró információs ablakokat jelenít meg böngészőben, ha az egérmutatót valamelyik megjelenített gombostű fölé viszi, az alább láthatóhoz hasonló módon. 

    ![Azure Térkép vezérlőelem és Search Service](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Location Based Services szolgáltatást használó fiók létrehozása
> * A fiók elsődleges kulcsának lekérése
> * Új weblap létrehozása a Térkép vezérlőelem API használatával
> * A Search Service használata egy közeli hasznos hely kereséséhez

Folytassa az [Útvonal egy hasznos helyhez az Azure Location Based Services használatával](./tutorial-route-location.md) című oktatóanyaggal, amelyből megtudhatja, hogyan használhatja az Azure Location Based Services szolgáltatást a hasznos helyekhez vezető útvonalak megkeresésére. 
