---
title: Csempe réteg hozzáadása Android-térképekhez Azure Mapsban | Microsoft Docs
description: Csempe réteg hozzáadása térképhez Azure Maps Android SDK használatával
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5d5f50a38db95f6e62bdd8c51aefd5957041e682
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886610"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Csempe réteg hozzáadása térképhez a Azure Maps Android SDK használatával

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a csempéket a térképeken a Azure Maps Android SDK használatával. A csempe rétegek lehetővé teszik, hogy az alapszintű Térkép csempék fölé írja a képeket Azure Maps. Azure Maps csempe rendszerével kapcsolatos további információkért tekintse meg a [nagyítási szintek és](zoom-levels-and-tile-grid.md) a csempék rácsának dokumentációját.

Egy csempe réteg tölti be a csempéket egy kiszolgálóról. Ezeket a lemezképeket előre megjelenítheti és tárolhatja, mint bármely más rendszerkép egy kiszolgálón a csempe rétege által értelmezett elnevezési konvencióval vagy olyan dinamikus szolgáltatással, amely menet közben hozza létre a képeket. Az Azure Maps TileLayer osztály három különböző csempe-szolgáltatási elnevezési konvenciót támogat. 

* X, Y, nagyítási jelölés – a nagyítási szint alapján az x az oszlop, az Y pedig a csempén lévő csempe sor pozíciója.
* Quadkey jelölés – x, y és nagyítási információ egyetlen karakterlánc-értékre, amely egy csempe egyedi azonosítója.
* A határolókerethoz tartozó határoló mező koordinátáit a [webes leképezési szolgáltatások (WMS)](https://www.opengeospatial.org/standards/wms)által gyakran `{west},{south},{east},{north}` használt formátumú rendszerképek megadására lehet használni.

> [!TIP]
> A TileLayer nagyszerű lehetőséget mutat a nagyméretű adathalmazok megjelenítésére a térképen. Nem csak a csempe réteg hozható létre egy képből, de a vektoros adatok csempe rétegként is megjeleníthető. A vektoros adattároló rétegként való megjelenítésével a Térkép vezérlőelemnek csak be kell töltenie a csempéket, ami sokkal kisebb lehet a fájlméretnél, mint az általuk képviselt adatmennyiség. Ezt a technikát sokan használják, akiknek több millió sornyi adatsort kell megjeleníteniük a térképen.

A csempe rétegbe átadott csempe URL-címének HTTP/HTTPS URL-címnek kell lennie egy TileJSON-erőforráshoz vagy egy csempe URL-sablonhoz, amely a következő paramétereket használja: 

* `{x}`-A csempe X pozíciója A és `{y}` a `{z}`is szükséges.
* `{y}`-A csempe Y pozíciója A és `{x}` a `{z}`is szükséges.
* `{z}`– A csempe nagyítási szintje A és `{x}` a `{y}`is szükséges.
* `{quadkey}`– Csempe quadkey-azonosítója a Bing Maps csempe rendszerelnevezési konvenciója alapján.
* `{bbox-epsg-3857}`– Egy határolókeret karakterlánca, amely a EPSG `{west},{south},{east},{north}` 3857 térbeli hivatkozási rendszer formátumát jelöli.
* `{subdomain}`-A megadott altartomány-értékeket tartalmazó helyőrzőt adja hozzá a rendszer.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő folyamat elvégzéséhez telepítenie kell [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) -t egy Térkép betöltéséhez.


## <a name="add-a-tile-layer-to-the-map"></a>Csempe réteg hozzáadása a térképhez

 Ez a minta bemutatja, hogyan hozhat létre egy csempe réteget, amely az x, y, zoom csempe rendszerű csempe-készletre mutat. Ennek a csempe rétegnek a forrása az [Iowa Állami Egyetem Iowa környezeti Mesonet](https://mesonet.agron.iastate.edu/ogc/)származó időjárási radar. 

Az alábbi lépéseket követve hozzáadhat egy csempe réteget a térképhez.

1. Szerkessze a **res > elrendezést > activity_main. xml fájlt** úgy, hogy az a következőhöz hasonlóan néz ki:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Másolja az alábbi kódrészletet az `MainActivity.java` osztály **onCreate ()** metódusára.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    A fenti kódrészlet először a **onReady ()** callback metódus használatával szerzi be Azure Maps Térkép vezérlőelem-példányát. Ezután létrehoz egy `TileLayer` objektumot, és átadja egy formázott **XYZ** csempe URL- `tileUrl` címét a lehetőséghez. A réteg opacitása úgy van beállítva, `0.8` hogy a csempe szolgáltatás által használt csempék 256 képpont csempék legyenek, ez az információ átkerül `tileSize` a lehetőségbe. Ezután a csempe réteget a Maps Layer Manager továbbítja.

    Miután hozzáadta a fenti kódrészletet, `MainActivity.java` a következőhöz hasonlóan kell kinéznie:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
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

Ha most futtatja az alkalmazást, látnia kell egy vonalat a térképen az alábbi képen látható módon:

<center>

![Androidos Térkép sora](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>További lépések

A Térkép stílusainak beállításával kapcsolatos további tudnivalókért tekintse meg a következő cikket.

> [!div class="nextstepaction"]
> [Térkép stílusainak módosítása Android-térképeken](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)