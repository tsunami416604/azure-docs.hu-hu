---
title: Az Azure Maps store lokátorok létrehozásához |} A Microsoft Docs
description: Egy tároló-lokátor létrehozása az Azure Maps használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e43c607c2dc67054bde7689d50e495a59e6b659
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540856"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Egy tároló-lokátor létrehozása az Azure Maps használatával

Ez az oktatóanyag végigvezeti egy egyszerű store kereső az Azure Maps használatával történő létrehozásának folyamatán. Store lokátorok gyakoriak. Az ilyen típusú alkalmazás által használt fogalmak számos sok más típusú alkalmazások alkalmazható. A legtöbb vállalatok körében, amelyek közvetlenül a fogyasztók értékesíthetik kell egy tároló-lokátor kínál az ügyfeleknek. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
    
> [!div class="checklist"]
> * Egy új weblap létrehozása az Azure térkép vezérlőelem API használatával.
> * Egyéni adatok betöltése egy fájlból, és megjeleníti azt a térképen.
> * Az Azure Maps Search szolgáltatás használata egy címet, vagy adjon meg egy lekérdezést.
> * A felhasználói helyadatok lekérése a böngészőből, és jelenítse meg azt a térképen.
> * Egyesíthet több réteget hozhat létre egyéni szimbólumok a térképen.  
> * A fürt adatpontokat.  
> * A térkép nagyítási vezérlőket.

<a id="Intro"></a>

