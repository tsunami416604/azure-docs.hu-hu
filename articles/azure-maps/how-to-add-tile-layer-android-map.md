---
title: Csemperéteg hozzáadása androidos térképekhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a csemperéteget a térképen a Microsoft Azure Maps Android SDK használatával.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335565"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Csemperéteg hozzáadása térképhez az Azure Maps Android SDK használatával

Ez a cikk bemutatja, hogyan jelenítheti meg a csemperéteget egy térképen az Azure Maps Android SDK használatával. A mozaikrétegek lehetővé teszik, hogy képeket helyezhet az Azure Maps alaptérképcsempéire. Az Azure Maps csempézési rendszeréről a [Nagyítási szintek és](zoom-levels-and-tile-grid.md) a csemperács dokumentációjában talál további információt.

A mozaikréteg kiszolgálóról töltődik be a csempékbe. Ezek a képek előre renderelhetők és tárolhatók, mint bármely más kép a kiszolgálón, a csemperéteg által érthető elnevezési konvenció használatával. Vagy ezek a képek egy dinamikus szolgáltatással jeleníthetők meg, amely közel valós időben hozza létre a képeket. Az Azure Maps TileLayer osztály a következő három különböző csempeszolgáltatás-elnevezési konvenciót támogat:

* X, Y, Nagyítás jelölés - A nagyítási szint alapján az x az oszlop, y pedig a mozaikrácsban lévő mozaik sorpozíciója.
* Négykulcsos jelölés – Az x, y kombináció egyetlen karakterlánc-értékre nagyítja az információt, amely egy csempe egyedi azonosítója.
* Határolókeret – A határolókeret koordinátái `{west},{south},{east},{north}` segítségével a [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms)által általánosan használt formátumban adható kép.

> [!TIP]
> A TileLayer nagyszerű módja annak, hogy nagy adatkészleteket jelenítsen meg a térképen. Nem csak egy mozaikréteg generálható képből, de a vektoradatok is mozaikrétegként is renderelhetők. A vektoradatok mozaikrétegként való megjelenítésével a térképvezérlőnek csak azokat a csempéket kell betöltenie, amelyek fájlméretük sokkal kisebb lehet, mint az általuk képviselt vektoradatoknak. Ezt a technikát sokan használják, akiknek több millió sornyi adatot kell megjelenítenie a térképen.

A csempe rétegnek átadott csempe URL-címének egy tilejson-erőforrás http/https URL-címének vagy a következő paramétereket használó csempe URL-sablonnak kell lennie: 

* `{x}`- X helyzetben a csempe. Is `{y}` szüksége `{z}`van, és .
* `{y}`- Y helyzetben a csempe. Is `{x}` szüksége `{z}`van, és .
* `{z}`- Zoom szinten a csempe. Is `{x}` szüksége `{y}`van, és .
* `{quadkey}`- Csempe négykulcsos azonosító alapján a Bing Maps csempe rendszer elnevezési konvenció.
* `{bbox-epsg-3857}`- Az EPSG 3857 térbeli referenciarendszer formátumú `{west},{south},{east},{north}` határolókeret-karakterlánca.
* `{subdomain}`- Az altartomány értékeinek helyőrzője, ha az altartomány értéke meg van adva.

## <a name="prerequisites"></a>Előfeltételek

A folyamat befejezéséhez ebben a cikkben telepítenie kell az [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) betölteni egy térképet.


## <a name="add-a-tile-layer-to-the-map"></a>Mozaikréteg hozzáadása a térképhez

 Ez a minta bemutatja, hogyan hozhat létre mozaikréteget, amely mozaikkészletre mutat. Ezek a csempék az "x, y, zoom" csempézési rendszert használják. A forrás a csempe réteg egy időjárási radar overlay az [Iowa Környezetvédelmi Mesonet az Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

Az alábbi lépések végrehajtásával hozzáadhat egy mozaikréteget a térképhez.

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Másolja az alábbi kódrészletet az `MainActivity.java` osztály **onCreate()** metódusába.

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
    
    A kódrészlet felett először beszerez egy Azure Maps map vezérlőpéldány **tonReady()** visszahívási módszer használatával. Ezután létrehoz `TileLayer` egy objektumot, és átad egy `tileUrl` formázott **xyz** csempe URL-t a beállításnak. A réteg opacitása a `0.8` beállítás, és mivel a csempeszolgáltatás csempéi 256 képpont `tileSize` méretű csempék, ez az információ átkerül a beállításba. A csemperéteg ezután átkerül a térképek rétegkezelőbe.

    Hozzáadása után a kódrészlet felett, meg `MainActivity.java` kell kinéznie az alábbi:
    
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

Ha most futtatja az alkalmazást, az alábbi módon kell látnia egy sort a térképen:

<center>

![Android térképvonal](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>További lépések

A térképstílusok beállításának módjairól az alábbi cikkben olvashat bővebben.

> [!div class="nextstepaction"]
> [Térképstílusok módosítása androidos térképeken](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)