---
title: Szimbólum-réteg hozzáadása az Android Maps-hez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy jelölőt egy térképhez. Egy olyan példát láthat, amely a Azure Maps Android SDK-t használja egy adatforrásból származó, pont-alapú adatokból álló szimbólum réteg hozzáadásához.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 10969e20cd7ae71cade230f6643a27d5d940ceaa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91311274"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Szimbólum-réteg hozzáadása térképhez Azure Maps Android SDK használatával

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból szimbólumként szolgáló rétegként a Azure Maps Android SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések teljes körű követéséhez telepítenie kell [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) -t egy Térkép betöltéséhez.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Ha a szimbólum réteg használatával szeretne jelölőt hozzáadni a térképhez, kövesse az alábbi lépéseket:

1. Szerkessze a **res**  >  **layout**  >  **activity_main.xml** , hogy a következő XML-fájlhoz hasonlítson:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Másolja az alábbi kódrészletet az osztály **onCreate ()** metódusára `MainActivity.java` .

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    A fenti kódrészlet először a **onReady ()** callback metódus használatával szerzi be Azure Maps Térkép vezérlőelem-példányát. Ezután létrehoz egy adatforrás-objektumot a **DataSource** osztály használatával, és hozzáadja azt a térképhez. Ezután hozzáadja a pont geometriáját tartalmazó **funkciót** . Ezután egy piros jelölő képe ikonként van beállítva a szimbólumhoz. A **szimbólum-réteg** szöveggel vagy ikonokkal jeleníti meg az adatforrásban lévő pont-alapú adatfeliratokat a térképen. Ekkor létrejön egy szimbólum-réteg, és a rendszer átadja az adatforrást a rendereléshez, majd hozzáadja a Térkép rétegeihez.
    
    Miután hozzáadta a fenti kódrészletet, a `MainActivity.java` következőhöz hasonlóan kell kinéznie:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
Ezen a ponton, ha futtatja az alkalmazást, látnia kell egy jelölőt a térképen, ahogy az itt látható:

<center>

![Androidos Térkép PIN-kódja](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Alapértelmezés szerint a szimbólum-rétegek az átfedésben lévő szimbólumok elrejtésével optimalizálja a szimbólumok megjelenítését. A nagyításhoz a rejtett szimbólumok láthatóvá válnak. A funkció letiltásához és az összes szimbólum megjelenítéséhez állítsa be a beállítást a következőre: `iconAllowOverlap` `true` .

## <a name="next-steps"></a>Következő lépések

Ha további dolgokat szeretne hozzáadni a térképhez, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)