---
title: Android-alkalmazás migrálása | Microsoft Docs
description: Egy Android-alkalmazás Google Mapsből Microsoft Azure Maps-be történő áttelepítésével kapcsolatos oktatóanyag.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481398"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Android-alkalmazás migrálása a Google Mapsből

A Azure Maps Android SDK olyan API-felületet tartalmaz, amely nagyon hasonlít a web SDK-hoz. Ha ezekkel az SDK-k valamelyikével fejlesztett ki, akkor az ajánlott eljárások és architektúrák közül sokat alkalmaz, és könnyen át tudja vinni a tudását a másikra.

A Azure Maps Android SDK az API 21 minimális Android-verzióját támogatja: Android 5.0.0 (nyalóka).

A Java-ban megadott összes példa, de a Kotlin is használható az Azure Maps Android SDK-val.

Az SDK-val való fejlesztéssel kapcsolatos további információkért tekintse meg a [Azure Maps Android SDK útmutatóit](how-to-use-android-map-control-library.md) is.

## <a name="load-a-map"></a>Térkép betöltése

Az Android-alkalmazásokban a Google vagy a Azure Maps használatával betöltheti a térképet, és számos különböző lépésből áll. Az SDK használatakor a következőkre van szükség:

- Egy API-vagy előfizetési kulcs beszerzése a platformhoz való hozzáféréshez.
- Adjon hozzá néhány XML-t egy tevékenységhez, hogy meghatározza, hol kell megjeleníteni a térképet, és hogy hogyan kell meghatározni.
- Továbbítsa az összes életciklus-metódust a Térkép nézetet tartalmazó tevékenységből a Map osztály megfelelő értékeire. Különösen a következő módszerekkel kell továbbítania:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Várjon, amíg a Térkép készen áll ahhoz, hogy programozott módon hozzáférhessen.

**Előtte: Google Maps**

Ha az Androidhoz készült Google Maps SDK-val szeretne térképet megjeleníteni, a következő lépéseket kell végrehajtania:

1.  Győződjön meg arról, hogy a Google Play-szolgáltatások telepítve vannak.
2.  Vegyen fel egy függőséget a Google Maps szolgáltatáshoz a modul **gradle. Build** fájljához: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Vegyen fel egy Google Maps API-kulcsot a **google\_Maps\_API. XML** fájljának alkalmazás szakaszában:
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Térképi töredék hozzáadása a fő tevékenységhez:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  A **MainActivity. Java** fájlban fel kell vennie a Google Maps SDK importálásait. Továbbítsa az összes életciklus-metódust a Térkép nézetet tartalmazó tevékenységből a Map osztály megfelelő értékeire. `MapView` példány a térképi töredékből a `getMapAsync(OnMapReadyCallback)` metódus használatával kérhető le. A `MapView` automatikusan inicializálja a térképek rendszerét és a nézetet. Szerkessze a **MainActivity. Java** fájlt a következőképpen:

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

Ha egy alkalmazásban futott, a Térkép vezérlőelem a következőképpen fog betöltődik.

<center>

