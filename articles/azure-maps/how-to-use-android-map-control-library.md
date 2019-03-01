---
title: Az Android térkép vezérlőelem használata az Azure Maps |} A Microsoft Docs
description: Az Azure Maps-Android térkép vezérlőelem.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010667"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Az Azure Maps Android SDK használata

Az Azure Maps Android SDK Android vector map kódtár. Ez a cikk végigvezeti Önt a folyamatokon telepítése az Azure Maps Android SDK-t, a térkép betöltése és helyezi el a PIN-kódot a térképen.

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

Hajtsa végre az ebben a cikkben ismertetett, szüksége lesz a [létrehozása az Azure Maps-fiók](how-to-manage-account-keys.md) az S1 tarifacsomagot.

### <a name="download-android-studio"></a>Az Android Studio letöltése

Az Android Studio letöltése, és hozzon létre egy projektet egy üres tevékenységgel az Azure Maps Android SDK telepítése előtt kell. Is [az Android Studio letöltése](https://developer.android.com/studio/) ingyenes a Google rendszeréből. 

## <a name="create-a-project-in-android-studio"></a>Az Android Studio-projekt létrehozása

Először egy új projekt létrehozása egy üres tevékenységgel. Hajtsa végre az alábbi lépéseket az Android Studio-projekt létrehozásához:

1. A **válassza ki a projekt**válassza **telefon és táblagép**. Az alkalmazás futni fog a méretet.
2. Az a **telefon és táblagép** lapon jelölje be **üres tevékenység**, majd válassza ki **tovább**.
3. A **projekt konfigurálásához**válassza `API 21: Android 5.0.0 (Lollipop)` a minimális SDK-t. Ez az a legkorábbi, az Azure Maps Android SDK által támogatott verzió.
4. Fogadja el az alapértelmezett `Activity Name` és `Layout Name` válassza **Befejezés**.

Tekintse meg a [Android Studio-dokumentáció](https://developer.android.com/studio/intro/) további segítség az Android Studio telepítése és a egy új projekt létrehozása.

![Projekt létrehozása](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>A virtuális eszköz beállítása

Az Android Studio lehetővé teszi, hogy a számítógép egy virtuális Android-eszköz beállításához. Ez segíthet a tesztelhetők az alkalmazások fejlesztése során. A virtuális eszköz beállításához jelölje ki a Android virtuális eszközt (AVD) Manager ikont a projekt képernyő jobb felső sarkában, és válassza **virtuális eszköz létrehozása**. Kiválasztásával is megtekintheti az AVD Manager **eszközök** > **Android** > **AVD Manager** az eszköztáron. Az a **telefonok** kategória, jelölje be **Nexus 5 X**, majd válassza ki **tovább**.

Egy az AVD beállításáról többet is megtudhat a [Android Studio-dokumentáció](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Az Azure Maps Android SDK telepítése

Az alkalmazás létrehozásakor a következő lépés, hogy telepítse az Azure Maps Android SDK. Hajtsa végre ezeket a lépéseket az SDK telepítése:

1. Adja hozzá a következő kódot a **minden projekt**, **tárházak** letiltása a **build.gradle** fájlt.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítés a **app/build.gradle** és adja hozzá a következő kódot:

    1. Az alábbi kód hozzáadása az Android letiltása:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Frissítse a függőségeket tartalmazó blokkba, és adja hozzá a következő kód azt:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Adja hozzá az alábbi XML-engedélyek beállítása a **AndroidManifest.xml** fájlt:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Szerkesztés **res** > **elrendezés** > **activity_main.xml** , az XML hasonlóan néz ki:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Szerkesztés **MainActivity.java** térkép nézet tevékenység osztály létrehozása. Után szerkesztheti, ez az osztály hasonlóan kell kinéznie:

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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>Osztályok importálása

Miután elvégezte az előző lépéseket, valószínűleg kap figyelmeztetést az Android Studio egy részének a kódot. Ezek a figyelmeztetések megszüntetéséhez hivatkozott osztályok importálása `MainActivity.java`.

Alt + Enter billentyűkombinációt (a beállítás + Return Mac számítógépen) kiválasztásával automatikusan importálhatja ezeket az osztályokat.

Válassza ki a Futtatás gomb, ahogyan az a következő ábra (vagy nyomja le a Mac számítógépen Control + R), az alkalmazás létrehozásához.

![Kattintson a Futtatás](./media/how-to-use-android-map-control-library/run-app.png)

Az Android Studio az alkalmazás néhány másodpercet vesz igénybe. A létrehozás befejezése után az emulált Android-eszközön tesztelheti az alkalmazást. A következőhöz hasonló térkép kell megjelennie:

![Android map](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Adjon hozzá egy jelölő a térkép

Szeretne hozzáadni egy jelölő a térképre, adja hozzá a `mapView.getMapAsync()` függvény `MainActivity.java`. A végső `MainActivity.java` kódot kell kinéznie:

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

Futtassa újra az alkalmazást. Itt látható módon kell megjelennie egy jelölő a térképen:

![Android térkép PIN-kód](./media/how-to-use-android-map-control-library/android-map-pin.png)