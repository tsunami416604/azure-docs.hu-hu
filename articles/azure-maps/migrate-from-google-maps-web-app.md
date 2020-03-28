---
title: 'Oktatóanyag: Webalkalmazás áttelepítése a Google Térképről | Microsoft Azure Maps'
description: Webalkalmazás áttelepítése a Google Térképről a Microsoft Azure Maps szolgáltatásba.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bdbf2a975cbdc3d06745b9375c1e6f8e751ddfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77914084"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Webalkalmazás áttelepítése a Google Térképről

A legtöbb, a Google Térképet használó webes alkalmazás a Google Térkép V3 JavaScript SDK-t használja. Az Azure Maps Web SDK a megfelelő Azure-alapú SDK-k áttérni. Az Azure Maps Web SDK lehetővé teszi az interaktív térképek testreszabását saját tartalmaival és képeivel. Az alkalmazást webes vagy mobilalkalmazásokban is futtathatja. Ez a vezérlő a WebGL-t használja, amely lehetővé teszi nagy adatkészletek nagy teljesítményű renderelését. Dolgozzon ezzel az SDK-val JavaScript vagy TypeScript használatával.

Meglévő webalkalmazás áttelepítésekor ellenőrizze, hogy nyílt forráskódú térképvezérlőt használ-e. Példák a nyílt forráskódú térképvezérlő könyvtárra: Cézium, Szórólap és OpenLayers. Továbbra is áttelepítheti az alkalmazást, még akkor is, ha nyílt forráskódú térképvezérlő könyvtárat használ, és nem szeretné használni az Azure Maps Web SDK-t. Ebben az esetben csatlakoztassa az alkalmazást az Azure Maps csempeszolgáltatásokhoz ([útcsempék](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [műholdas csempéi).](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Az alábbi pontok részletesen ismertetik, hogyan használhatja az Azure Maps néhány általánosan használt nyílt forráskódú térképvezérlő tárak.

- Cézium - A 3D-s térkép vezérlő az interneten. [Kódminta](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [dokumentációja](https://cesiumjs.org/)
- Szórólap – Könnyű 2D térképvezérlés az interneten. [Kódminta](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [dokumentációja](https://leafletjs.com/)
- OpenLayers – A web 2D-s térképvezérlője, amely támogatja a kivetítéseket. [Kódminta](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [dokumentációja](https://openlayers.org/)

## <a name="key-features-support"></a>A legfontosabb funkciók támogatása

A táblázat felsorolja a Google Maps V3 JavaScript SDK legfontosabb API-funkcióit és az Azure Maps Web SDK támogatott API-funkcióját.

| A Google Térkép funkció     | Az Azure Maps Web SDK támogatása |
|-------------------------|:--------------------------:|
| Jelölők                 | ✓                          |
| Jelölőfürt-fürtözés       | ✓                          |
| Vonalláncok & sokszögek    | ✓                          |
| Adatrétegek             | ✓                          |
| Földelt átfedések         | ✓                          |
| Hőtérképek               | ✓                          |
| Mozaikrétegek             | ✓                          |
| KML-réteg               | ✓                          |
| Rajzeszközök           | ✓                          |
| Geokódoló szolgáltatás        | ✓                          |
| Irányváltásszolgáltatás      | ✓                          |
| Távolságmátrix szolgáltatás | ✓                          |
| Magassági szolgáltatás       | Tervezve                    |

## <a name="notable-differences-in-the-web-sdks"></a>Jelentős különbségek a webes SDK-kban

Az alábbiakban néhány fontos különbséget talál a Google Térkép és az Azure Maps webes SDK-k között, amelyeket figyelembe kell venni:

- Amellett, hogy egy üzemeltetett végpont az Azure Maps Web SDK eléréséhez, egy NPM-csomag érhető el. A Web SDK-csomag beágyazása az alkalmazásokba. További információt ebben a [dokumentációban](how-to-use-map-control.md)talál. Ez a csomag TypeScript-definíciókat is tartalmaz.
- Először létre kell hoznia egy példányt a Map osztály az Azure Mapsben. Várja meg, `ready` `load` amíg a térképek vagy az esemény kigyulladnak, mielőtt programozott módon kommunikálna a térképpel. Ez a sorrend biztosítja, hogy az összes térkép-erőforrás be van töltve, és készen áll a hozzáférésre.
- Mindkét platform hasonló csempézési rendszert használ az alaptérképekhez. A Google Térkép csempéi 256 képpont méretűek; az Azure Maps csempéi azonban 512 képpont méretűek. Ha ugyanazt a térképnézetet szeretné beszerezni az Azure Mapsben, mint a Google Térképen, vonhatja ki a Google Térkép nagyítási szintjét az Azure Maps első számú elemével.
- A Google Térkép koordinátáit "szélességi, hosszúsági" néven nevezik, míg az Azure Maps a "hosszúsági szélesség"-et használja. Az Azure Maps formátum igazodik `[x, y]`a standard , amely követi a legtöbb GIS platformok.
- Az Azure Maps Web SDK alakzatai a GeoJSON-sémán alapulnak. A segítő osztályok az [ *atlas.data* névtéren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)keresztül vannak elérhetővé. Ott van még az [*atlasz is. Alakzat*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) osztály. Ezzel az osztállyal körbefolyathatja a GeoJSON-objektumokat, hogy megkönnyítse az adatok kötésre való lekötésének és karbantartásának.
- Az Azure Maps koordinátái pozícióobjektumokként vannak definiálva. A koordináta számtömbként `[longitude,latitude]`van megadva a formátumban. Vagy új atlas.data.Position(hosszúság, szélesség) használatával van megadva.
    > [!TIP]
    > A Pozíció osztály statikus segítő módszerrel rendelkezik a "szélességi, hosszúsági" formátumú koordináták importálásához. Az [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) metódus gyakran helyettesíthető `new google.maps.LatLng` a Google Térkép-kódban található módszerrel.
- Ahelyett, hogy a térképhez hozzáadott minden alakzat stílusadatait megadnák, az Azure Maps elválasztja a stílusokat az adatoktól. Az adatok adatforrásokban tárolódnak, és renderelési rétegekhez kapcsolódnak. Az Azure Maps-kód adatforrások használatával jeleníti meg az adatokat. Ez a megközelítés nagyobb teljesítményelőnyöket biztosít. Emellett sok réteg támogatja az adatközpontú stílust, ahol az üzleti logika hozzáadható a rétegstílus-beállításokhoz. Ez a támogatás módosítja az egyes alakzatok renderelésének módját egy rétegen belül az alakzatban meghatározott tulajdonságok alapján.

## <a name="web-sdk-side-by-side-examples"></a>Webes SDK egymás melletti példák

Ez a gyűjtemény kódmintákat minden platform, és minden minta kiterjed egy közös használati eset. Célja, hogy segítsen áttelepíteni a webalkalmazást a Google Maps V3 JavaScript SDK-ból az Azure Maps Web SDK-ba. A webalkalmazásokkal kapcsolatos kódminták JavaScript-ben találhatók. Az Azure Maps azonban TypeScript-definíciókat is biztosít egy [NPM-modulon](how-to-use-map-control.md)keresztül.

### <a name="load-a-map"></a>Térkép betöltése

Mindkét SDK-k ugyanazokkal a lépésekkel rendelkeznek a térkép betöltéséhez:

- Hivatkozás hozzáadása a Map SDK-hoz.
- Adjon `div` hozzá egy címkét az oldal törzséhez, amely a térkép helyőrzőjeként fog működni.
- Hozzon létre egy JavaScript-függvényt, amely akkor lesz hívva, amikor az oldal betöltődik.
- Hozzon létre egy példányt a megfelelő térképosztályból.

**Néhány kulcsfontosságú különbség**

- A Google Maps megköveteli, hogy egy fiókkulcsot meg kell adni az API parancsfájlhivatkozásában. Az Azure Maps hitelesítési hitelesítő adatai a térképosztály beállításaiként vannak megadva. Ez a hitelesítő adat lehet egy előfizetési kulcs vagy az Azure Active Directory-adatok.
- A Google Térkép elfogadja a visszahívási függvényt az API parancsfájlhivatkozásában, amely egy inicializálási függvény meghívására szolgál a térkép betöltéséhez. Az Azure Maps, a lap betöltési eseményét kell használni.
- Ha hivatkozik `div` az elem, amelyben a térkép `Map` jelenik meg, az `id` osztály az Azure `HTMLElement` Maps csak az értéket, míg a Google Maps igényel egy objektumot.
- Az Azure Maps koordinátái pozícióobjektumokként vannak definiálva, amelyek `[longitude, latitude]`a formátumban egyszerű számtömbként adhatók meg.
- Az Azure Maps nagyítási szintje egy szinttel alacsonyabb, mint a Google Térkép nagyítási szintje. Ez az eltérés azért van, mert a különbség a méret ben csempézés rendszer a két platform.
- Az Azure Maps nem ad hozzá navigációs vezérlőket a térképvászonhoz. Tehát alapértelmezés szerint a térképen nincsenek nagyítási gombok és térképstílus gombok. A térképstílus-választó, a nagyítási gombok, az iránytű vagy az elforgatás vezérlője, valamint a hangmagasság-vezérlő hozzáadásához azonban vannak vezérlési lehetőségek.
- Egy eseménykezelő van hozzáadva az `ready` Azure Maps ben a térképpéldány eseményének figyeléséhez. Ez az esemény akkor jelenik meg, ha a térkép betöltötte a WebGL környezetet és az összes szükséges erőforrást. Adja hozzá a leképezés betöltése után futtatni kívánt kódot ehhez az eseménykezelőhöz.

Az alábbi alapvető példák a Google Térkép segítségével töltik be a New York-i térképet a koordinátákon. A hosszúság: -73.985, szélesség: 40.747, és a térkép 12-es nagyítási szinten van.

**Előtte: Google Térkép**

A Google Térkép középre igazított és nagyított megjelenítése egy adott helyre.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Ha ezt a kódot böngészőben futtatja, a következő höz hasonló térkép jelenik meg:

<center>

![Egyszerű Google Térkép](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Utána: Azure Maps**

Töltsön be egy térképet ugyanazzal a nézettel az Azure Mapsben egy térképstílus-vezérlő és nagyítási gombokkal együtt.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Ha ezt a kódot böngészőben futtatja, a következő höz hasonló térkép jelenik meg:

<center>

![Egyszerű Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Ide [kattintva](how-to-use-map-control.md)részletes dokumentációt talál arról, hogyan állíthatja be és használhatja az Azure Maps térképvezérlőt egy webalkalmazásban.

> [!NOTE]
> A Google Térképtől eltérően az Azure Maps nem igényel kezdeti középpontot és nagyítási szintet a térkép betöltéséhez. Ha ez az információ nem áll meg a térkép betöltésekor, az Azure maps megpróbálja meghatározni a felhasználó városát. Középre fog tárni és ráközelít a térképre.

**További források:**

- Az Azure Maps navigációs vezérlőket is biztosít a térképnézet forgatásához és feldobásához, [az itt](map-add-controls.md)dokumentáltan.

### <a name="localizing-the-map"></a>A térkép honosítása

Ha a célközönséged több országban is elterjedt, vagy különböző nyelveket beszélsz, fontos a honosítás.

**Előtte: Google Térkép**

A Google Térkép honosításához adja hozzá a nyelvi és régióparamétereket.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Íme egy példa a Google Térképre, amelynek nyelve "fr-FR" .

<center>

![Google Térkép honosítása](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Utána: Azure Maps**

Az Azure Maps két különböző módon állítja be a térkép nyelvi és regionális nézetét. Az első lehetőség az, hogy ezt az információt hozzáadja a globális *atlasz* névtérhez. Ez azt eredményezi, hogy az alkalmazás összes térképvezérlő példánya alapértelmezés szerint ezeket a beállításokat adja meg. A következőkben a nyelv francia ("fr-FR") és a regionális nézet "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A második lehetőség az, hogy adja át ezt az információt a térkép opciók betöltésekor a térképet. tetszik:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Az Azure Maps segítségével több térképpéldány ttölthet be ugyanazon az oldalon különböző nyelvi és régióbeállításokkal. Az is lehetséges, hogy frissítse ezeket a beállításokat a térképen, miután betöltötte. 

A [támogatott nyelvek](supported-languages.md) részletes listáját megtalálhatja az Azure Mapsben.

Íme egy példa az Azure Maps a nyelv beállítása "fr" és a felhasználói régió beállítása "fr-FR".

<center>

![Az Azure Maps honosítása](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>A térképnézet beállítása

Az Azure és a Google Térkép dinamikus térképei programozott módon új földrajzi helyekre helyezhetők át. Ehhez hívja meg a megfelelő függvényeket a JavaScriptben. A példák bemutatják, hogyan lehet a térképen műholdas légi felvételeket megjeleníteni, a térképet egy hely fölé középre, és a zoomszintet 15-re változtatni a Google Térképen. A következő helykoordinátákat használjuk: hosszúság: -111.0225 és szélesség: 35.0272.

> [!NOTE]
> A Google Térkép 256 képpont méretű csempéket használ, míg az Azure Maps nagyobb 512 képpontos csempét. Így az Azure Maps kevesebb hálózati kérelmet igényel a Google Térképhez hasonló térképterület betöltéséhez. A csempepiramisok térképvezérlőkben való működésének köszönhetően ki kell vonnia a Google Térképben használt nagyítási szintet az Azure Maps használatakor az első számmal. Ez a számtani művelet biztosítja, hogy az Azure Maps nagyobb csempéi ugyanazt a térképterületet jelenítsék meg, mint a Google Térképen,

**Előtte: Google Térkép**

A Google Térkép térképvezérlő `setOptions` áthelyezése a módszerrel. Ez a módszer lehetővé teszi a térkép középpontjának és a nagyítási szintnek a megadását.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Térkép készlet nézet](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Utána: Azure Maps**

Az Azure Mapsben módosítsa a `setCamera` térkép pozícióját a módszerrel, és módosítsa a térképstílusát a `setStyle` módszerrel. Az Azure Maps koordinátái "hosszúsági, szélességi" formátumúak, és a nagyítási szint értékét eggyel vonják ki.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Az Azure Maps készletnézete](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**További források:**

- [Térképstílus kiválasztása](choose-map-style.md)
- [Támogatott térképstílusok](supported-map-styles.md)

### <a name="adding-a-marker"></a>Jelölő hozzáadása

Az Azure Mapsben többféle módon is megjeleníthetők a pontadatok a térképen:

- **HTML markerek** - Rendereli pontok hagyományos DOM elemeket. A HTML-jelölők támogatják a húzást.
- **Szimbólumréteg** – A pontokat ikonnal vagy szöveggel rendereli a WebGL környezetben.
- **Buborékréteg** – A pontokat körökként rendereli a térképen. A körök sugarai az adatok tulajdonságai alapján méretezhetők.

Szimbólumrétegek és buborékrétegek renderelése a WebGL környezetben. Mindkét réteg nagy pontkészleteket jeleníthet meg a térképen. Ezeka rétegek adatforrásban kell tárolni az adatokat. Az esemény kilövése után hozzá kell `ready` adni az adatforrásokat és a renderelési rétegeket a térképhez. A HTML-jelölők dom-elemként jelennek meg az oldalon belül, és nem használnak adatforrást. Minél több DOM elem van egy oldalnak, annál lassabb lesz az oldal. Ha néhány száz nál több pontot renderel a térképen, ajánlott inkább a renderelési rétegek egyikét használni.

Adjunk hozzá egy jelölőt a térképhez a 10-es számmal, címkeként. Hosszúság használata: -0,2 és szélesség: 51.5.

**Előtte: Google Térkép**

A Google Térkép segítségével jelölőket adhat `google.maps.Marker` a térképhez az osztály használatával, és adja meg a térképet az egyik lehetőségként.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Térkép jelölő](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Utána: Az Azure Maps HTML-jelölők használatával**

Az Azure Mapsben html-jelölők használatával jelenítsen meg egy pontot a térképen. Html-jelölők használata olyan alkalmazásokhoz ajánlott, amelyeknek csak kis számú pontot kell megjelenítenie a térképen. HTML-jelölő használatához hozzon létre `atlas.HtmlMarker` egy példányt az osztályból. Adja meg a szöveg- és pozícióbeállításokat, `map.markers.add` és adja hozzá a jelölőt a térképhez a módszerrel.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Az Azure Maps HTML-jelölője](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Utána: Az Azure Maps szimbólumréteghasználatával**

Szimbólumréteg esetén adja hozzá az adatokat egy adatforráshoz. Csatolja az adatforrást a réteghez. Emellett az adatforrást és a réteget hozzá `ready` kell adni a térképhez az esemény elsüllyesztése után. Ahhoz, hogy egy egyedi szöveges értéket egy szimbólum fölé lehessen tenni, a szöveges információt az adatpont tulajdonságaként kell tárolni. A tulajdonságra a `textField` réteg beállításában hivatkozni kell. Ez a megközelítés egy kicsit több munkát, mint a HTML markerek, de jobb teljesítményt.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Az Azure Maps szimbólumrétege](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**További források:**

- [Adatforrás létrehozása](create-data-source-web-sdk.md)
- [Szimbólumréteg hozzáadása](map-add-pin.md)
- [Buborékréteg hozzáadása](map-add-bubble-layer.md)
- [Fürt pontadatai](clustering-point-data-web-sdk.md)
- [HTML-jelölők hozzáadása](map-add-custom-html.md)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)
- [Szimbólumréteg ikonbeállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Szimbólumréteg szövegének beállítása](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-jelölőosztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML-jelölő beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Egyéni jelölő hozzáadása

Egyéni képek segítségével ábrázolhatja a térkép pontjait. Az alábbi térkép egy egyéni képet használ egy pont megjelenítéséhez a térképen. A pont az 51,5 szélességi fokon és a hosszúsági fokon jelenik meg: -0,2. A horgony eltolja a jelölő pozícióját, így a nyomótű ikonjának pontja igazodik a térképen a megfelelő pozícióhoz.

<center>

![sárga nyomótű képe](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Előtte: Google Térkép**

Egyéni jelölő létrehozása a `Icon` képhez `url` tartalmazó objektum megadásával. Adjon `anchor` meg egy pontot, amely a tolószögkép pontját a térképen lévő koordinátához igazítja. A Google Térkép horgonyértéke a kép bal felső sarkához viszonyítva érhető el.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![A Google Térkép egyéni jelölője](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Utána: Az Azure Maps HTML-jelölők használatával**

Html-jelölő testreszabásához adja `string` `HTMLElement` át `htmlContent` a HTML-kódot vagy a jelölő beállítását. Ezzel `anchor` a beállítással adhatja meg a jelölő relatív helyzetét a beosztáskoordinátához viszonyítva. Rendelje hozzá a beállításhoz a `anchor` kilenc definiált hivatkozási pont egyikét. Ezek a meghatározott pontok a következők: "közép", "fent", "alul", "bal", "jobb", "bal felső", "jobb felső", "bal alsó", "jobb alsó". A tartalom alapértelmezés szerint a html tartalom alsó középpontjához van rögzítve. A google térképről való kódáttelepítés megkönnyítése érdekében állítsa a `anchor` "bal `pixelOffset` felső sarok" beállítást, majd használja a Google Térképben használt eltolással ellátott beállítást. Az Azure Maps eltolásai a Google Térkép eltolásainak ellenkező irányában mozognak. Szóval szorozzuk meg az eltolásokat mínusz 1-el.

> [!TIP]
> Adja `pointer-events:none` hozzá stílusként a html tartalmat, hogy letiltsa az alapértelmezett húzási viselkedést a Microsoft Edge-ben, amely egy nem kívánt ikont jelenít meg.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Az Azure Maps egyéni HTML-jelölője](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Utána: Az Azure Maps szimbólumréteghasználatával**

Az Azure Maps szimbólumrétegei egyéni lemezképeket is támogatnak. Először töltse be a képet a térkép erőforrások at, és rendelje hozzá egy egyedi azonosítót. Hivatkozzon a szimbólumrétegben lévő képre. Ezzel `offset` a beállítással igazíthatja a képet a térkép megfelelő pontjához. Ezzel `anchor` a beállítással adhatja meg a szimbólum relatív helyzetét a pozíciókoordinátákhoz képest. Használja a kilenc meghatározott referenciapont egyikét. Ezek a pontok a következők: "közép", "fent", "alul", "bal", "jobb", "bal felső", "jobb felső", "bal alsó", "jobb alsó". A tartalom alapértelmezés szerint a html tartalom alsó középpontjához van rögzítve. A google térképről való kódáttelepítés megkönnyítése érdekében állítsa a `anchor` "bal `offset` felső sarok" beállítást, majd használja a Google Térképben használt eltolással ellátott beállítást. Az Azure Maps eltolásai a Google Térkép eltolásainak ellenkező irányában mozognak. Szóval szorozzuk meg az eltolásokat mínusz 1-el.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Az Azure Maps egyéni ikonszimbólum-rétege](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> A speciális egyéni pontok megjelenítéséhez használjon több renderelési réteget együtt. Tegyük fel például, hogy több olyan nyomógombot szeretne, amelyek azonos ikonnal rendelkeznek a különböző színű körökben. Ahelyett, hogy minden színátfedéshez egy csomó képet hozna létre, adjon hozzá egy szimbólumréteget a buborékréteg tetejére. A nyomótűk hivatkoznak ugyanarra az adatforrásra. Ez a megközelítés hatékonyabb lesz, mint létrehozni és fenntartani egy csomó különböző képeket.

**További források:**

- [Adatforrás létrehozása](create-data-source-web-sdk.md)
- [Szimbólumréteg hozzáadása](map-add-pin.md)
- [HTML-jelölők hozzáadása](map-add-custom-html.md)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)
- [Szimbólumréteg ikonbeállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Szimbólumréteg szövegének beállítása](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-jelölőosztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML-jelölő beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Vonallánc hozzáadása

Vonalláncok használatával jelölhet vonalat vagy görbét a térképen. Hozzunk létre egy szaggatott vonalláncot a térképen.

**Előtte: Google Térkép**

A Vonallánc osztály egy sor beállítást fogad el. Adja át a koordináták tömbjét a `path` vonallánc beállításában.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Térkép vonallánc](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Utána: Azure Maps**

A vonalláncok `LineString` `MultiLineString` at vagy objektumokat hívnak. Ezek az objektumok hozzáadhatók egy adatforráshoz, és vonalréteg használatával renderelhetők. Adjon `LineString` hozzá egy adatforráshoz, majd adja `LineLayer` hozzá az adatforrást a rendereléshez.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Az Azure Maps vonallánc](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**További források:**

- [Sorok hozzáadása a térképhez](map-add-line-layer.md)
- [Vonalréteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Sokszög hozzáadása

Az Azure Maps és a Google Térkép hasonló támogatást nyújt a sokszögekszámára. A sokszögek a térkép egy területének ábrázolására szolgálnak. Az alábbi példák bemutatják, hogyan hozhat létre olyan sokszöget, amely a térkép középső koordinátája alapján háromszöget képez.

**Előtte: Google Térkép**

A Sokszög osztály egy sor beállítást fogad el. Adja át a koordináták at the `paths` option of the polygon.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Térkép sokszög](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Utána: Azure Maps**

Objektumok `Polygon` hozzáadása `MultiPolygon` adatforráshoz. Renderelje az objektumot a térképen rétegek használatával. Sokszögréteg gel rendereli a sokszög et. És a sokszög körvonalát vonalréteg használatával renderelje.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps sokszög](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**További források:**

- [Sokszög hozzáadása a térképhez](map-add-shape.md)
- [Kör hozzáadása a térképhez](map-add-shape.md#add-a-circle-to-the-map)
- [Sokszögréteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Vonalréteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Információs ablak megjelenítése

Az entitásokkal kapcsolatos további információk osztályként `google.maps.InfoWindow` jeleníthetők meg a térképen a Google Térképen. Az Azure Mapsben ez a funkció `atlas.Popup` az osztály használatával érhető el. A következő példák jelölőt adnak a térképhez. Amikor a jelölőre kattint, egy információs ablak vagy egy előugró ablak jelenik meg.

**Előtte: Google Térkép**

Infoablak példányosítsa `google.maps.InfoWindow` meg a konstruktora segítségével.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Térkép felugró ablak](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Utána: Azure Maps**

Előugró ablaksegítségével jelenítsünk meg további információkat a helyről. Html vagy `string` `HTMLElement` objektum átadása az `content` előugró ablak beállításának. Ha szeretné, a felugró ablakok bármely alakzattól függetlenül megjeleníthetők. Így az előugró `position` ablakokhoz meg kell adni egy értéket. Adja `position` meg az értéket. Előugró ablak megjelenítéséhez `open` hívja meg `map` a metódust, és adja át azt, amelyben a felugró ablak megjelenik.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Az Azure Maps előugró ablaka](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Ugyanezt teheti egy szimbólummal, buborékkal, vonallal vagy poligonréteggel, ha a kiválasztott réteget a térképek eseménykódjára adja át jelölő helyett.

**További források:**

- [Előugró ablak hozzáadása](map-add-popup.md)
- [Előugró ablak médiatartalommal](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Előugró ablakok alakzatokon](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Felugró ablak újrafelhasználása több pinekkel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Előugró osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Előugró beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>GeoJSON-fájl importálása

A Google Térkép támogatja a GeoJSON-adatok `google.maps.Data` betöltését és dinamikus formázását az osztályon keresztül. Az osztály funkciói sokkal inkább igazodnak az Azure Maps adatalapú stílusához. De van egy fontos különbség. A Google Térkép segítségével megadhat egy visszahívási funkciót. Az üzleti logika stílus minden funkció, hogy a feldolgozott külön-külön a felhasználói felület szál. Az Azure Mapsben azonban a rétegek támogatják az adatközpontú kifejezések stílusként való megadását. Ezeket a kifejezéseket a renderelési időpontban külön szálon dolgozza fel a folyamat. Az Azure Maps megközelítés javítja a renderelési teljesítményt. Ez az előny akkor figyelhető meg, ha a nagyobb adatkészletek gyorsan kell renderelni.

A következő példák betölteni a GeoJSON takarmány minden földrengések az elmúlt hét nap ban a USGS. Földrengések adatok teszi a méretezett körök a térképen. Az egyes körök színe és léptéke az egyes földrengések nagyságán `"mag"` alapul, amely az adatkészlet egyes funkcióinak tulajdonságában tárolódik. Ha a magnitúdó nagyobb vagy egyenlő, mint öt, a kör piros lesz. Ha ez nagyobb vagy egyenlő, hogy három, de kevesebb, mint öt, a kör narancssárga lesz. Ha kevesebb, mint három, a kör zöld lesz. Az egyes körök sugara a magnitúdó és a 0,1 nagymértékű exponenciális értéke lesz.

**Előtte: Google Térkép**

Adjon meg egyetlen visszahívási `map.data.setStyle` függvényt a metódusban. A visszahívási funkción belül alkalmazza az üzleti logikát az egyes funkciókra. Töltse be a GeoJSON-előtolást a `map.data.loadGeoJson` módszerrel.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Térkép GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Utána: Azure Maps**

A GeoJSON az Azure Maps alapadattípusa. Importálja adatforrásba a `datasource.importFromUrl` módszerrel. Használjon buborékréteget. A buborékréteg az adatforrás szolgáltatásainak tulajdonságai alapján biztosítja a méretezett körök renderelését. Ahelyett, hogy visszahívási függvény, az üzleti logika alakítja át egy kifejezést, és átad ják a stílus beállításokat. A kifejezések határozzák meg az üzleti logika működését. A kifejezések átadhatók egy másik szálnak, és kiértékelhetők a jellemzőadatok alapján. Több adatforrás és réteg is hozzáadható az Azure Maps, mindegyik különböző üzleti logikával. Ez a funkció lehetővé teszi, hogy több adatkészlet különböző módon jelenjen meg a térképen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**További források:**

- [Szimbólumréteg hozzáadása](map-add-pin.md)
- [Buborékréteg hozzáadása](map-add-bubble-layer.md)
- [Fürt pontadatai](clustering-point-data-web-sdk.md)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Jelölőfürt-fürtözés

A térképen sok adatpont megjelenítésekor a pontok átfedhetik egymást. Az egymást átfedő térkép zsúfoltnak tűnik, és a térkép nehezen olvasható és használható. A fürtözési pont adatai az egymáshoz közeli adatpontok egyesítésének és a térképen egyetlen fürtözött adatpontként való megjelenítésének folyamata. Ahogy a felhasználó ráközelít a térképre, a fürtök szétválnak az egyes adatpontjaikra. A fürtadatpontok javítják a felhasználói élményt és a leképezési teljesítményt.

A következő példákban a kód betölti a GeoJSON-hírcsatornát a múlt heti földrengésadatokból, és hozzáadja a térképhez. A fürtök méretezett és színes körökként jelennek meg. A körök léptéke és színe a benne lévő pontok számától függ.

> [!NOTE]
> A Google Térkép és az Azure Maps némileg eltérő fürtözési algoritmusokat használ. Így néha a csomópontok eloszlása a fürtökben változó.

**Előtte: Google Térkép**

Fürtjelölők fürtjelölők höz használja a MarkerCluster könyvtárat. A fürtikonok csak képekre korlátozódnak, amelyek nek a neve egytől ötig terjedő számokat tartalmaz. Ugyanabban a könyvtárban vannak.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Térkép-fürtözés](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Utána: Azure Maps**

Adatok hozzáadása és kezelése adatforrásban. Kapcsolja össze az adatforrásokat és a rétegeket, majd jelenítse meg az adatokat. Az `DataSource` Azure Maps osztálya számos fürtözési lehetőséget biztosít.

- `cluster`– Megmondja az adatforrásnak a fürtpont-adatokat.
- `clusterRadius`- A sugar képpontban a klaszter pontok együtt.
- `clusterMaxZoom`- Az a maximális nagyítási szint, amelyben a fürtözés történik. Ha ennél nagyobb rakoncátrál, az összes pont szimbólumként jelenik meg.
- `clusterProperties`- Olyan egyéni tulajdonságokat határoz meg, amelyeket az egyes fürtök összes pontja alapján kifejezések alapján számítanak ki, és hozzáadjuk az egyes fürtpontok tulajdonságaihoz.

Ha a fürtözés engedélyezve van, az adatforrás fürtözött és nem fürtözött adatpontokat küld rétegeknek renderelésre. Az adatforrás több százezer adatpont fürtözésére képes. A fürtözött adatpont tulajdonságai a következőtulajdonságokkal rendelkeznek:

| Tulajdonság neve             | Típus    | Leírás   |
|---------------------------|---------|---------------|
| `cluster`                 | logikai | Azt jelzi, hogy a szolgáltatás fürtöt jelöl-e. |
| `cluster_id`              | sztring  | A fürt egyedi azonosítója, amely használható a `getClusterExpansionZoom` `getClusterChildren`DataSource `getClusterLeaves` , és metódusok. |
| `point_count`             | szám  | A fürt által tartalmazott pontok száma.  |
| `point_count_abbreviated` | sztring  | Olyan karakterlánc, amely hosszú `point_count` ideig rövidíti az értéket. (például 4000 lesz 4K)  |

Az `DataSource` osztály a következő segítő funkcióval rendelkezik a `cluster_id`fürtről szóló további információk eléréséhez a használatával.

| Módszer | Visszatérés típusa | Leírás |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Ígéret&lt;&lt;tömbjellemző&lt;geometriája, bármilyen&gt; \| alakzat&gt;&gt; | Az adott fürt gyermekeinek lekérése a következő nagyítási szinten. Ezek a gyerekek lehetnek a formák és alklaszterek kombinációja. Az alfürtök fürtözött tulajdonságokkal rendelkező szolgáltatások lesznek. |
| `getClusterExpansionZoom(clusterId: number)` | Ígéret&lt;száma&gt; | Kiszámítja azt a nagyítási szintet, amelyen a fürt kitágul vagy széttörik. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Ígéret&lt;&lt;tömbjellemző&lt;geometriája, bármilyen&gt; \| alakzat&gt;&gt; | A fürt összes pontjának beolvasása. Állítsa `limit` be a pontok egy részhalmazának `offset` visszaadására, és használja a lapozáshoz a pontokat. |

Fürtözött adatok renderelésekor a térképen gyakran két vagy több réteget érdemes használni. A következő példa három réteget használ. Buborékréteg a csoportok mérete alapján méretezett színes körök rajzolása. A fürtméretének szövegként való megjelenítéséhez használt szimbólumréteg. És egy második szimbólumréteget használ a nem fürtözött pontok rendereléséhez. A fürtözött adatok megjelenítésének számos más módja is van. További információt a [Fürtpont-adatdokumentációban](clustering-point-data-web-sdk.md) talál.

Közvetlenül importálhatja a GeoJSON-adatokat az `importDataFromUrl` osztály függvényével, az `DataSource` Azure Maps térképen belül.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps fürtözés](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**További források:**

- [Szimbólumréteg hozzáadása](map-add-pin.md)
- [Buborékréteg hozzáadása](map-add-bubble-layer.md)
- [Fürt pontadatai](clustering-point-data-web-sdk.md)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Hőtérkép hozzáadása

A hőtérképek, más néven pontsűrűségi térképek, az adatvizualizáció egy típusa. Az adatok sűrűségét jelölik színtartomány használatával. És gyakran használják, hogy megmutassák az adatok "forró pontok" a térképen. A hőtérképek nagyszerű módja a nagy pontadatkészletek megjelenítésének.

A következő példák betölteni a GeoJSON takarmány minden földrengések az elmúlt hónapban, a USGS, és teszi őket, mint egy súlyozott hőtérkép. A `"mag"` súly a szálláshely.

**Előtte: Google Térkép**

Hőtérkép létrehozásához töltse be a "vizualizációs" könyvtárat az API-parancsfájl URL-címéhez való hozzáadással. `&libraries=visualization` A Google Térkép hőtérkép-rétege nem támogatja közvetlenül a GeoJSON-adatokat. Először töltse le az adatokat, és alakítsa át egy sor súlyozott adatpont:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps hőtérkép](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Utána: Azure Maps**

Töltse be a GeoJSON-adatokat egy adatforrásba, és csatlakoztassa az adatforrást egy hőtérkép-réteghez. A súlyhoz használt tulajdonság kifejezéssel átadható `weight` a beállításnak. Közvetlenül importálja a GeoJSON-adatokat `DataSource` az Azure Mapsbe az `importDataFromUrl` osztály függvényének használatával.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Az Azure Maps hőtérképe](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**További források:**

- [Hőtérképréteg hozzáadása](map-add-heat-map-layer.md)
- [Hőtérkép rétegosztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Hőtérkép réteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Mozaikréteg átfedése

Az Azure Maps mozaikrétegeit képátfedéseknek nevezzük a Google Térképen. A mozaikrétegek lehetővé teszik a kisebb mozaikképekre bontott nagy méretű képek átfedését, amelyek a térképmozaik-rendszerhez igazodnak. Ez a megközelítés gyakran nagy képek vagy nagy adatkészletek átfedéséhez használatos.

A következő példák egy időjárási radarcsempe-réteget fednek le az Iowa Imkeresztüli Állami Egyetem Iowa Environmental Mesonet-jéből.

**Előtte: Google Térkép**

A Google Térképen a csemperétegek `google.maps.ImageMapType` az osztály használatával hozhatók létre.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Térkép csemperéteg](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Utána: Azure Maps**

A többi réteghez hasonlóan hozzáadhat egy mozaikréteget a térképhez. X, y, zoom helyőrzők formátumú URL-címet használjon; `{x}`, `{y}` `{z}` , hogy megmondja a rétegnek, hogy hol érheti el a csempéket. Az Azure Maps `{quadkey}`csemperétegek támogatják a , `{bbox-epsg-3857}`és `{subdomain}` helyőrzőket is.

> [!TIP]
> Az Azure Maps-rétegek könnyen renderelhető alatt más rétegek, beleértve az alap térképrétegek. Gyakran kívánatos, hogy a csemperétegek a térképcímkék alatt legyenek, hogy könnyen olvashatók legyenek. A `map.layers.add` módszer egy második paramétert vesz fel, amely annak a rétegnek az azonosítója, amelybe az új réteget az alá kell beszúrni. Ha mozaikréteget szeretne beszúrni a térképfeliratok alá, használja ezt a kódot:`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Az Azure Maps csemperétege](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Csempe kérelmeket lehet rögzíteni `transformRequest` a lehetőséget a térképen. Ez lehetővé teszi, hogy szükség esetén módosítsa vagy hozzáadja a fejléceket a kéréshez.

**További források:**

- [Csemperétegek hozzáadása](map-add-tile-layer.md)
- [Mozaikréteg-osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Mozaikréteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Forgalom megjelenítése

A forgalmi adatok az Azure és a Google Térkép között is eltitakadhatók.

**Előtte: Google Térkép**

Fedd le a forgalmi adatokat a térképen a forgalmi réteg használatával.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Térkép forgalom](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Utána: Azure Maps**

Az Azure Maps számos különböző lehetőséget biztosít a forgalom megjelenítéséhez. A forgalmi eseményeket, például az útlezárásokat és a baleseteket ikonként jelenítheti meg a térképen. Fedd le a forgalom áramlását és a színkódolt utakat a térképen. A színek a feladott sebességhatár alapján módosíthatók a normál várható késleltetéshez vagy az abszolút késleltetéshez viszonyítva. Az Azure Maps incidensadatai percenként frissülnek, és kétpercenként frissítik az adatfolyam-frissítéseket.

A kívánt értékek `setTraffic` hozzárendelése a beállításokhoz.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Az Azure Maps forgalma](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Ha az Azure Maps egyik forgalmi ikonjára kattint, további információk jelennek meg egy felugró ablakban.

<center>

![Az Azure Maps forgalmi incidense](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**További források:**

- [Forgalom megjelenítése a térképen](map-show-traffic.md)
- [Forgalmi átfedési beállítások](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Földalátét hozzáadása

Az Azure és a Google maps is támogatja a földrajzihivatkozással készített képek térképen való átfedését. A georeferencia-alapú képek a térkép pásztázása és nagyítása után mozognak és méreteződnek. A Google Térképen a georeferencia-alapú képeket földalapú átfedéseknek nevezzük, míg az Azure Mapsben képrétegeknek nevezzük őket. Nagyszerűek az alaprajzok építéséhez, a régi térképek lefedéséhez vagy egy drón képeihez.

**Előtte: Google Térkép**

Adja meg az átfedésben tartani kívánt kép URL-címét, és egy határolókeretet a térképen lévő kép kötéséhez. Ez a példa egy [1922-es Newark New Jersey-i](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) térképképet fed le a térképen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Ha ezt a kódot böngészőben futtatja, a következő höz hasonló térkép jelenik meg:

<center>

![Google Térkép képátfedés](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Utána: Azure Maps**

Az `atlas.layer.ImageLayer` osztály segítségével fedje át a georeferencia-alapú képeket. Ehhez az osztályhoz egy kép URL-címe és a kép négy sarkának koordinátáira van szükség. A lemezképet vagy ugyanazon a tartományon kell üzemeltetni, vagy engedélyezni kell az RB-t.

> [!TIP]
> Ha csak észak, dél, kelet, nyugat és forgatási adatok vannak, és nincsenek koordinátái a kép minden sarkához, használhatja a statikus [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) módszert.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps képátfedés](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**További források:**

- [Kép átfedése](map-add-image-layer.md)
- [Képréteg osztálya](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="add-kml-to-the-map"></a>KML hozzáadása a térképhez

Az Azure és a Google Maps egyaránt importálhat és renderelhet KML-, KMZ- és GeoRSS-adatokat a térképen. Az Azure Maps támogatja a GPX, a GML, a térbeli CSV-fájlok, a GeoJSON, a Jól ismert szöveg (WKT), a webleképezési szolgáltatások (WMS), a webes leképezési csempeszolgáltatások (WMTS) és a Web Feature Services (WFS) szolgáltatást is. Az Azure Maps helyileg beolvassa a fájlokat a memóriába, és a legtöbb esetben sokkal nagyobb KML-fájlok kezelésére képes. 

**Előtte: Google Térkép**


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Ha ezt a kódot böngészőben futtatja, a következő höz hasonló térkép jelenik meg:

<center>

![Google Térkép képátfedés](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**Utána: Azure Maps**

Az Azure Mapsben a GeoJSON a webes SDK-ban használt fő adatformátum, a további térbeli adatformátumok könnyen integrálhatók a [térbeli IO-modul](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)használatával. Ez a modul funkciókat mind az olvasás és írás térbeli adatok és tartalmaz egy egyszerű adatréteg, amely könnyen teszi az adatokat bármelyik ilyen térbeli adatformátumok. Az adatok olvasásához egy térbeli adatfájlban egyszerűen adja át egy URL-címet, vagy nyers adatokat, mint karakterlánc vagy blob a `atlas.io.read` függvénybe. Ez visszaadja az összes olyan elemzési adatot a fájlból, amelyet ezután hozzáadhat a térképhez. A KML egy kicsit összetettebb, mint a legtöbb térbeli adatformátum, mivel sokkal több stílusinformációt tartalmaz. Az `SpatialDataLayer` osztály támogatja a stílusok többségének megjelenítését, azonban az ikonok képeit be kell tölteni a térképre a jellemzőadatok betöltése előtt, és a földalátéteket külön-külön kell rétegként hozzáadni a térképhez. URL-címen keresztül történő adatbetöltéskor azt egy CORs-kompatibilis végponton kell üzemeltetni, vagy egy proxyszolgáltatást kell átadni az olvasási függvénybe. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
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
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps képátfedés](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**További források:**

- [atlas.io.read függvény](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io?view=azure-maps-typescript-latest#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>További kódminták

A Google Térkép áttelepítésével kapcsolatos további kódminták:

- [Rajzeszközök](map-add-drawing-toolbar.md)
- [A térkép korlátozása kétujjas pásztázásra](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Görgetőkerék nagyításának korlátozása](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Teljes képernyős vezérlő létrehozása](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Szolgáltatások:**

- [Az Azure Maps-szolgáltatások modul használata](how-to-use-services-module.md)
- [Hasznos helyek keresése](map-search-location.md)
- [Információk bekéselése koordinátából (fordított geokód)](map-get-information-from-coordinate.md)
- [Útvonal megjelenítése A-ból B-be](map-route.md)
- [Automatikus javaslat keresése a JQuery felhasználói felületével](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>A Google Maps V3 és az Azure Maps Web SDK-osztály leképezése

A következő függelék a Google Térkép V3 általánosan használt osztályainak és az ezzel egyenértékű Azure Maps Web SDK-nak a kereszthivatkozását tartalmazza.

### <a name="core-classes"></a>Alapvető osztályok

| Google Térkép   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Megjelenítése](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Lakosság](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. Előugró beállítások](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Pozíció](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. Kameraopciók](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Szolgáltatásbeállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Stílusbeállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Átfedési osztályok

| Google Térkép  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Pont](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Ikonopciók](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextOptions (Szövegbeállításai)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions (Buborékréteg opciók)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Sokszög](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. Sokszögréteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Lásd: [Kör hozzáadása a térképhez](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. Mozaikréteg](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Szolgáltatási osztályok

Az Azure Maps Web SDK tartalmaz egy szolgáltatási modult, amely külön tölthető be. Ez a modul az Azure Maps REST-szolgáltatásokat webes API-val burkolja, és JavaScript-, TypeScript- és Node.js-alkalmazásokban is használható.

| Google Térkép | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.szolgáltatás.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. KeresésNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions (Keresési POI-beállítások)](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.szolgáltatás.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.szolgáltatás.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Kódtárak

A könyvtárak további funkciókat adnak a térképhez. Ezek közül a könyvtárak közül sok az Azure Maps alapvető SDK-jában található. Íme néhány egyenértékű osztály használata a Google Térkép-könyvtárak helyett

| Google Térkép           | Azure Maps   |
|-----------------------|--------------|
| Rajztár       | [Rajzeszközök modul](set-drawing-options.md) |
| Geometriai könyvtár      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Képi megjelenítési könyvtár | [Hőtérkép-réteg](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>További lépések

További információ az Azure Maps Web SDK-ról.

> [!div class="nextstepaction"]
> [A térképvezérlő használata](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [A szolgáltatási modul használata](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [A rajzeszközök modul használata](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