![egyszerű Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Utána: Azure Maps**

Ha az Androidhoz készült Azure Maps SDK-val szeretne térképet megjeleníteni, a következő lépéseket kell elvégeznie:

1. Nyissa meg a legfelső szintű **Build. gradle** fájlt, és adja hozzá a következő kódot a **minden projekt**, **adattárak** blokkolása szakaszhoz:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítse az **alkalmazást/Build. gradle** , és adja hozzá a következő kódot:
    
    1. Győződjön meg arról, hogy a projekt **minSdkVersion** értéke 21 vagy újabb.

    2. Adja hozzá a következő kódot az Android szakaszhoz:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Frissítse a függőségek blokkot, és adjon hozzá egy új implementációs függőségi sort a legújabb Azure Maps Android SDK-hoz:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > A Azure Maps Android SDK-t rendszeresen frissítjük és bővítettük. A legújabb Azure Maps implementáció verziószámának beszerzéséhez tekintse meg a [Bevezetés az Android Map Control](how-to-use-android-map-control-library.md) dokumentációját. Azt is megteheti, hogy a verziószámot "0,2" értékről "0 +" értékre állítja, hogy mindig a legújabb verzióra mutasson.
    
    4. Nyissa meg a **fájlt** az eszköztáron, majd kattintson a **szinkronizálás projekt Gradle-fájlokkal**elemre.
3. Térképi töredék hozzáadása a fő tevékenységhez (res \> elrendezés \> tevékenység\_Main. xml):
    
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

4. A **MainActivity. Java** fájlban a következőkre lesz szüksége:
    
    * importálások hozzáadása az Azure Maps SDK-hoz
    * a Azure Maps hitelesítési adatainak beállítása
    * a Térkép vezérlőelem példányának beolvasása a **onCreate** metódusban

    Ha a hitelesítési adatokat a `AzureMaps` osztályban globálisan a `setSubscriptionKey` vagy a `setAadProperties` módszer használatával állítja be, akkor a hitelesítési adatokat nem kell minden nézetben felvennie. 

    A Térkép vezérlőelem saját életciklus-metódusokat tartalmaz az Android OpenGL-életciklusának kezeléséhez, amelyet közvetlenül a tartalmazó tevékenységből kell meghívni. Ahhoz, hogy az alkalmazás megfelelően működjön, hívja meg a Map Control életciklusának módszereit, a Térkép vezérlőelemet tartalmazó tevékenységen felül kell bírálnia a következő életciklus-metódusokat, és meg kell hívnia a megfelelő leképezés-vezérlési módszert. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Szerkessze a **MainActivity. Java** fájlt a következőképpen:
    
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

Ha futtatja az alkalmazást, a Térkép vezérlőelem a következőképpen lesz betöltve.

<center>

Egyszerű Azure Maps ![](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Figyelje meg, hogy a Azure Maps-vezérlő támogatja a nagyítást, és több globális nézetet biztosít.

> [!TIP]
> Ha Android-emulátort használ a Windowsban, előfordulhat, hogy a Térkép nem jelenik meg az OpenGL-vel és a szoftveresen felgyorsított grafikus megjelenítéssel való ütközések miatt. A probléma megoldásához a következő működött. Nyissa meg a AVD-kezelőt, és válassza ki a szerkeszteni kívánt virtuális eszközt. Az **emulált teljesítmény** szakaszban állítsa a **grafikus** beállítást a **hardver**elemre.

## <a name="localizing-the-map"></a>A Térkép honosítása

Ha a célközönség több ország között oszlik meg, vagy különböző nyelveket beszél, a honosítás fontos.

**Előtte: Google Maps**

A Térkép nyelve a fő tevékenység `onCreate` metódusában állítható be a Térkép környezeti nézetének beállítása előtt. A következő nyelvi kóddal korlátozza a nyelvet francia nyelvre: "FR".

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Itt látható egy példa arra, hogy a Google Maps a "FR" nyelvre van állítva.

<center>

![Google Maps honosítás](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Utána: Azure Maps**

A Azure Maps három különböző módszert biztosít a Térkép nyelvének és regionális nézetének beállításához. Az első lehetőség a nyelv és a regionális nézet információinak átadása a `AzureMaps` osztályba a statikus `setLanguage` és a `setView` módszerek globális használatával. Ezzel a beállítással megadhatja az alkalmazásban betöltött összes Azure Maps vezérlő alapértelmezett nyelvi és regionális nézetét. A következő nyelvi kód a "fr-FR" nyelvkód alapján korlátozza a nyelvet franciára.

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

A második lehetőség, hogy átadja a nyelvet, és megtekinti az információkat a Térkép vezérlőelem XML-kódjában.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A harmadik lehetőség az, hogy programozott módon állítsa be a Térkép nyelvét és regionális nézetét a Maps `setStyle` metódus használatával. Ezt bármikor megteheti a Térkép nyelvének és regionális nézetének módosításához.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Itt látható egy példa arra, hogy Azure Maps a "fr-FR" nyelvre beállított nyelvet.

<center>

![Azure Maps honosítás](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="setting-the-map-view"></a>A Térkép nézet beállítása

Az Azure-ban és a Google Maps-ben elérhető dinamikus leképezések programozott módon áthelyezhetők az új földrajzi helyekre a megfelelő módszerek meghívásával. Az alábbi példák azt mutatják be, hogyan lehet a Térkép műhold antennát megjeleníteni, középre állítani a térképet koordinátákkal (szélesség: 35,0272, hosszúság:-111,0225), és a Google Maps-ben a nagyítási szint 15 értékre változik.

> [!NOTE]
> A Google Maps a méretekben 256 képpont méretű csempéket használ, míg Azure Maps nagyobb 512 képpont csempét használ. Ez csökkenti a Azure Maps által igényelt hálózati kérések számát, ha a Google Maps szolgáltatással ugyanazt a térképi területet szeretné betölteni. Ahhoz azonban, hogy a csempe-piramisok hogyan működnek a Térkép vezérlőelemekben, a Azure Maps nagyobb csempéje azt jelenti, hogy ugyanazt a megtekinthető terület a Google Maps-ben is elérhetővé teszi, a Google Maps-ben használt nagyítási szint kivonásával a Azure Maps használatakor.

**Előtte: Google Maps**

A Google Maps Map vezérlő kamerája programozott módon áthelyezhető a `moveCamera` metódussal, amely lehetővé teszi a Térkép középpontjának és a nagyítási szint megadását. A `setMapType` metódussal lehet megváltoztatni a megjelenített Térkép típusát.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps-készlet nézet](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Utána: Azure Maps**

Amint azt korábban említettük, a Azure Maps azonos megtekinthető területének eléréséhez kivonja a Google Maps-ben használt nagyítási szintet, ebben az esetben pedig a 14. nagyítási szintet használja.

A Térkép kezdeti nézete beállítható a Térkép vezérlőelem XML-attribútumaiban.

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

A Térkép nézet a Maps `setCamera` és az `setStyle` metódusok használatával is frissíthető.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps beállított nézet](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**További források:**

- [Támogatott Térkép stílusa](supported-map-styles.md)

## <a name="adding-a-marker"></a>Jelölő hozzáadása

A térképre mutató képek használatával a pont adatai gyakran a térképen jelennek meg. Ezeket a képeket gyakran jelölőknek, pushpins, PIN-kódoknak vagy szimbólumoknak nevezzük. A következő példák a leképezési pontok adatmegjelenítését jelzik a térképen (szélesség: 51,5, hosszúság:-0,2).

**Előtte: Google Maps**

A Google Maps használatával a jelölők a Maps `addMarker` metódussal vehetők fel.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps-jelölő](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Utána: Azure Maps**

Azure Maps pontban az adatpontok a térképen is megtekinthetők, ha először hozzáadja az adatforráshoz, majd az adatforrást egy szimbólum réteghez csatlakoztatja. Az adatforrás optimalizálja a térbeli adatok kezelését a Térkép vezérlőelemben, és a szimbólum réteg megadja, hogyan kell megjeleníteni a pontok információit képként és/vagy szövegként.

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

![Azure Maps jelölő](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Egyéni jelölő hozzáadása

Az egyéni rendszerképeket egy térképen ábrázoló pontok ábrázolására lehet használni. Az alábbi ábrán egy egyéni rendszerkép jelenik meg, amely egy pontot jelenít meg a térképen a következő helyen: (szélesség: 51,5, hosszúság:-0,2), és kiegyenlíti a jelölő pozícióját, hogy a gombostű ikon a Térkép helyes helyére legyen igazítva.

<center>

![sárga gombostű-rendszerkép](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_gombostű. png</center>

A fenti képen mindkét példában a rendszer hozzáadja az alkalmazások erőforrásainak megrajzolható mappájához.

**Előtte: Google Maps**

A Google Maps segítségével egyéni rendszerképeket használhat a jelölők számára, ha betölti őket a jelölő `icon` lehetőségével. Ha a kép pontját a koordináta felé szeretné igazítani, akkor a `anchor` lehetőség használható. A horgony a rendszerkép méreteihez képest, ebben az esetben a 0,2 egység széles és 1 egység magas.

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

![Google Maps egyéni jelölő](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Utána: Azure Maps**

A Azure Maps az egyéni rendszerképeket is támogatja, de a lemezképet először be kell tölteni a Térkép erőforrásaiba, és hozzá kell rendelni egy egyedi azonosítót. A szimbólum réteg ezután hivatkozhat erre az AZONOSÍTÓra. A szimbólum eltolással lehet a képen a megfelelő pontra igazítani a `iconOffset` kapcsoló használatával. Vegye figyelembe, hogy az ikon eltolása képpontban megadva. Alapértelmezés szerint az eltolás a rendszerkép alsó középpontjához képest relatív értékre van állítva, de ez a `iconAnchor` kapcsolóval módosítható. Ez a példa a `iconAnchor` beállítást állítja be, hogy `"center"`, és egy ikon eltolásával helyezze át a képet öt képpontot jobbra, a 15 képpontot pedig a gombostű-rendszerkép pontjához igazítva.

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

![Azure Maps egyéni jelölő](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Vonallánc hozzáadása

A vonalláncok egy vonal vagy elérési út ábrázolására szolgálnak a térképen. Az alábbi példák bemutatják, hogyan hozhat létre szaggatott vonalláncot a térképen.

**Előtte: Google Maps**

A Google Maps segítségével létrehozhat egy vonalláncot a `PolylineOptions` osztály használatával, és hozzáadhatja a térképhez a `addPolyline` metódus használatával. Az ecsetvonás színe a `color` kapcsolóval állítható be, a körvonal szélessége a szélesség beállítással állítható be, a stroke Dash tömb pedig beállítható a `pattern` kapcsoló használatával.

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

![Google Maps-vonallánc](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Utána: Azure Maps**

Azure Maps a vonalláncok neve LineString vagy MultiLineString objektum. Ezek az objektumok hozzáadhatók egy adatforráshoz, és egy sor réteg használatával is megjeleníthető. Vegye figyelembe, hogy a stroke width és a Dash Array "pixel" egységei a Azure Maps web SDK-ban vannak igazítva, amely ugyanazokat az értékeket használja mindkét SDK-ban, ugyanazokat az eredményeket használják.

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

![Azure Maps vonallánc](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Sokszög hozzáadása

A sokszögek egy területnek a térképen való ábrázolására szolgálnak. Az alábbi példák bemutatják, hogyan hozhat létre olyan sokszöget, amely a Térkép középpontjának koordinátái alapján alkotja a háromszöget.

**Előtte: Google Maps**

A Google Maps segítségével egy sokszög hozható létre a `PolygonOptions` osztály használatával, és a `addPolygon` metódussal adható hozzá a térképhez. A kitöltés és a körvonal színei a `fillColor` és az `strokeColor` lehetőséggel állíthatók be, a körvonal szélessége a `strokeWidth` kapcsolóval állítható be.

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

![Google Maps-sokszög](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Utána: Azure Maps**

Azure Maps a sokszög és a többsokszögű objektumok hozzáadhatók egy adatforráshoz, és a térképen a rétegek használatával is megjeleníthető. A sokszög területe egy sokszög rétegben jeleníthető meg. A sokszög körvonalait egy vonal réteggel lehet megjeleníteni. Vegye figyelembe, hogy a stroke width és a Dash Array "pixel" egységei a Azure Maps web SDK-ban vannak igazítva, amely ugyanazokat az értékeket használja mindkét SDK-ban, ugyanazokat az eredményeket használják.

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

## <a name="overlay-a-tile-layer"></a>Csempe rétegének átfedése

A csempék, más néven képátfedések a Google Maps-ben lehetővé teszik, hogy olyan rétegbeli képeket illesszen be, amelyek a Maps csempe rendszerhez igazított kisebb csempés képekre vannak bontva. Ez egy gyakori módszer a rétegek vagy nagyon nagy adathalmazok átfedésére.

Az alábbi példák egy időjárási radar csempe réteget fedik fel az Iowa State University Iowa környezeti Mesonet. A csempék mérete 256 képpont.

**Előtte: Google Maps**

A Google Maps segítségével egy csempe réteg helyezhető a Térkép tetejére, és a `TileOverlayOptions` osztályt használja, és a `addTileLauer` metódussal adja hozzá a térképhez. A csempék félig áttetszővé tételéhez a `transparency` beállítás értéke 0,2, vagy 20% átlátszó.

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

![Google Maps csempe réteg](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Utána: Azure Maps**

Azure Maps a csempék rétegét ugyanúgy lehet a térképhez adni, mint bármely más rétegben. Olyan formázott URL-cím, amely x, y és nagyítási helyőrzőket tartalmaz; a `{x}`, `{y}`, `{z}` vagy annak a rétegnek a használatával kell eldönteni, hogy hová fér hozzá a csempék. A rétegek Azure Maps a `{quadkey}`, a `{bbox-epsg-3857}` és a `{subdomain}` helyőrzőket is támogatják. A csempék félig áttetszővé tételéhez a rendszer 0,8-es opacitási értéket használ. Vegye figyelembe, hogy az opacitás és az átlátszóság, bár hasonló, fordított értékeket használjon. A közöttük való átalakítás egyszerűen kivonja az értéket az első számból.

> [!TIP]
> Azure Maps rétegekben könnyen megjeleníthető más rétegek alatt, beleértve az alapszintű leképezési rétegeket is. Gyakran érdemes a Térkép feliratai alatt megjeleníteni a csempe rétegeit, hogy azok könnyen olvashatók legyenek. A `map.layers.add` metódus egy második paramétert vesz igénybe, amely annak a rétegnek az azonosítója, amelyben az alábbi új réteget be kell szúrni. A Térkép felirat alatti csempe réteg beszúrásához a következő kód használható: `map.layers.add(myTileLayer, "labels");`

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

![Azure Maps csempe rétege](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Forgalom megjelenítése

A forgalmi adatok az Azure és a Google Maps is felhelyezhetők.

**Előtte: Google Maps**

A Google Maps szolgáltatásban a forgalmi flow-adatok a Térkép fölé helyezhetők, ha igaz értéket adunk a Térkép `setTrafficEnabled` metódusának.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps Traffic](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Utána: Azure Maps**

Azure Maps számos különböző lehetőséget biztosít a forgalom megjelenítéséhez. A forgalmi incidensek, például a közúti bezárások és a balesetek ikonként is megjeleníthetők a térképen. A forgalom, a színkódolt utak a térképen is behelyezhetők, és a színek a feladott sebességkorlátozás alapján módosíthatók, a normál várható késéshez képest, vagy abszolút késleltetéssel. Az incidensek Azure Maps percenként frissülnek, és az adatforgalom két percenként történik.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps forgalom](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps Android SDK-ról.

> [!div class="nextstepaction"]
> [Az Android Térkép vezérlőelem használata](how-to-use-android-map-control-library.md)
