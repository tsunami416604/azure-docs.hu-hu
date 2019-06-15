---
title: Adja hozzá az Azure Maps leképezi egy szimbólum réteg Android |} A Microsoft Docs
description: Az Azure Maps Android SDK térkép szimbólumok hozzáadása
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871077"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Egy szimbólum réteg hozzáadása az Azure Maps Android SDK térképre

Ez a cikk bemutatja, hogyan pont adatokat egy adatforrásból egy térképen az Azure Maps Android SDK-val szimbólum rétegként leképezni.

## <a name="prerequisites"></a>Előfeltételek

Teljesen kövesse a jelen cikkben ismertetett lépések, telepítenie kell [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) betölteni a térképen.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Adjon hozzá egy jelölő a térképen a szimbólum réteg használatával, kövesse az alábbi lépéseket:

1. Szerkesztés **res** > **elrendezés** > **activity_main.xml** , a következő XML formátumú hasonlóan néz ki:
    
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

2. Másolja a következő kódrészletet a **onCreate()** módszere a `MainActivity.java` osztály.

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
    
    A fenti kódrészletben először lekéri az Azure Maps térkép vezérlőelem példány használatával az **onReady()** visszahívási metódus. Ezután létrehoz egy objektum használhatja a **DataSource** osztályt, és hozzáadja azt a térképen. Ezután hozzáadja egy **funkció** tartalmazó egy pont geometriai rá. Egy piros jelölőkép majd a szimbólum ikonja van beállítva. A **szimbólum réteg** használja a szöveges vagy ikonjai burkolja az adatforrás a térképen karakterként szoftverfrissítésipont-alapú adatok megjelenítéséhez. A szimbólum réteg majd jön létre, és átadott jelennek meg, hogy az adatforrás és a térkép rétegekhez kerül.
    
    Miután hozzáadta a fenti kódrészletben a `MainActivity.java` az alábbihoz hasonlóan kell kinéznie:
    
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
    
Ezen a ponton az alkalmazás futtatásakor megjelenik egy jelölő a térképen, itt látható módon:

<center>

![Android térkép PIN-kód](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>További lépések

További szolgáltatáshasználatot felvenni a térképre, tekintse meg:

> [!div class="nextstepaction"]
> [Az alakzatok hozzáadása egy Android térkép](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)