---
title: 'Oktatóanyag: Üzletkereső alkalmazás létrehozása az Azure Maps használatával | Microsoft Azure Maps'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre üzletkereső webalkalmazást a Microsoft Azure Maps webes SDK használatával.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 49b57b213a452d6c594bbc1ca537e68bd7a83864
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333844"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Oktatóanyag: Üzletkereső létrehozása az Azure Maps használatával

Ez az oktatóanyag végigvezeti az Azure Maps használatával egy egyszerű tárolókereső létrehozásának folyamatán. Az üzletkeresők gyakoriak. Az ilyen típusú alkalmazásokban használt fogalmak közül sok más típusú alkalmazásra is alkalmazható. Felajánlás egy készlet lokátor -hoz vásárlók van egy must részére a leg--bb teendő amit elad közvetlenül -hoz fogyasztó. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
    
> [!div class="checklist"]
> * Hozzon létre egy új weblapot az Azure Map Control API használatával.
> * Egyéni adatok betöltése egy fájlból, és megjelenítve azokkal a térképen.
> * Az Azure Maps Search szolgáltatás segítségével keressen egy címet, vagy adjon meg egy lekérdezést.
> * Szerezd meg a felhasználó helyét a böngészőből, és mutasd meg a térképen.
> * Több réteg kombinálásával egyéni szimbólumokat hozhat létre a térképen.  
> * Fürtadatpontok.  
> * Nagyítási vezérlők hozzáadása a térképhez.

<a id="Intro"></a>

Ugorjon előre az [élő áruház keresőpéldájára](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) vagy [forráskódjára.](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator) 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépései végrehajtásához először létre kell hoznia egy Azure Maps-fiókot, és be kell szereznie az elsődleges kulcsot (előfizetési kulcs). Kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) című útmutató utasításait az Azure Maps-fiókelőfizetés S1-es díjcsomaggal történő létrehozásához, és kövesse az elsődleges kulcs [beszerezése](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseit a fiók elsődleges kulcsának lekérnie. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](how-to-manage-authentication.md)című témakörben talál.

## <a name="design"></a>Tervezés

Mielőtt beleugrik a kódot, ez egy jó ötlet, hogy kezdődik a design. Az üzletkereső lehet olyan egyszerű vagy összetett, amennyire csak szeretné. Ebben az oktatóanyagban egy egyszerű tárolókeresőt hozunk létre. Néhány tippet is tartalmazunk az út mentén, hogy segítsen kiterjeszteni néhány funkciót, ha úgy dönt. Létrehozunk egy raktárkeresőt egy kitalált cégnek, a Contoso Coffee-nak. Az alábbi ábra egy drótvázat mutat be az ebben az oktatóanyagban építünk az üzletkereső általános elrendezéséről:

<center>

