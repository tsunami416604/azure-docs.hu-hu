---
title: Az Azure Maps Android térkép vezérlőelem használata |} A Microsoft Docs
description: Android térkép vezérlőelem használata az Azure Maps szolgáltatásban.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c1258ab619b165553c55481ad67d663be65ac832
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269603"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Azure Maps Android SDK használata

Az Azure Maps Android SDK Android vektor maps szalagtár. Ez a cikk végigvezeti Önt az Azure Maps Android SDK telepítése, térkép betöltése és PIN-kód helyezi azt.

## <a name="prerequisites-to-get-started"></a>A kezdéshez Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Kövesse a jelen útmutató lépéseit, először szüksége [fiók és kulcsok kezelése](how-to-manage-account-keys.md) hozhat létre és kezelhet a fiók előfizetés S1 tarifacsomagot.

### <a name="download-android-studio"></a>Az Android studio letöltése

Letöltheti a [Android Studio](https://developer.android.com/studio/) ingyenes a Google rendszeréből. Az Azure Maps Android SDK telepítéséhez kell először az Android Studio letöltése, és hozzon létre egy projektet egy üres tevékenységgel.

## <a name="create-a-project-in-android-studio"></a>Az Android Studio-projekt létrehozása

Hozzon létre egy új projektet egy üres tevékenységgel kell. Hozzon létre egy új Android Studio-projektet az alábbi lépésekkel:

1. A *válassza ki a projekt*, ellenőrizze a "Telefonos és Táblagépes" képernyő tényezőként arról, hogy az alkalmazás.
2. Kattintson a *üres tevékenység* méretet, és kattintson a **tovább**.
3. A *projekt konfigurálásához*válassza `API 21: Android 5.0.0 (Lollipop)` a minimális SDK-t. Ez az Azure Maps Android SDK által támogatott legalacsonyabb verzió.
4. Fogadja el az alapértelmezett `Activity Name` és `Layout Name` kattintson **Befejezés**

Lásd: [Android Studio-dokumentáció](https://developer.android.com/studio/intro/) további az Android Studio telepítése és a egy új projekt létrehozása érdekében.

![Új projekt létrehozása](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>A virtuális eszköz beállítása

Az Android Studio lehetővé teszi, hogy a számítógép egy virtuális Android-eszköz beállításához. Ami segít az alkalmazás teszteléséhez, fejlesztése során. Állítsa be a virtuális eszköz kattintson az Android virtuális eszközt (AVD) Manager ikonra a felső, a projekt képernyő jobb. Kattintson a **virtuális eszköz létrehozása** gombra. A kezelő-n keresztül is elérhető, **eszközök > Android > AVD Manager** az eszköztáron. Az a **telefonok** kategória, jelölje be **Nexus 5 X** kattintson **tovább**.

További információ az AVD a beállítása a [Android Studio-dokumentáció](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Az Azure Maps Android SDK telepítése

Mielőtt folytatná az alkalmazása létrehozásához felé, kövesse az Azure Maps Android SDK telepítése az alábbi lépéseket. 

1. Adja hozzá a következőt a **minden projekt**, tárházak letiltása a **build.gradle** fájlt.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítés a **app/build.gradle** , és adja hozzá a következőket:

    1. Az Android blokk adja hozzá a következőket:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Frissítse a függőségeket tartalmazó blokkba, és adja hozzá a következőket:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Adja hozzá a következő engedélyek beállítása a **AndroidManifest.xml**

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Szerkesztés **res > elrendezés > activity_main.xml**, ezért az alábbi XML hasonlóan néz ki:
    
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

5. Szerkesztés **MainActivity.java** térkép nézet tevékenység osztály létrehozása. A Szerkesztés után az alábbi osztály hasonlóan kell kinéznie:

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

A fenti lépések végrehajtását követően nagy valószínűséggel kap figyelmeztetést az Android Studio néhány, a szöveg a kódban. Ezek a figyelmeztetések megszüntetéséhez hivatkozik osztályok importálása `MainActivity.java`.

Billentyű lenyomásával automatikusan importálhatja ezeket az osztályokat `Alt` + `Enter`(`Option` + `Return` Mac gépen). 

Kattintson a **Run 'App'** gombra (vagy `Control` + `R` Mac számítógépen) az alkalmazás létrehozásához.

![Kattintson a Futtatás](./media/how-to-use-android-map-control-library/run-app.png)

Hozza létre az alkalmazást az android studióhoz készült néhány másodpercet vesz igénybe. A build után a Befejezés az emulált Android-eszközön tesztelheti az alkalmazást. Az alábbihoz hasonló térkép jelenik meg.

![Android map](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Adjon hozzá egy jelölő a térkép

Ahhoz, hogy a térkép be jelölő hozzáadásához hozzáadni `mapView.getMapAsync()` függvény a `MainActivity.java`. A végső `MainActivity.java` a következőhöz hasonlóan kell kinéznie:

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

Futtassa újra az alkalmazást, és megjelenik egy jelölő a térképen az alábbihoz hasonló.

![Android térkép PIN-kód](./media/how-to-use-android-map-control-library/android-map-pin.png)