Ugorhat a [élő store kereső példa](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) vagy [forráskódját](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a lépések elvégzéséhez szüksége lesz a [létrehozása az Azure Maps-fiók](./tutorial-search-location.md#createaccount) és [lekérése a fiókja előfizetési kulcsát](./tutorial-search-location.md#getkey).

## <a name="design"></a>Tervezés

Mielőtt megkezdi a munkát a kódba, célszerű olyan kialakítás megkezdéséhez. A tároló kereső lehet egyszerű vagy összetett, célszerű lehet. Ebben az oktatóanyagban létrehozunk egy egyszerű store lokátort. Néhány tipp a vizualizáción érdekében bizonyos funkciók kiterjesztheti, ha úgy dönt, hogy tartalmazza. Egy tároló kereső Contoso kávé nevű kitalált vállalat hozunk létre. A következő ábrán látható egy Drótvázdiagram készítünk ebben az oktatóanyagban a tároló-lokátor általános elrendezésének:

<br/>
<center>

![A Contoso kávé kávézóban helyek egy tároló kereső Drótvázdiagram](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Ezt a keresőt store hasznosságát maximalizálása reszponzív elrendezést alakítottunk ki, amely beállítja, ha a felhasználó képernyő szélessége kisebb, mint 700 képpont széles tartalmazza. Reszponzív elrendezést alakítottunk ki megkönnyíti az áruház-kereső kis képernyőn, például használja a mobileszközökön. Itt látható egy kis-elrendezés az egy Drótvázdiagram:  

<br/>
<center>

![A Contoso kávé Drótvázdiagram kereső mobil eszközön tárolja.](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

A wireframes viszonylag egyszerű alkalmazás megjelenítése. Az alkalmazás egy keresőmezőt, a közeli tárolók listáját, a látható, hogy van néhány jelölők (szimbólum) és a egy előugró ablak, amely további információkat jelenít meg, amikor a felhasználó kiválaszt egy jelölő rendelkezik. Részletesebben Íme az ebben az oktatóanyagban be ezt a keresőt tároló beépített funkciók:

* Az importált tabulátorral tagolt adatfájlból minden hely töltődnek be a térképen.
* A felhasználó Pásztázás és nagyíthatja a térképet, hajtsa végre a keresést, és válassza ki a saját helyét GPS gombot.
* Az oldal elrendezése az eszköz képernyőjén szélessége alapján állítja be.  
* Egy fejléc a store emblémája jeleníti meg.  
* A felhasználó használhatja egy keresőmező és a Keresés gombra, keressen egy helyen, például egy cím, irányítószám vagy város. 
* A `keypress` esemény ablakhoz történő a keresőmezőbe egy keresési aktivál, ha a felhasználó lenyomja az ENTER billentyűt. Ez a funkció gyakran van kihagyott, de jobb felhasználói élményt hoz létre.
* Ha a térkép helyezi, és az egyes helyekre középpontja a térkép közötti távolságot számítható ki. Az eredmények listájában a legközelebbi hely megjelenítése a térkép tetején frissül.  
* Az eredmények listájából válasszon ki egy eredményt, ha a térkép a kiválasztott helyen keresztül közepén, és helyének adatait az előugró ablakban jelenik meg.  
* Egy adott helyen a térképen kiválasztásával is aktivál az előugró ablak.
* Amikor a felhasználó lekicsinyíti, a fürtök helyek vannak csoportosítva. Fürtök a körben több kör jelöli. Fürtök formájában, és külön, a felhasználó megváltoztatja a nagyítási szint.
* Fürt kiválasztása a térkép két szinten nagyítása és középpontba keresztül a fürt helye állítása.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>A tároló helye adatkészlet létrehozása

Egy kereső áruházbeli alkalmazás kifejlesztett, mielőtt kell a tárolók a térképen megjelenítendő szeretnénk az adatkészlet létrehozása. Ebben az oktatóanyagban egy adatkészletet egy kitalált, Contoso kávé nevű kávézóban használunk. Ez egyszerű store keresőként az adatkészlet egy Excel-munkafüzet kezelik. Az adatkészlet 10,213 kilenc országok elosztva Contoso kávé kávézóban helyeket tartalmaz: az Egyesült Államokban, Kanadában, az Egyesült Királyság, Franciaország, Németország, Olaszország, Hollandia, Dánia, és Spanyolország. A következő képernyőkép: az adatok a következőhöz hasonló:

<br/>
<center>

![A kereső adatokat egy Excel-munkafüzet képernyőképe](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Is [az Excel-munkafüzet letöltése](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Az adatok képernyőkép megnézzük, jobban láthatóvá tesszük a következő megfigyeléseken:
    
* Helyadatok használatával tárolja a **addressLine elemhez**, **Város**, **település** (megye), **AdminDivision** (állam/megye) , **Postai irányítószám** (irányítószám), és **ország** oszlopokat.  
* A **szélességi** és **hosszúsági** oszlopok a Contoso kávé kávézóban helyek koordinátákat tartalmaznak. Ha koordináták információ nem rendelkezik, használhatja a keresési szolgáltatások az Azure Maps-meghatározni a hely koordinátái.
* Néhány további oszlopokat a kávézóban – üzemelő hálózathoz kapcsolódó metaadatokat tartalmaz: egy telefonszám, Wi-Fi elérési pontokról készített és tolószéket kisegítő lehetőségek, valamint a tároló megnyitása és bezárása 24 órás formátumban többször logikai típusú oszlopokat. A saját számára a helyadatok fontosabb metaadatokat tartalmazó oszlopokat hozhat létre.

> [!Note]
> Az Azure Maps rendereli a gömbös Mercator vetületet "EPSG:3857" adatokat, de olvassa be az adatokat a "EPSG:4325", amely a WGS84 datum használja. 

Számos módon tegye elérhetővé az alkalmazáshoz az adatkészletet. Egy megoldás, az adatok betöltéséhez az adatbázisba, és elérhetővé tehettük egy webszolgáltatás, amelyet le az adatokat, és elküldi az eredményeket a felhasználó böngészőjében. Ez a beállítás ideális a nagy adatkészletek vagy adatkészleteket, amelyek gyakran frissülnek. Ez a beállítás azonban jóval több fejlesztési munkát igényel, és magasabb költsége. 

Egy másik megoldás, ez az adatkészlet alakítható át egy egyszerű szöveges fájl, amelyet a könnyen elemezhetők a böngészőben. Magát a fájlt az alkalmazás többi lehet üzemeltetni. Ez a beállítás áramvonalasíthatók, de csak kisebb adatkészletek esetében jó választás, mert a felhasználó letölti az összes adatot. Használjuk a sima szöveges fájlt ehhez az adatkészlethez, mert az adatok mérete 1 MB-nál kisebb.  

A munkafüzet egy egyszerű szöveges fájl konvertálásához a munkafüzet mentéséhez tabulátorral tagolt-fájlként. Minden oszlop egy tabulátor karakter, ami lehetővé teszi az oszlopok elemezhetők a kódban van elválasztva. Vesszővel tagolt (CSV) formátum használhat, de ez a beállítás szükséges további elemzési logikai. Bármely mező, amely rendelkezik egy vesszővel tagolt körülötte lenne kell csomagolni, tegye idézőjelek közé. Exportálhatja az adatokat egy tabulátorokkal tagolt fájlt az Excelben, válassza ki a **Mentés másként**. Az a **Fájltípus** legördülő listában válassza **szöveg (delimited)(*.txt) lapon**. A fájl neve *ContosoCoffee.txt*. 

<br/>
<center>

![A Mentés másként párbeszédpanel képernyőképe](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Ha a szöveges fájlt a Jegyzettömbben nyílik meg, akkor hasonlít az alábbi ábra:

<br/>
<center>

![Egy Jegyzettömb-fájlt egy tabulátorokkal tagolt adatkészletet megjelenítő képernyőkép](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>A projekt beállítása

A projekt létrehozásához használhatja [Visual Studio](https://visualstudio.microsoft.com) vagy tetszőleges Kódszerkesztő. Hozzon létre három fájlt a projektmappa fájllistájának: *index.html*, *index.css*, és *index.js*. Ezek a fájlok határozza meg, az elrendezést, stílusának és az alkalmazás logikáját. Hozza létre a *adatok* , és adja hozzá *ContosoCoffee.txt* azt a mappát. Hozzon létre egy másik mappát nevű *lemezképek*. Tíz képek az alkalmazásban, ikonok, gombok és jelölőket küldhet a térképen a használjuk. Is [töltse le a lemezképek](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Az alábbi ábra a projektmappa fájllistájának kell hasonlítania:

<br/>
<center>

![Az egyszerű Store kereső projektmappa képernyőképe](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>A felhasználói felület létrehozása

A felhasználói felület létrehozásához adja hozzá a kódot *index.html*:

1. Adja hozzá a következő `meta` -címkék a `head` , *index.html*. A címkék megadása a karakterkészletet (UTF-8), ossza meg az Internet Explorer és a Microsoft Edge böngésző legújabb használandó, és adja meg a nézőpont, amely jól működik a rugalmas elrendezés.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Adja hozzá hivatkozásokat az Azure-térképek webes vezérlőinek JavaScript és CSS-fájlokat:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

1. Adjon hozzá egy hivatkozást az Azure Maps Services modul. A modul az egy JavaScript-függvénytárat, amely becsomagolja az Azure Maps REST szolgáltatások, és lehetővé teszi a JavaScript gyerekjáték. A modul hasznos motorja olyan keresési funkciókat.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```

1. Adja hozzá hivatkozásokat *index.js* és *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. A dokumentum törzsében, adjon hozzá egy `header` címke. Belül a `header` címkével, adja hozzá az embléma és a vállalat nevét.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Adjon hozzá egy `main` címkét, és hozzon létre egy keresés panelen text box és a keresési gombot. Adja hozzá `div` a térképen, a lista panel és a saját helyét GPS gombra mutató hivatkozásokat.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Ha elkészült, *index.html* hasonlóan kell kinéznie [ebben a példában index.html fájlt](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

A következő lépés, hogy a CSS-stílust határozza meg. CSS-stílust határozza meg, hogyan vannak leírva az alkalmazás-összetevők és az alkalmazás megjelenése. Nyissa meg *index.css* , és adja hozzá a következő kód azt. A `@media` stílus stílust beállításokat használja, ha a képernyő szélessége 700 képpontnál kisebb határozza meg.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Ha futtatja az alkalmazást, láthatja a fejlécet, a keresőmezőbe, és a Keresés gombra, de a leképezés nem látható, mert azt még nem lett betöltve. Keresés próbál, nem történik semmi. Állítsa be a JavaScript-logika a funkciókat az áruház-kereső eléréséhez a következő szakaszban leírt kell.

## <a name="wire-the-application-with-javascript"></a>Az alkalmazás JavaScript átviteli

Ezen a ponton minden van beállítva a felhasználói felületen. Most hogy kell adja hozzá a JavaScript betölteni és elemezni az adatokat, és a térképen adatok jelennek majd meg. Első lépésként nyissa meg a *index.js* és adja hozzá a kódot, a következő lépésben ismertetett módon.

1. Adja hozzá a globális beállítások megkönnyítik a beállítások frissítése. A térkép, egy előugró ablak, egy adatforrást, egy ikon réteget, egy HTML-jelölő, amely megjeleníti az egy keresőmezőt, és a egy példányát az Azure Maps keresési szolgáltatás ügyfél-központ változót is, megadni.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Adja hozzá kódot *index.js*. A következő kódot a térkép inicializálja, hozzáad egy [eseményfigyelő](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , amely megvárja, amíg befejeződik az oldal betöltése, másolatot események figyelése a Betöltés a térkép kábeleket, és használja a Keresés és a saját helyét gomb.

   Amikor a felhasználó kiválasztja a Keresés gombra, vagy ha a felhasználó lenyomja a billentyűt után kifejezést a keresőmezőbe írja be egy olyan helyre, szemben a felhasználó lekérdezése egy intelligens keresés kezdeményezik. Ország ISO 2 értékeket tömbjét adja át a `countrySet` beállítással korlátozhatja a keresési eredmények között, az adott országban. Korlátozza az országok keresés segítségével növelheti a visszaadott eredmények pontosságát. 
  
   Ha a keresés befejeződött, az első eredmény igénybe vehet, és állítsa be a térkép kamera a terület fölé. Amikor a felhasználó a saját helyét gombot választja, a HTML5-alapú földrajzi hely meghatározásának API, amely a böngészőbe, és a felhasználó földrajzi helye a térkép középre helyükre keresztül használja.  

   > [!Tip]
   > Előugró ablakok használatakor érdemes létrehozni egy `Popup` példány, és újra felhasználhatja a példány annak tartalmát és pozícióját frissítésével. A minden `Popup`példányt ad hozzá a kódot, több DOM-elemek hozzáadásakor a lapot. A további DOM van elemei lapon nyomon követheti, hogy rendelkezik a böngésző dolgot. Ha túl sok elem, a böngésző lassú válhat.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 }, // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

        //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. A térkép `load` eseményfigyelő, adja hozzá a nagyítási vezérlő és a egy HTML jelölő egy keresési terület megjelenítéséhez.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. A térkép `load` eseményfigyelő, adjon hozzá egy adatforrást. Ezt követően hívás betölteni és elemezni az adatkészletet. Az adatforrás fürtözésének engedélyezésére. A fürtszolgáltatás az adatokat a forrás csoportok egymással átfedésben lévő pontok együtt egy fürtben. A fürtök külön be felhasználóként az egyes pontok közeledik. Ez lehetővé teszi egy több képlékeny felhasználói felülettel, és növeli a teljesítményt.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Miután betöltöttük az adatkészlet, a térkép `load` eseményfigyelő, meghatározhatja egy adott szinten is adatok jelennek meg. Egy buborék réteg fürtözött adatpontok előállítására szolgál. A szimbólum réteg jelennek meg az egyes csoportokban a buborék réteg felett pontok száma szolgál. Szimbólum beállítása egy egyéni ikon az egyes helyeken a térképen jeleníti meg.

   Adjon hozzá `mouseover` és `mouseout` eseményeket a buborék és ikon rétegek módosítása az egérmutatót, amikor a felhasználó rámutat egy fürt vagy a térképen ikonra. Adjon hozzá egy `click` eseményhez, és a fürt buborék réteget. Ez `click` a térkép alatt két szinttel nagyítása és középpontba a térkép keresztül a fürt, amikor a felhasználó kiválasztja az összes fürt állítása esemény. Adjon hozzá egy `click` esemény a ikon réteghez. Ez `click` esemény megjelenik egy előugró ablak, amely egy kávézóban részleteit jeleníti meg, ha a felhasználó kiválaszt egy adott helyhez ikont. Vegyen fel egy eseményt a térképen, a figyelő észleli, ha a térkép áthelyezése befejeződött. Ha ez az esemény akkor következik be, frissítse az elemeket a lista panelen.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: '{point_count_abbreviated}',
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Ha a kávézóban adathalmaz betöltődik, azt kell először letölti. Ezt követően a szövegfájl sorokat kell osztani. Az első sor a fejléc-információkat tartalmazza. Ahhoz, hogy a kód könnyebb követhetősége érdekében, hogy a fejléc a be olyan objektum, amely majd használatával minden egyes tulajdonság cella indexét keressük kell elemezni. Az első sor után a fennmaradó sorok hurkot, és hozzon létre egy pont funkció. A pont funkció hozzáadása az adatforráshoz. Végül frissítse a listát panelen.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Ha a lista panel frissül, a térkép a központ az aktuális nézetben térképen minden pont szolgáltatás közötti távolságot számítása. A szolgáltatások majd távolság szerint vannak rendezve. HTML jön létre minden egyes helyet megjelenítéséhez a listában panelen.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Get all the shapes that have been rendered in the bubble layer.
        var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

        data.forEach(function(shape) {
            if (shape instanceof atlas.Shape) {
                //Calculate the distance from the center of the map to each shape, and then store the data in a distance property.  
                shape.distance = atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles');
            }
        });

        //Sort the data by distance.
        data.sort(function(x, y) {
            return x.distance - y.distance;
        });

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Route the distance to two decimal places.  
                (Math.round(shape.distance * 100) / 100),
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Amikor a felhasználó kiválaszt egy elemet a listában panelen, a rendszer beolvassa az alakzatot, amelyhez az elem kapcsolódik az adatforrás. Egy előugró ablak az alakzat tárolt adatok a tulajdonság alapján jön létre. A térkép az alakzat fölé közepén. Ha a térkép kisebb, mint 700 képpont széles, a térképnézet eltolás van, így az előugró ablakban látható.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Route the distance to two decimal places.  
            '<br/>', (Math.round(shape.distance * 100) / 100),
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>')
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>')
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Most már rendelkezik egy teljesen működőképes store lokátort. Egy webböngészőben nyissa meg a *index.html* az áruház-kereső fájlt. Amikor a fürtök jelenik meg a térképen, kereshet egy adott helyre vonatkozóan a keresőmező használatával, a saját helyét gomb kiválasztásával, a fürt kiválasztásával vagy kinagyítva egyes helyek megtekintéséhez a térképen.

Először egy felhasználó a saját helyét gombot választja a böngészőben, amely kéri a felhasználó földrajzi helye hozzáférési engedélyt a biztonsági figyelmeztetést jelenít meg. Ha a felhasználó elfogadja a megosztását a saját helyét, a térkép ráközelít az a felhasználó földrajzi helye, és a közeli kávézókban látható. 

<br/>
<center>

![Képernyőkép a böngészőben a felhasználó a felhasználó földrajzi helye hozzáférési kérelem](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Ha ráközelít közel kávézóban helyeket tartalmazó területen, a fürtök szét az egyes helyeken. Válasszon ki egy, az ikonok a térképen, vagy jelöljön ki egy elemet a oldalpanel egy előugró ablak, amely megjeleníti az adott hely információk megtekintéséhez.

<br/>
<center>

![A befejezett store lokátor képernyőképe](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Méretezze át a böngészőablakot, és kevesebb mint 700 képpont széles vagy a mobileszközökön az alkalmazás megnyitásakor, a kedvezőbb elrendezéssel járó módosítások olyan kisebb képernyőkön. 

<br/>
<center>

![Képernyőkép a kis képernyőn verzióját a tároló-lokátor](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerheti, hogyan alapszintű store lokátorok létrehozásához az Azure Maps használatával. Az ebben az oktatóanyagban létrehozott tároló kereső előfordulhat, hogy a szükséges funkciókat. A tároló kereső szolgáltatások hozzáadása, vagy további előzetes funkciók használata több egyéni felhasználói élményt: 

> [!div class="checklist"]
> * Engedélyezése [javaslatokat a beírás](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) kifejezést a keresőmezőbe.  
> * Adjon hozzá [több nyelv támogatása](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Engedélyezi a felhasználó számára [útvonalon helyek szűrése](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Adja hozzá a teszi [beállítása szűrőket](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Adja hozzá a támogatási egy lekérdezési karakterláncot adjon meg egy kezdeti keresett érték. Ha ezt a beállítást a tároló lokátort a, felhasználók könyvjelző formájában elmentheti és megoszthatja a kereséseket. Azt is, hogy ezen a lapon egy másik lapon keresések át egyszerű módszert kínál.  
> * A tároló lokátort, üzembe helyezése egy [Azure App Service Web Appsban](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Store az adatok egy adatbázisban, és keresse meg a közeli helyeket. További tudnivalókért tekintse meg a [SQL Server térbeli adattípusok áttekintése](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) és [térbeli adatok lekérdezése a legközelebbi szomszéd](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

Az oktatóanyag kódmintáját itt érheti el:

> [Egy tároló-lokátor létrehozása az Azure Maps használatával](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

[A minta megtekintése élőben](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

További információ az Azure Maps lefedettségéről és képességeiről:

> [!div class="nextstepaction"]
> [Nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md)

További példakódok és az interaktív kódolási felület bemutatása:

> [!div class="nextstepaction"]
> [A térkép vezérlőelem használata](how-to-use-map-control.md)