![A Contoso Kávézó telephelyein található üzletkereső alkalmazás drótváza](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Az üzletkereső hasznosságának maximalizálása érdekében olyan reszponzív elrendezést is tartalmazunk, amely beállítja, ha a felhasználó képernyőszélessége 700 képpontnál kisebb. Az adaptív elrendezés megkönnyíti az üzletkereső használatát egy kis képernyőn, például egy mobileszközön. Itt egy kis képernyős elrendezés drótváza:  

<center>

![A Contoso Coffee store lokátor alkalmazás drótváza mobileszközön](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

A drótvázak meglehetősen egyszerű alkalmazást mutatnak. Az alkalmazás egy keresőmező, egy listát a közeli üzletek, és egy térképet, amely néhány markerek, mint például a szimbólumok. És van egy előugró ablaka, amely további információkat jelenít meg, amikor a felhasználó kijelöl egy jelölőt. Részletesebben, itt vannak a funkciók építünk ebbe a boltban lokátor ebben a bemutató:

* Az importált tabulátorral tagolt adatfájl összes helye betöltődik a térképre.
* A felhasználó pásztázhatja és nagyíthatja a térképet, keresést hajthat végre, és kiválaszthatja a Saját hely GPS gombot.
* A lapelrendezés az eszköz képernyőjének szélessége alapján módosul.  
* A fejlécen az üzlet emblémája látható.  
* A felhasználó keresőmezővel és keresőgombbal kereshet egy helyet, például címet, irányítószámot vagy várost. 
* A `keypress` keresőmezőhöz hozzáadott esemény akkor indítja el a keresést, ha a felhasználó megnyomja az Enter billentyűt. Ezt a funkciót gyakran figyelmen kívül hagyják, de jobb felhasználói élményt nyújt.
* Amikor a térkép mozog, kiszámítja az egyes helyeken a térkép középpontjától számított távolságot. Az eredménylista frissül, hogy a térkép tetején a legközelebbi helyek jelenjenek meg.  
* Amikor kiválaszt egy eredményt az eredménylistában, a térkép középre kerül a kijelölt hely felett, és a helyre vonatkozó információk egy előugró ablakban jelennek meg.  
* Ha kiválaszt egy adott helyet a térképen, az előugró ablakot is kiváltja.
* Amikor a felhasználó kicsinyíti, a helyek fürtökbe vannak csoportosítva. A fürtöket egy kör képviseli, amelynek egy száma van a körön belül. A fürtök a nagyítási szint módosításával és szétválasztásakor elkülönülnek egymástól.
* A fürt kiválasztása két szinttel közelít a térképen, és a fürt helye fölé kerül.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Az üzlethely-adatkészlet létrehozása

Mielőtt egy boltkereső alkalmazást fejlesztenénk, létre kell hoznunk egy adatkészletet az üzletekből, amelyeket meg szeretnénk jeleníteni a térképen. Ebben az oktatóanyagban egy adatkészletet használunk egy Contoso Coffee nevű fiktív kávézóhoz. Az egyszerű tárolókereső adatkészletét egy Excel-munkafüzet kezeli. Az adatkészlet 10 213 Contoso Coffee kávézót tartalmaz kilenc országban/régióban: az Egyesült Államokban, Kanadában, az Egyesült Királyságban, Franciaországban, Németországban, Olaszországban, Hollandiában, Dániában és Spanyolországban. Íme egy képernyőkép arról, hogy hogyan néznek ki az adatok:

<center>

![Képernyőkép az Excel-munkafüzet tárolókereső adatairól](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

[Letöltheti az Excel-munkafüzetet](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Az adatok képernyőképét tekintve a következő észrevételeket tehetjük:
    
* A helyadatoktárolása az **AddressLine**, **Város**, **Önkormányzat** (megye), **Az AdminDivision** (állam/megye), **a Irányítószám** (irányítószám) és az **Ország** oszlop ban tárolódik.  
* A **Szélesség** i és **hosszúsági** oszlopok tartalmazzák az egyes Contoso Coffee kávézóhelyek koordinátáit. Ha nem rendelkezik koordináta-adatokkal, használhatja a keresési szolgáltatások az Azure Mapsben a helykoordináták meghatározásához.
* Néhány további oszlop a kávézókhoz kapcsolódó metaadatokat tartalmaz: telefonszámot, logikai oszlopokat, valamint a nyitvatartási és zárási időket 24 órás formátumban tárolja. A logikai oszlopok a Wi-Fi és a kerekesszékek akadálymentesítéséhez tartoznak. Létrehozhat saját oszlopokat, amelyek a helyadatok szempontjából relevánsabb metaadatokat tartalmaznak.

> [!Note]
> Az Azure Maps az "EPSG:3857" gömb mercator-vetületben jeleníti meg az adatokat, de a WGS84-es adatforgalmat használó "EPSG:4325" formában olvassa be az adatokat. 

Az adatkészletet számos módon elérhetővé teheti az alkalmazás számára. Az egyik megközelítés az adatok betöltése egy adatbázisba, és elérhetővé teszi az adatoklekérdezést lekérdezi. Ezután elküldheti az eredményeket a felhasználó böngészőjének. Ez a beállítás ideális nagy adatkészletek vagy gyakran frissített adatkészletek esetén. Ez a lehetőség azonban több fejlesztési munkát igényel, és magasabb költségeket igényel. 

Egy másik megközelítés az adatkészlet átalakítása egy sima szöveges fájlba, amelyet a böngésző könnyen elemezhet. Maga a fájl az alkalmazás többi részével is üzemeltethető. Ez a beállítás egyszerűvé teszi a dolgokat, de csak kisebb adatkészletek esetén érdemes, mert a felhasználó letölti az összes adatot. Az adatkészlethez a sima szövegfájlt használjuk, mivel az adatfájl mérete kisebb, mint 1 MB.  

Ha a munkafüzetet egylapra szóló szövegfájllá szeretné konvertálni, mentse a munkafüzetet tabulátorral tagolt fájlként. Minden oszlopot egy tabulátorkarakter határolt, ami megkönnyíti az oszlopok elemzését a kódunkban. Használhat vesszővel tagolt érték (CSV) formátumot, de ez a beállítás több elemzési logikát igényel. Minden olyan mező, amely körül vessző van, idézőjelekkel van becsomagolva. Ha ezeket az adatokat tabulátorral tagolt fájlként szeretné exportálni az Excelben, válassza a **Mentés másként**lehetőséget. A **Fájltípus** legördülő listában válassza **a Szöveg (Tabulátorral tagolt) (*.txt) lehetőséget.** Nevezze el a fájlt *ContosoCoffee.txt*. 

<center>

![Képernyőkép a Szövegként párbeszédpanelről](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Ha megnyitja a szövegfájlt a Jegyzettömbben, az az alábbi ábrához hasonlóan jelenik meg:

<center>

![Képernyőkép egy jegyzettömbfájlról, amely tabulátorral tagolt adatkészletet jelenít meg](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>A projekt beállítása

A projekt létrehozásához használhatja a [Visual Studio](https://visualstudio.microsoft.com) alkalmazást vagy az Ön által választott kódszerkesztőt. A projektmappában hozzon létre három fájlt: *index.html*, *index.css*és *index.js*. Ezek a fájlok határozzák meg az alkalmazás elrendezését, stílusát és logikáját. Hozzon létre egy *adatoknevű mappát,* és adja hozzá a *ContosoCoffee.txt fájlt* a mappához. Hozzon létre egy másik *mappát,* amelynek neve képek . Az általunk használt 10 kép ebben az alkalmazásban az ikonok, gombok, és markerek a térképen. Letöltheti [ezeket a képeket](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). A projektmappának most az alábbi ábrához hasonlóan kell kinéznie:

<center>

![Képernyőkép az Egyszerű tárolókereső projekt mappájáról](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>A felhasználói felület létrehozása

A felhasználói felület létrehozásához adjon kódot az *index.html fájlhoz:*

1. Adja hozzá `meta` a `head` következő címkéket az *index.html*. A `charset` címke határozza meg a karakterkészletet (UTF-8). Az internet `http-equiv` Explorer és a Microsoft Edge értéke a böngésző legújabb verzióinak használatát jelzi. Az utolsó `meta` címke pedig olyan nézetablakot határoz meg, amely jól működik az adaptív elrendezések esetében.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Hivatkozások hozzáadása az Azure Maps JavaScript- és CSS-fájlokhoz:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Hivatkozás hozzáadása az Azure Maps Services modulhoz. A modul egy JavaScript-kódtár, amely betakarja az Azure Maps REST-szolgáltatásokat, és megkönnyíti a JavaScript használatát. A modul hasznos a keresési funkciók bekapcsolása érdekében.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Hivatkozás hozzáadása *az index.js* és *az index.css értékhez:*

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. A dokumentum törzsében adjon `header` hozzá egy címkét. A `header` címkén belül adja hozzá az emblémát és a vállalat nevét.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. `main` Címke hozzáadása és szövegdobozsal és keresőgombbal elkészített keresőpanel létrehozása. Emellett adjon `div` hozzá hivatkozásokat a térképhez, a listapanelhez és a Saját hely GPS gombhoz.

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

Amikor elkészült, *az index.html* ennek [a példa index.html fájlnak kell kinéznie.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html)

A következő lépés a CSS-stílusok meghatározása. A CSS-stílusok határozzák meg az alkalmazás-összetevők lefektetését és az alkalmazás megjelenését. Nyissa meg *az index.css-t,* és adja hozzá a következő kódot. A `@media` stílus alternatív stílusbeállításokat határoz meg, amelyeket akkor használhat, ha a képernyő szélessége kisebb, mint 700 képpont.  

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

Futtassa most az alkalmazást, és megjelenik a fejléc, a keresőmező és a keresés gomb. De a térkép nem látható, mert még nincs betöltve. Ha megpróbálsz rákeresni, nem történik semmi. Be kell állítanunk a JavaScript logikát, amely a következő szakaszban ismertetjük. Ez a logika az üzletlokátor összes funkciójához hozzáfér.

## <a name="wire-the-application-with-javascript"></a>Az alkalmazás vezetékezése JavaScript-tel

Minden most létre a felhasználói felületen. Továbbra is hozzá kell adnunk a JavaScriptet az adatok betöltéséhez és elemzéséhez, majd az adatok rendereléséhez a térképen. A kezdéshez nyissa meg *az index.js-t,* és adjon hozzá kódot az alábbi lépéseknek.

1. Globális beállítások hozzáadásával megkönnyítheti a beállítások frissítését. Adja meg a térkép, az előugró ablak, az adatforrás, az ikonréteg és a HTML-jelölő változóit. Állítsa be a HTML-jelölőt a keresési terület középpontjának jelzésére. És adja meg az Azure Maps keresési szolgáltatás ügyfél példányát.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Kód hozzáadása az *index.js .add*code to index.js . A következő kód inicializálja a térképet. Hozzáadtunk egy [eseményfigyelőt,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) hogy megvárja, amíg az oldal betöltése befejeződik. Ezután bekötöttük az eseményeket, hogy figyelemmel kísérjük a térkép betöltését, és funkcionalitást biztosítsunk a keresés gombnak és a Saját hely gombnak.

   Amikor a felhasználó kiválasztja a keresés gombot, vagy beír egy helyet a keresőmezőbe, majd megnyomja az entert, a rendszer intelligens keresést indít a felhasználó lekérdezésével szemben. Adja át az ország ISO 2 `countrySet` értékek et arra a lehetőségre, hogy a keresési eredményeket ezekre az országokra/régiókra korlátozza. Az országok/régiók keresésre való korlátozása növeli a visszaadott eredmények pontosságát. 
  
   Miután a keresés befejeződött, vegye be az első eredményt, és állítsa be a térképkamerát az adott területre. Amikor a felhasználó a Saját hely gomb kiválasztását választja, olvassa be a felhasználó tartózkodási helyét a HTML5 geolokációs API-val. Ez az API be van építve a böngészőbe. Ezután középre a térképet a helyük felett.  

   > [!Tip]
   > Előugró ablakok használataesetén a legjobb, ha egyetlen `Popup` példányt hoz létre, és a példány tartalmát és pozícióját frissíti. Minden `Popup`példány, amit hozzáad a kódot, több DOM elemek et adnak hozzá az oldalhoz. Minél több DOM elem van egy oldalon, annál több dolog, amit a böngészőnek nyomon kell követnie. Ha túl sok elem van, a böngésző lelassulhat.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
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
            retryOptions: { maxTries: 4 } // Retry options
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

1. A térkép eseményfigyelőjében `ready` adjon hozzá egy nagyításvezérlőt és egy HTML-jelölőt a keresési terület középpontjának megjelenítéséhez.

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

1. A térkép eseményfigyelő, `ready` adjon hozzá egy adatforrást. Ezután kezdeményezze az adatkészlet betöltését és elemzését. Engedélyezze a fürtözést az adatforráson. Az adatforrás fürtözése a fürt egymást átfedő pontjait csoportosítja. A fürtök a felhasználó nagyításakor különálló pontokra válnak. Ez a viselkedés jobb felhasználói élményt nyújt, és javítja a teljesítményt.

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

1. Miután betölti az adatkészletet `ready` a térkép eseményfigyelője, definiálja a rétegek az adatok megjelenítéséhez egy sor. A buborékréteg a fürtözött adatpontok megjelenítésére szolgál. A szimbólumréteg segítségével az egyes fürtökben lévő pontok száma a buborékréteg felett jelenik meg. A második szimbólumréteg a térképen az egyes helyek egyéni ikonját jeleníti meg.

   A `mouseover` `mouseout` buborék- és ikonrétegek hozzáadásával és eseményeivel módosíthatja az egérmutatót, amikor a felhasználó egy fürt vagy ikon fölé viszi az egérmutatót a térképen. Adjon `click` hozzá egy eseményt a fürtbuborék-réteghez. Ez `click` az esemény két szintet nagyít a térképen, és a fürt fölé igazítja a térképet, amikor a felhasználó kiválaszt egy fürtöt. `click` Esemény hozzáadása az ikonréteghez. Ez `click` az esemény egy előugró ablakot jelenít meg, amely egy kávézó adatait jeleníti meg, amikor a felhasználó kiválaszt egy adott helyikont. Adjon hozzá egy eseményt a térképhez, amelyet figyelni szeretne, ha a térkép áthelyezése befejeződik. Amikor az esemény bekövetkezik, frissítse a listapanel elemeit.  

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
            textField: ['get', 'point_count_abbreviated'],
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

1. A kávézó-adatkészlet betöltésekor először le kell tölteni. Ezután a szövegfájlt sorokra kell osztani. Az első sor a fejlécadatait tartalmazza. Annak érdekében, hogy a kód könnyebben követhető legyen, elemezzük a fejlécet egy objektumba, amelyet aztán felhasználhatunk az egyes tulajdonok cellaindexének felkutatására. Az első sor után haladja végig a többi vonalat, és hozzon létre egy pontjellemzőt. Adja hozzá a pontjellemzőt az adatforráshoz. Végül frissítse a listapanelt.

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

1. A listapanel frissítésekor a program kiszámítja a távolságot. Ez a távolság a térkép középpontjától az aktuális térképnézet összes pontjellemzőjéig. A funkciók ezután távolság szerint vannak rendezve. A HTML-kód az egyes helyeket jeleníti meg a listapanelen.

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

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

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

                //Get the distance of the shape.
                distances[shape.getId()],
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

1. Amikor a felhasználó kijelöl egy elemet a listapanelen, az adatforrásból az az alakzat, amelyhez az elem kapcsolódik, beolvassa az elemet. A létrejön egy előugró ablak, amely az alakzatban tárolt tulajdonságadatokon alapul. A térkép az alakzat fölé van igazítva. Ha a térkép 700 képpontnál kisebb, a térképnézet el lesz tolva, így az előugró ablak láthatóvá válik.

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

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
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
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
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

Most, van egy teljesen működőképes üzlet lokátor. Webböngészőben nyissa meg az *index.html* fájlt az üzletkeresőhöz. Amikor a fürtök megjelennek a térképen, a keresőmező segítségével, a Saját hely gomb kiválasztásával, a fürt kiválasztásával vagy a térképen az egyes helyek megtekintéséhez nagyítva kereshet egy helyet.

Amikor a felhasználó először választja ki a Saját hely gombot, a böngésző biztonsági figyelmeztetést jelenít meg, amely engedélyt kér a felhasználó tartózkodási helyének elérésére. Ha a felhasználó beleegyezik, hogy megosztja a tartózkodási helyét, a térkép ráközelít a felhasználó tartózkodási helyére, és megjelennek a közeli kávézók. 

<center>

![Képernyőkép a böngésző nek a felhasználó tartózkodási helyének elérésére vonatkozó kéréséről](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Ha elég közel nagyít egy olyan területen, ahol kávézó helyek találhatók, a fürtök különálló helyekre válnak. Jelölje ki az egyik ikont a térképen, vagy jelöljön ki egy elemet az oldalsó panelen egy előugró ablak megtekintéséhez. Az előugró ablak a kijelölt hely adatait jeleníti meg.

<center>

![Képernyőkép a kész üzletkeresőről](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Ha a böngészőablakot 700 képpontnál kisebb rekreál, vagy mobileszközön nyitja meg az alkalmazást, az elrendezés úgy változik, hogy jobban megfelel a kisebb képernyőknek. 

<center>

![Képernyőkép az üzletkereső kisképernyős verziójáról](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy alapvető üzletlokátort az Azure Maps használatával. Az oktatóanyagban létrehozott üzletkereső rendelkezhet az összes szükséges funkcióval. Az üzletkeresőhöz hozzáadhat funkciókat, vagy további előzetes funkciókat is használhat az egyénibb felhasználói élmény érdekében: 

> [!div class="checklist"]
> * Javaslatok engedélyezése a keresőmezőbe [beírás közben.](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI)  
> * [Több nyelv támogatásának](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)hozzáadása. 
> * Lehetővé teszi a felhasználó számára, hogy szűrje a [helyeket az útvonal mentén.](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route) 
> * Adja hozzá a [szűrők beállításának](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)lehetőségét. 
> * Támogatás hozzáadásával lekérdezési karakterlánc használatával adhatja meg a kezdeti keresési értéket. Ha ezt a beállítást beilleszti az üzletkeresőbe, a felhasználók könyvjelzővel láthatják el és megoszthatják a kereséseket. Azt is előírja, egy egyszerű módszer az Ön számára, hogy adja át keres erre az oldalra egy másik oldalon.  
> * Telepítse az üzletkeresőt [egy Azure App Service Web App néven.](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html) 
> * Tárolja adatait egy adatbázisban, és keresse meg a közeli helyeket. További információ: [AZ SQL Server térbeli adattípusok áttekintése](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) és A legközelebbi szomszéd [térbeli adatok lekérdezése](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017)című témakörben olvashat.

> [!div class="nextstepaction"]
> [Teljes forráskód megtekintése](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Élő minta megtekintése](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

További információ az Azure Maps lefedettségéről és képességeiről:

> [!div class="nextstepaction"]
> [Nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md)

További példakódok és az interaktív kódolási felület bemutatása:

> [!div class="nextstepaction"]
> [A térképvezérlő használata](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)
