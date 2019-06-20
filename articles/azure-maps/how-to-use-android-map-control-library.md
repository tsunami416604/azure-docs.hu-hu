---
title: Ismerkedés az Azure Maps-Android térkép vezérlőelem |} A Microsoft Docs
description: Az Azure Maps-Android térkép vezérlőelem.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9df5eb9fa4493f82c6efd4a8e30eee324e4eac2a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273830"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Ismerkedés az Azure Maps SDK Androidra

Az Azure Maps Android SDK Android vector map kódtár. Ez a cikk végigvezeti Önt a folyamatokon telepítése az Azure Maps Android SDK és a egy térkép betöltése.

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

1. Nyissa meg a legfelső szintű **build.gradle** fájlt, és adja hozzá a következő kódot a **minden projekt**, **tárházak** rész letiltása:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítés a **app/build.gradle** és adja hozzá a következő kódot:
    
    1. Győződjön meg arról, hogy a projekt **minSdkVersion** API 21 vagy újabb verziója.

    2. Adja hozzá a következő kódot a Android szakaszához:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Frissítse a függőségeket tartalmazó blokkba, és adja hozzá a megvalósítás függőségi írja új sorba a legújabb Azure Maps Android SDK-hoz készült:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Rendszeresen folyamatban van, az Azure Maps Android SDK frissített és továbbfejlesztett. Megtekintheti a [Android térkép vezérlőelem – első lépések](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) dokumentációjában, a legújabb Azure Maps megvalósítási verziószámának beolvasásához. Emellett a verziószám között megadható "0.2-es", "0 +", hogy mindig a legújabb verzióra mutasson.

3. Szerkesztés **res** > **elrendezés** > **activity_main.xml** és cserélje le a következő:
    
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

4. Az a **MainActivity.java** fájlt kell:
    
    * az Azure Maps SDK importálások hozzáadása
    * Állítsa be az Azure Maps hitelesítő adatait
    * a térkép vezérlőelem példány lépjen be a **onCreate** metódus

    A hitelesítési adatok beállítása a globálisan a módszerekkel setSubscriptionKey vagy setAadProperties AzureMaps osztályról teszi, hogy így nem kell hozzáadni a hitelesítő adatait minden nézethez. A térkép vezérlőelem a saját életciklus metódust kell meghívni a tartalmazó tevékenység közvetlenül az Android a OpenGL életciklusának kezelésére szolgáló tartalmaz. Ahhoz, hogy az alkalmazás megfelelően, hívja meg a térkép vezérlőelem életciklus módszerek kell bírálja felül a következő életciklus módszerek a tevékenység, amely tartalmazza a térkép vezérlőelem és a megfelelő térkép vezérlőelem metódust hívja. 

    Szerkessze a **MainActivity.java** fájlt az alábbiak szerint:
    
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

<center>

![Android map](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan területre adatok hozzáadása a térképen:

> [!div class="nextstepaction"]
> [A szimbólum réteg hozzáadása az Android-térképet](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Az alakzatok hozzáadása egy Android térkép](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Térkép stílusok Android Maps-Közösséghez](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
