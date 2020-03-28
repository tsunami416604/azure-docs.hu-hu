---
title: 'Oktatóanyag: Android-alkalmazás áttelepítése | Microsoft Azure Maps'
description: Android-alkalmazás áttelepítése a Google Térképről a Microsoft Azure Maps szolgáltatásba.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9514398ec6a84becd1283e4b0975804101b64086
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209732"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Android-alkalmazás áttelepítése a Google Térképről

Az Azure Maps Android SDK rendelkezik egy API-felület, amely hasonlít a webes SDK. Ha ezen SDK-k egyikével fejlesztett ki, számos ugyanazokat a fogalmakat, ajánlott eljárásokat és architektúrákat alkalmazza.

Az Azure Maps Android SDK támogatja az API 21 minimális Android-verzióját: Android 5.0.0 (Lollipop).

Minden példa java nyelven van megadva; azonban használhatja a Kotlin az Azure Maps Android SDK.

Az Azure Maps Android SDK-val való fejlesztéséről az [Azure Maps Android SDK útmutatói](how-to-use-android-map-control-library.md)című témakörben talál további információt.

## <a name="load-a-map"></a>Térkép betöltése

A google vagy az Azure Maps használatával androidos alkalmazásokban betöltegy térképet, hasonló lépésekből áll. Az SDK használatakor a következőket kell tennie:

- Szerezzen be egy API-t vagy előfizetési kulcsot bármelyik platform eléréséhez.
- Adjon hozzá egy XML-t egy tevékenységhez, hogy megadja, hol és hogyan kell megjeleníteni a térképet, és hogyan kell elhelyezni.
- A térképnézetet tartalmazó tevékenység összes életciklus-metódusának felülbírálása a térképosztály megfelelő metódusaihoz. Különösen a következő módszereket kell felülírnia:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Várja meg, amíg a térkép készen áll, mielőtt megpróbálna hozzáférni és programozni.

**Előtte: Google Térkép**

Ha térképet szeretne megjeleníteni az Android-alapú Google Maps SDK használatával, a következő lépések rekednek:

1.  Győződjön meg arról, hogy a Google Play-szolgáltatások telepítve vannak.
2.  A Google Térkép szolgáltatás függőségének hozzáadása a modul **gradle.build** fájljához: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Adjon hozzá egy Google Maps API-kulcsot a **Google\_Maps\_api.xml** fájl alkalmazásszakaszában:
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Térképtöredék hozzáadása a fő tevékenységhez:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  A **MainActivity.java** fájlban importálnia kell a Google Térkép SDK-t. Továbbítsa az összes életciklus-módszert a térképnézetet tartalmazó tevékenységből a térképosztály megfelelő imasmára. Egy `MapView` példány lekérése a `getMapAsync(OnMapReadyCallback)` térképtöredékből a módszerrel. Az `MapView` automatikusan inicializálja a térképrendszert és a nézetet. A **MainActivity.java** fájl szerkesztése az alábbiak szerint:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Alkalmazás futtatásakor a térképvezérlő az alábbi képen látható módon töltődik be.

<center>

