---
title: Szimbólumréteg hozzáadása androidos térképekhez | Microsoft Azure Maps
description: Ebben a cikkben megtudhatja, hogyan jelenítheti meg a pontadatokat a térképen egy szimbólumréteg hozzáadásával a Microsoft Azure Maps Android SDK használatával.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335589"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Szimbólumréteg hozzáadása térképhez az Azure Maps Android SDK használatával

Ez a cikk bemutatja, hogyan renderelheti pontadatok egy adatforrásból, mint egy szimbólum réteg egy térképen az Azure Maps Android SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy teljes mértékben kövesse a cikkben leírt lépéseket, telepítenie kell [az Azure Maps Android SDK-t](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) a térkép betöltéséhez.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Ha jelölőt szeretne hozzáadni a térképhez a szimbólumréteg használatával, kövesse az alábbi lépéseket:

1. Az**activity_main.xml**  > **fájl** >  **szerkesztése,** így a következő XML-fájlnak tűnik:
    
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

2. Másolja a következő kódrészletet az `MainActivity.java` osztály **onCreate()** metódusába.

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
    
    A kódrészlet felett először beszerez egy Azure Maps map vezérlőpéldány **tonReady()** visszahívási módszer használatával. Ezután létrehoz egy adatforrás-objektumot a **DataSource-osztály** használatával, és hozzáadja azt a térképhez. Ezután hozzáad egy pontgeometriát tartalmazó **funkciót.** A piros jelölőkép ezután a szimbólum ikonjaként lesz beállítva. A **szimbólumréteg** szöveget vagy ikonokat használ az adatforrásba burkolt pontalapú adatok nak a térképen lévő szimbólumként való megjelenítéséhez. Ezután létrehoz egy szimbólumréteget, és az adatforrást átadják a rendereléshez, majd hozzáadjuk a térkép rétegeihez.
    
    Hozzáadása után a kódrészlet felett, meg `MainActivity.java` kell kinéznie az alábbi:
    
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
    
Ezen a ponton, ha futtatja az alkalmazást, látnia kell egy jelölőt a térképen, ahogy itt látható:

<center>

![Android térkép pin](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>További lépések

Ha további dolgokat szeretne hozzáadni a térképhez, olvassa el a következő témakört:

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)