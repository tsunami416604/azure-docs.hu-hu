---
title: Alakzatok hozzáadása Android-térképekhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a különböző alakzatokat a térképen a Microsoft Azure Maps Android SDK használatával.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7a793ff35675c876dc429976ebee96887b12735a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335594"
---
# <a name="add-a-shape-to-a-map-using-azure-maps-android-sdk"></a>Alakzat hozzáadása térképhez az Azure Maps Android SDK használatával

Ez a cikk bemutatja, hogyan jelenítheti meg az alakzatokat egy térképen az Azure Maps Android SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

A folyamat befejezéséhez ebben a cikkben telepítenie kell az [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) betölteni egy térképet.


## <a name="add-a-line-to-the-map"></a>Vonal hozzáadása a térképhez

A **vonalréteg**segítségével vonalat adhat a térképhez, az alábbi lépéseket követve hozzáadhat egy vonalat a térképhez.

1. A **activity_main.xml > fájl > szerkesztése > elrendezést** úgy, hogy az alábbinak tűnjön:

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
            app:mapcontrol_centerLat="40.743270"
            app:mapcontrol_centerLng="-74.004420"
            app:mapcontrol_zoom="12"
            />
    
    </FrameLayout>
    ```

2. Másolja az alábbi kódrészletet az `MainActivity.java` osztály **onCreate()** metódusába.

    ```Java
    mapControl.onReady(map -> {

        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a list of points.
        List<Point> points = Arrays.asList(
            Point.fromLngLat(-73.972340, 40.743270),
            Point.fromLngLat(-74.004420, 40.756800));
    
        //Create a LineString feature and add it to the data source.
        dataSource.add(LineString.fromLngLats(points));
    
        //Create a line layer and add it to the map.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(5f)));
    });

    ```
    
    A kódrészlet felett először beszerez egy Azure Maps map vezérlőpéldány **tonReady()** visszahívási módszer használatával. Ezután létrehoz egy adatforrás-objektumot a **DataSource-osztály** használatával, és hozzáadja azt a térképhez. Ezután létrehoz egy listát a **pont** objektumokról. A **linestring** jön létre a pontok listájából, és hozzáadódnak az adatforráshoz. A **vonalréteg** az adatforrásba csomagolt vonalobjektumokat jeleníti meg a térképen. Ekkor létrejön egy vonalréteg, és hozzáadja az adatforrást.

    Hozzáadása után a kódrészlet felett, meg `MainActivity.java` kell kinéznie az alábbi:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.LineString;
    import com.mapbox.geojson.Point;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a list of points.
                List<Point> points = Arrays.asList(
                    Point.fromLngLat(-73.972340, 40.743270),
                    Point.fromLngLat(-74.004420, 40.756800));
            
                //Create a LineString feature and add it to the data source.
                dataSource.add(LineString.fromLngLats(points));
            
                //Create a line layer and add it to the map.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(5f)));
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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

Ha most futtatja az alkalmazást, az alábbi módon kell látnia egy sort a térképen:

<center>

![Android-térképen megjelenített vonal](./media/how-to-add-shapes-to-android-map/android-map-line.png)</center>


## <a name="add-a-polygon-to-the-map"></a>Sokszög hozzáadása a térképhez

A **Sokszögréteg** lehetővé teszi a sokszög területének renderelését a térképen. Az alábbi lépéseket követve sokszöget adhat a térképhez.

1. A **activity_main.xml > fájl > szerkesztése > elrendezést** úgy, hogy az alábbinak tűnjön:

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
            app:mapcontrol_centerLat="40.78"
            app:mapcontrol_centerLng="-73.97"
            app:mapcontrol_zoom="12"
            />
    
    </FrameLayout>
    ```

2. Másolja a következő kódrészletet az `MainActivity.java` osztály **onCreate()** metódusába.

    ```Java
    mapControl.onReady(map -> {
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a list of point arrays to create polygon rings.
        List<List<Point>> rings = Arrays.asList(Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.7968),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)));
    
        //Create a Polygon feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
    
        //Add a polygon layer for rendering the polygon area.
        map.layers.add(new PolygonLayer(dataSource,
            fillColor("red")));
    
        //Add a line layer for rendering the polygon outline.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(2f)));
    });
    ```

    A kódrészlet felett először beszerez egy Azure Maps map vezérlőpéldány **tonReady()** visszahívási módszer használatával. Ezután létrehoz egy adatforrás-objektumot a **DataSource-osztály** használatával, és hozzáadja azt a térképhez. Ezután **egy Sokszög** objektum jön létre a **Pontobjektumok** listájából, és hozzáadódik az adatforráshoz. A **sokszögréteg** a térképen az adatforrásba burkolt adatokat jelenít meg. Ezután létrehoz egy sokszögréteget a sokszögterület megjelenítéséhez, és hozzáadja az adatforrást. A **vonalréteg** az adatforrásba csomagolt vonalobjektumokat jeleníti meg. A kódrészlet utolsó része egy vonalréteget hoz létre a sokszög körvonalának megjelenítéséhez, és hozzáadja hozzá az adatforrást.

    Hozzáadása után a kódrészlet felett, meg `MainActivity.java` kell kinéznie az alábbi:

    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import java.util.Arrays;
    import android.util.Log;
    import java.util.Collections;
    import android.support.v7.app.AppCompatActivity;
    import com.mapbox.geojson.Point;
    import com.mapbox.geojson.Polygon;
    import com.mapbox.geojson.Feature;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.layer.PolygonLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
    import static com.microsoft.azure.maps.mapcontrol.options.PolygonLayerOptions.fillColor;
    
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a list of point arrays to create polygon rings.
                List<List<Point>> rings = Arrays.asList(Arrays.asList(
                    Point.fromLngLat(-73.98235, 40.76799),
                    Point.fromLngLat(-73.95785, 40.80044),
                    Point.fromLngLat(-73.94928, 40.7968),
                    Point.fromLngLat(-73.97317, 40.76437),
                    Point.fromLngLat(-73.98235, 40.76799)));
            
                //Create a Polygon feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
            
                //Add a polygon layer for rendering the polygon area.
                map.layers.add(new PolygonLayer(dataSource,
                    fillColor("red")));
            
                //Add a line layer for rendering the polygon outline.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(2f)));
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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

Ha most futtatja az alkalmazást, akkor egy sokszöget kell látnia a térképen az alábbiak szerint:

<center>

![Android-térképen megjelenített sokszög](./media/how-to-add-shapes-to-android-map/android-map-polygon.png)</center>


## <a name="next-steps"></a>További lépések

További adatok hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)