![Egyszerű Google Térkép](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Utána: Azure Maps**

Ha az Android-alapú Azure Maps SDK használatával szeretne térképet megjeleníteni, a következő lépéseket kell elvégeznie:

1. Nyissa meg a legfelső szintű **build.gradle** fájlt, és adja hozzá a következő kódot az **összes projektblokk** szakaszhoz:

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítse az **alkalmazást/build.gradle-t,** és adja hozzá a következő kódot:
    
    1. Győződjön meg arról, hogy a projekt **minSdkVersion** API 21-es vagy újabb verziójú.

    2. Adja hozzá a következő kódot az Android szakaszhoz:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Frissítse a függőségi blokkot. Új implementációs függőségi vonal hozzáadása a legújabb Azure Maps Android SDK-hoz:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Az Azure Maps Android SDK rendszeresen frissül és továbbfejlesztett. Az Android [térképvezérlés sel](how-to-use-android-map-control-library.md) való ismerkedés a legújabb Azure Maps verziószám ának beszerzése érdekében. A verziószámot "0.2"-ről "0+"-ra is beállíthatja, hogy a kód mindig a legújabb verzióra mutasson.
    
    4. Nyissa meg az eszköztár **Fájl fájlját,** majd kattintson a **Projekt szinkronizálása gradle fájlokkal parancsra.**
3. Térképtöredék hozzáadása a fő \> \> tevékenységhez (erőforrás-elrendezési tevékenység\_main.xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. A **MainActivity.java** fájlban a következőkre van szükség:
    
    * Az Azure Maps SDK importálása
    * Az Azure Maps hitelesítési adatainak beállítása
    * A térképvezérlő példány beszereznie az **onCreate** metódusban

     Állítsa be a `AzureMaps` hitelesítési `setSubscriptionKey` adatokat az osztályban a vagy `setAadProperties` metódusokkal. Ez a globális frissítés biztosítja, hogy a hitelesítési adatokat minden nézethez hozzáadja.

    A térképvezérlő saját életciklus-módszereket tartalmaz az Android OpenGL életciklusának kezelésére. Ezeket a módszereket közvetlenül a tartalmazott tevékenységből kell meghívni. A térképvezérlő életciklus-metódusai helyes hívásához felül kell bírálnia a következő életciklus-metódusokat a térképvezérlőt tartalmazó tevékenységben. Hívja meg a megfelelő térképvezérlési módszert.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    A **MainActivity.java** fájl szerkesztése az alábbiak szerint:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Ha futtatja az alkalmazást, a térképvezérlő az alábbi képen látható módon töltődik be.

<center>

![Egyszerű Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Figyelje meg, hogy az Azure Maps vezérlő támogatja a nagyobb kicsinyítést, és több világnézetet biztosít.

> [!TIP]
> Ha Android-emulátort használ Windows rendszerű számítógépen, előfordulhat, hogy a térkép nem jelenik meg az OpenGL-lel és a szoftveresen gyorsított grafikus rendereléssel való ütközések miatt. A következő néhány ember számára működött a probléma megoldása. Nyissa meg az AVD-kezelőt, és válassza ki a szerkesztandó virtuális eszközt. Görgessen lefelé a **Konfiguráció ellenőrzése** panelen. Az **Emulált teljesítmény** csoportban állítsa a **Grafikus** beállítást **Hardver**re.

## <a name="localizing-the-map"></a>A térkép honosítása

A lokalizáció akkor fontos, ha a célközönséged több országban is elterjedt, vagy különböző nyelveket beszélsz.

**Előtte: Google Térkép**

Adja hozzá a `onCreate` következő kódot a metódushoz a térkép nyelvének beállításához. A kódot hozzá kell adni a térkép környezeti nézetének beállítása előtt. Az "fr" nyelvkód a nyelvet a franciára korlátozza.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Íme egy példa a Google Maps a nyelv beállítása "fr".

<center>

![Google Térkép honosítása](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Utána: Azure Maps**

Az Azure Maps három különböző módon állítja be a nyelvet és a térkép regionális nézetét. Az első lehetőség az, hogy átadja a `AzureMaps` nyelvi és regionális nézet információkat az osztálynak. Ez a beállítás `setLanguage` `setView` a statikus és metódusok globálisan. Ami azt jelenti, hogy az alapértelmezett nyelv és a regionális nézet az alkalmazásba betöltött összes Azure Maps-vezérlőben van beállítva. Ez a példa a francia nyelvet állítja be az "fr-FR" nyelvkód használatával.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A második lehetőség a nyelv és az információk megtekintése a térképvezérlő XML-kódjának.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A harmadik lehetőség a nyelvi és regionális térképnézet programozása a térképek `setStyle` módszerrel. Ez a beállítás a kód végrehajtásakor bármikor frissíti a nyelvi és területi nézetet.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Íme egy példa az Azure Maps a nyelv beállítása "fr-FR".

<center>

![Az Azure Maps honosítása](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Tekintse át a [támogatott nyelvek](supported-languages.md)teljes listáját.

## <a name="setting-the-map-view"></a>A térképnézet beállítása

Az Azure Maps és a Google Térkép dinamikus térképei programozott módon áthelyezhetők új földrajzi helyekre a megfelelő módszerek felhívásával. Tegyük a térképet műholdas légi felvételek megjelenítésére, középre a térképet egy koordinátákat mutató helyre, és változtassuk meg a nagyítási szintet. Ebben a példában a szélességi fokot használjuk: 35.0272, hosszúság: -111.0225, és 15-ös nagyítási szint.

**Előtte: Google Térkép**

A Google Térkép térképvezérlő kamerája programozott `moveCamera` módon mozgatható a módszerrel. A `moveCamera` módszer lehetővé teszi a térkép középpontjának és a nagyítási szintnek a megadását. A `setMapType` módszer a térkép típusát megjelenítésre módosítja.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Térkép készlet nézet](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> A Google Térkép 256 képpont méretű csempéket használ, míg az Azure Maps nagyobb, 512 képpontméretű csempéket. Ez csökkenti az Azure Maps által a Google Térképhez hasonló térképterület betöltéséhez szükséges hálózati kérelmek számát. Ahhoz, hogy ugyanazt a látható területet érje el, mint egy térkép a Google Térképen, ki kell vonnia a Google Térképben használt nagyítási szintet e-vel az Azure Maps használatakor. 

**Utána: Azure Maps**

Ahogy azt korábban említettük, az Azure Maps azonos látható területének eléréséhez vonja ki a Google Térképben használt nagyítási szintet eggyel. Ebben az esetben 14-es nagyítási szintet használjon.

A kezdeti térképnézet XML-attribútumokban állítható be a térképvezérlőben.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

A térképnézet a térképek `setCamera` és `setStyle` módszerek segítségével programozható.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Az Azure Maps készletnézete](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**További források:**

- [Támogatott térképstílusok](supported-map-styles.md)

## <a name="adding-a-marker"></a>Jelölő hozzáadása

A pontadatok gyakran a térképen lévő kép használatával jelennek meg. Ezeket a képeket jelölőknek, tollkitűzőknek, tűknek vagy szimbólumoknak nevezzük. A következő példák a pontadatokat jelölőkként jelenítik meg a térképen a szélességi fokon: 51.5, hosszúság: -0.2.

**Előtte: Google Térkép**

A Google Térkép segítségével a jelölők a térképek `addMarker` módszerrel kerülnek hozzáadásra.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Térkép jelölő](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Utána: Azure Maps**

Az Azure Mapsben renderelheti a pontadatokat a térképen, először adja hozzá az adatokat egy adatforráshoz. Ezután az adatforrást egy szimbólumréteghez csatlakoztatja. Az adatforrás optimalizálja a térbeli adatok kezelését a térképvezérlőben. A szimbólumréteg határozza meg, hogyan jeleníthető meg a pontadatok képként vagy szövegként.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Az Azure Maps jelölője](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Egyéni jelölő hozzáadása

Az egyéni képek a térkép pontjainak ábrázolására használhatók. Az alábbi példákban látható térkép egy egyéni képet használ egy pont megjelenítéséhez a térképen. A pont az 51.5 szélességi fokon és a hosszúsági fokon van: -0,2. A horgony eltolja a jelölő pozícióját, így a nyomótű ikonjának pontja igazodik a térképen a megfelelő pozícióhoz.

<center>

![sárga nyomótű képe](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

Mindkét példában a fenti kép hozzáadódik az alkalmazások erőforrásainak rajzolt mappájához.

**Előtte: Google Térkép**

A Google Térkép segítségével egyéni képek használhatók a jelölőkhöz. Egyéni képek betöltése `icon` a jelölő beállításával. Ha a kép pontját a koordinátához szeretné igazítani, használja ezt a `anchor` beállítást. A horgony a kép méretéhez viszonyítva van. Ebben az esetben a horgony 0,2 egység széles, és 1 egység magas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![A Google Térkép egyéni jelölője](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Utána: Azure Maps**

Az Azure Maps ben lévő szimbólumrétegek támogatják az egyéni lemezképeket, de először a lemezképet be kell tölteni a térkép-erőforrásokba, és egyedi azonosítót kell hozzárendelni. Ezután a szimbólumrétegnek hivatkoznia kell erre az azonosítóra. A szimbólum eltolása a kép megfelelő `iconOffset` pontjához igazodva a beállítással. Az ikon eltolása képpontban van. Alapértelmezés szerint az eltolás a kép alsó középpontjához képest relatív, `iconAnchor` de ez az eltolási érték a beállítással módosítható. Ez a `iconAnchor` példa `"center"`a beállítását a parancsra állítja. Egy ikoneltolást használ a kép öt képpontjobbra, 15 képponttal feljebb mozgatásához, hogy igazodjon a tolótű képpontjához.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Az Azure Maps egyéni jelölője](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Vonallánc hozzáadása

A vonalláncok egy vonalat vagy görbét jelölnek a térképen. Az alábbi példák bemutatják, hogyan hozhat létre szaggatott vonalláncot a térképen.

**Előtte: Google Térkép**

A Google Térkép segítségével renderelje a vonalláncot az `PolylineOptions` osztály használatával. Adja hozzá a vonalláncot `addPolyline` a térképhez a módszerrel. A beállítással állítsa `color` be a körvonal színét. A beállítással állítsa `width` be a körvonal szélességét. A `pattern` beállítással vegyen fel körvonal-tömbtömböt.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Térkép vonallánc](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Utána: Azure Maps**

Az Azure Mapsben a `LineString` `MultiLineString` vonalláncok neve vagy objektumok. Adja hozzá ezeket az objektumokat egy adatforráshoz, és sorréteggel jelenítse meg őket. A beállítással állítsa `strokeWidth` be a körvonal szélességét. A `strokeDashArray` beállítással vegyen fel körvonal-tömbtömböt.

A körvonal szélessége és a dash array "pixel" egységek az Azure Maps Web SDK, ugyanaz, mint a Google Maps szolgáltatásban. Mindkettő ugyanazokat az értékeket fogadja el, hogy ugyanazt az eredményt hozza.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Az Azure Maps vonallánc](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Sokszög hozzáadása

A sokszögek a térkép egy területének ábrázolására szolgálnak. A következő példák bemutatják, hogyan hozhat létre sokszöget. Ez a sokszög háromszöget képez a térkép középső koordinátája alapján.

**Előtte: Google Térkép**

A Google Térkép segítségével renderelje `PolygonOptions` a sokszöget az osztály használatával. Adja hozzá a sokszöget `addPolygon` a térképhez a módszerrel. A kitöltési és `fillColor` körvonalszíneket a és `strokeColor` a beállításokkal állíthatja be. A beállítással állítsa `strokeWidth` be a körvonal szélességét.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google Térkép sokszög](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Utána: Azure Maps**

Az Azure Mapsben `MultiPolygon` hozzáadhat és objektumokat adhat hozzá `Polygon` és objektumokat egy adatforráshoz, és rétegek használatával renderelheti őket a térképen. Sokszög területének renderelése sokszögrétegben. Sokszög körvonalának renderelése vonalréteg használatával. Adja meg a körvonal `strokeColor` színét és szélességét a és `strokeWidth` a beállításokkal.

Az Azure Maps Web SDK-ban a körvonal szélessége és a kötőjeltömb "képpont" egységei igazodnak a Google Térkép megfelelő egységeihez. Mindkettő ugyanazokat az értékeket fogadja el, és ugyanazt az eredményt adja.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure Maps sokszög](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Mozaikréteg átfedése

 A Mozaikrétegek segítségével átfedheti a kisebb mozaikképekre bontott rétegképeket, amelyek igazodnak a térképmozaik-rendszerhez. Ez a megközelítés a rétegképek vagy nagy adatkészletek átfedésének gyakori módja. A mozaikrétegeket képátfedéseknek nevezzük a Google Térképen.

A következő példák egy időjárási radarcsempe-réteget fednek le az Iowa Imkeresztüli Állami Egyetem Iowa Environmental Mesonet-jéből. A csempék mérete 256 képpont.

**Előtte: Google Térkép**

A Google Térkép segítségével a mozaikréteg a térkép tetejére rakható. Használd `TileOverlayOptions` az osztályt. Adja hozzá a csemperéteget `addTileLauer` a térképhez a módszerrel. Ahhoz, hogy a csempe `transparency` félig átlátszó, a beállítás beállítása 0,2, vagy 20%, átlátszó.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Google Térkép csemperéteg](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Utána: Azure Maps**

A mozaikréteg a többi réteghez hasonlóan hozzáadható a térképhez. Formázott URL, amelyx, y és zoom helyőrzővel rendelkezik; `{x}`, `{y}` `{z}` , , illetve arra szolgál, hogy megmondja a réteg, ahol a hozzáférést a csempe. Az Azure Maps csempérétegei is támogatják `{quadkey}`a , `{bbox-epsg-3857}`és `{subdomain}` helyőrzőket is. A mozaikréteg félig átlátszóvá tétele érdekében a függvény 0,8 opacitási értéket használ. Az opacitás és az átlátszóság, bár hasonló, fordított értékeket használ. A két beállítás közötti konvertáláshoz vonjuk ki az értéküket az egyes számból.

> [!TIP]
> Az Azure Mapsben célszerű más rétegek, köztük alaptérképrétegek alatti rétegeket megjeleníteni. Emellett gyakran kívánatos a csemperétegek megjelenítése a térképcímkék alatt, hogy azok könnyen olvashatók legyenek. A `map.layers.add` módszer egy második paramétert vesz igénybe, amely annak a rétegnek az azonosítója, amelybe az új réteget az alá kell szúrni. Ha mozaikréteget szeretne beszúrni a térképfeliratok alá, a következő kód használható:`map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Az Azure Maps csemperétege](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Forgalom megjelenítése

Mind az Azure Maps, mind a Google Maps rendelkezik forgalmi adatok átfedésével.

**Előtte: Google Térkép**

A Google Térkép segítségével a forgalmi adatok a térkép tetejére rakhatók, ha igaz, hogy a térkép módszerét `setTrafficEnabled` átadják.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Térkép forgalom](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Utána: Azure Maps**

Az Azure Maps számos különböző lehetőséget biztosít a forgalom megjelenítéséhez. A közlekedési események, például az útlezárások és a balesetek ikonként jeleníthetők meg a térképen. A forgalmi áramlás és a színkódolt utak eltussolhatók a térképen. A színek módosíthatók úgy, hogy a feladott sebességkorláthoz képest a normál várható késleltetéshez vagy az abszolút késleltetéshez viszonyítva jelenjenek meg. Az Azure Maps ben az incidensadatok percenként frissülnek, és a folyamatadatok kétpercenként frissülnek.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Az Azure Maps forgalma](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>További lépések

További információ az Azure Maps Android SDK-ról.

> [!div class="nextstepaction"]
> [Az Android térképvezérlő használata](how-to-use-android-map-control-library.md)
