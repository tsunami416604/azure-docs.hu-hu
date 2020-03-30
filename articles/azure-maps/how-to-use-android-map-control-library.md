---
title: Az Android térképvezérlés első lépései | Microsoft Azure Maps
description: Ebben a cikkben megtudhatja, hogyan kezdheti el az Android térképvezérlőt a Microsoft Azure Maps Android SDK használatával.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55bfb5030d0a168e7556240212fcd5f3be30a289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335366"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Az Azure Maps Android SDK – első lépések

Az Azure Maps Android SDK egy vektoros térképtár Android. Ez a cikk végigvezeti az Azure Maps Android SDK telepítésének folyamatain, és betölti a térképet.

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

A cikkben szereplő eljárások végrehajtásához először létre kell [hoznia egy Azure Maps-fiókot](quick-demo-map-app.md#create-an-account-with-azure-maps) az S1 tarifacsomagban, és [be kell szereznie](quick-demo-map-app.md#get-the-primary-key-for-your-account) a fiók elsődleges kulcsát.

Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.

### <a name="download-android-studio"></a>Az Android Studio letöltése

Töltse le az Android Studio alkalmazást, és hozzon létre egy projektet egy üres tevékenységgel az Azure Maps Android SDK telepítése előtt. Az [Android Studio](https://developer.android.com/studio/) ingyenesen letölthető a Google-tól. 

## <a name="create-a-project-in-android-studio"></a>Projekt létrehozása az Android Studio-ban

Először hozzon létre egy új projektet üres tevékenységgel. Az alábbi lépésekkel hozzon létre egy Android Studio-projektet:

1. A **Projekt kiválasztása**csoportban válassza a Telefon és **táblagép**lehetőséget. Az alkalmazás ezen a mérettényezőn fog futni.
2. A **Telefon és a Táblagép** lapon válassza a **Tevékenység kiürítése**lehetőséget, majd a **Tovább**gombot.
3. A **Projekt konfigurálása**csoportban válassza ki `API 21: Android 5.0.0 (Lollipop)` a minimális SDK-t. Ez az Azure Maps Android SDK által támogatott legkorábbi verzió.
4. Fogadja el `Activity Name` `Layout Name` az alapértelmezett értéket, és válassza a **Befejezés gombot.**

Az [Android Studio telepítésével](https://developer.android.com/studio/intro/) és az új projekt létrehozásával kapcsolatban további segítséget talál az Android Studio dokumentációjában.

![Projekt létrehozása az Android stúdióban ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Virtuális eszköz beállítása

Az Android Studio lehetővé teszi egy virtuális Android-eszköz beállítását a számítógépen. Ezzel segíthet az alkalmazás tesztelésében a fejlesztés során. Virtuális eszköz beállításához válassza az Android Virtuális eszköz (AVD) kezelőjének ikonját a projekt képernyőjének jobb felső sarkában, majd válassza a **Virtuális eszköz létrehozása lehetőséget.** Azt is eljut az AVD Manager kiválasztásával **Eszközök** > **Android** > **AVD Manager** az eszköztáron. A **Telefonok kategóriában** válassza a **Nexus 5X**lehetőséget, majd a **Tovább**gombot.

Az AVD beállításáról az Android [Studio dokumentációjában olvashat bővebben.](https://developer.android.com/studio/run/managing-avds)

![Android emulátor](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Az Azure Maps Android SDK telepítése

Az alkalmazás létrehozásának következő lépése az Azure Maps Android SDK telepítése. Hajtsa végre az alábbi lépéseket az SDK telepítéséhez:

1. Nyissa meg a legfelső szintű **build.gradle** fájlt, és adja hozzá a következő kódot az **összes projekthez**, **az adattárak** blokkszakaszához:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítse az **alkalmazást/build.gradle-t,** és adja hozzá a következő kódot:
    
    1. Győződjön meg arról, hogy a projekt **minSdkVersion** API 21-es vagy újabb verziójú.

    2. Adja hozzá a következő kódot az Android szakaszhoz:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Frissítse a függőségi blokkot, és adjon hozzá egy új implementációs függőségi sort a legújabb Azure Maps Android SDK-hoz:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Nyissa meg az eszköztár **Fájl fájlját,** majd kattintson a **Projekt szinkronizálása gradle fájlokkal parancsra.**
3. Térképtöredék hozzáadása a fő \> tevékenységhez (res layout \> activity\_main.xml):
    
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

4. A **MainActivity.java** fájlban a következőkre van szükség:
    
    * az Azure Maps SDK importálásának hozzáadása
    * az Azure Maps hitelesítési adatainak beállítása
    * a térképvezérlő példány bekése az **onCreate** metódusban

    Ha globálisan beállítja az `AzureMaps` `setSubscriptionKey` osztály `setAadProperties` hitelesítési adatait a vagy metódusokkal, akkor nem kell minden nézetben megadnia a hitelesítési adatokat. 

    A térképvezérlő saját életciklus-módszereket tartalmaz az Android OpenGL életciklusának kezelésére. Ezeket az életciklus-metódusokat közvetlenül az azt tartalmazó tevékenységből kell meghívni. Ahhoz, hogy az alkalmazás helyesen hívja meg a térképvezérlő életciklus-metódusait, felül kell bírálnia a következő életciklus-módszereket a térképvezérlőt tartalmazó tevékenységben. És meg kell hívnia a megfelelő térképvezérlési módszert. 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

    A **MainActivity.java** fájl szerkesztése az alábbiak szerint:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>Osztályok importálása

Az előző lépések elvégzése után valószínűleg figyelmeztetéseket kap az Android Studio-tól a kód egy részéről. A figyelmeztetések feloldásához importálja a `MainActivity.java`alkalmazásban hivatkozott osztályokat.

Ezeket az osztályokat automatikusan importálhatja az Alt+Enter (Option+Return on a Mac) lehetőség kiválasztásával.

Az alkalmazás létrehozásához válassza a futtatás gombot az alábbi ábrán látható módon (vagy nyomja meg a Control+R billentyűkombinációt Macen).

![Kattintson a Run (Futtatás) gombra](./media/how-to-use-android-map-control-library/run-app.png)

Az Android Studio néhány másodpercet vesz igénybe az alkalmazás megépítése. Miután a build befejeződött, tesztelheti az alkalmazást az emulált Android-eszközön. Látnod kellene egy ilyen térképet:

<center>

![Azure Maps az Android-alkalmazásban](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>A térkép honosítása

Az Azure Maps Android SDK három különböző módon állítja be a térkép nyelvét és regionális nézetét. A következő kód bemutatja, hogyan kell beállítani a nyelvet francia ("fr-FR") és a regionális nézet "auto". 

Az első lehetőség az, hogy átadja a `AzureMaps` nyelvet, `setLanguage` és `setView` tekintse meg a regionális információkat az osztályba a statikus és módszerek globálisan. Ez beállítja az alapértelmezett nyelvi és regionális nézetet az alkalmazásba betöltött összes Azure Maps-vezérlőben.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A második lehetőség a nyelv átad és az adatok megtekintése a térképvezérlő XML-be.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A harmadik lehetőség a térkép nyelvének és regionális nézetének `setStyle` programozott beállítása a térképek módszerrel. Ezt bármikor megteheti a térkép nyelvének és regionális nézetének megváltoztatásához.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Íme egy példa az Azure Maps a nyelv beállítása "fr-FR" és a regionális nézet beállítása "auto".

<center>

![Azure Maps, térképkép, amelyen francia címkék láthatók](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

A támogatott nyelvek és a regionális nézetek teljes listáját [itt](supported-languages.md)olvashatja.

## <a name="next-steps"></a>További lépések

További információ arról, hogyan adhat hozzá átfedési adatokat a térképen:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása Android-térképhez](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Térképstílusok módosítása androidos térképeken](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
