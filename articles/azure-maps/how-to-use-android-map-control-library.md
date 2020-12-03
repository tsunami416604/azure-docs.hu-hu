---
title: Ismerkedés a Azure Maps Android SDK-val
description: Ismerkedjen meg az Microsoft Azure Maps Android SDK-val. Ismerje meg, hogyan hozhat létre projektet a Android Studioban, hogyan telepítheti az SDK-t, és hogyan hozhat létre interaktív térképet.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531261"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Ismerkedés a Azure Maps Android SDK-val

A Azure Maps Android SDK az Androidhoz készült vektoros Térkép-könyvtár. Ez a cikk végigvezeti a Azure Maps Android SDK telepítésének és a Térkép betöltésének folyamatán.

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.
A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).
3. [Töltse le és telepítse a Google Android Studio](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Projekt létrehozása Android Studio

Android Studio projekt létrehozásához hajtsa végre a következő lépéseket:

1. Android Studio elindítása.
2. Kattintson az **+ új projekt létrehozása** lehetőségre.
3. A **telefon és a tábla** lapon kattintson az **üres tevékenység** elemre. Kattintson a **Tovább** gombra.
4. A **projekt konfigurálása** területen válassza ki `API 21: Android 5.0.0 (Lollipop)` a minimális SDK-t.
5. Válassza ki `Java` a nyelvet.
6. Fogadja el az alapértelmezett értéket `Name` a projekthez. Kattintson a **Finish** (Befejezés) gombra.

A Android Studio telepítésével és új projekt létrehozásával kapcsolatos további segítségért tekintse meg a [Android Studio dokumentációját](https://developer.android.com/studio/intro/) .

![Projekt létrehozása az Android Studióban ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Eszköz beállítása

Ha tesztelni szeretné az alkalmazást a fejlesztés során, Android-telefont vagy Android-emulátort is használhat.

A AVD (androidos virtuális eszköz) beállításával kapcsolatos további tudnivalókért tekintse meg a [Android Studio dokumentációját](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>A Azure Maps Android SDK telepítése

Az alkalmazás létrehozásának következő lépése a Azure Maps Android SDK telepítése.

Az SDK telepítéséhez hajtsa végre a következő lépéseket:

1. A projekt lapon bontsa ki a **Gradle-parancsfájlok** elemet. Nyissa meg a **Build. gradle (projekt: My_Application)** szakaszt, és adja hozzá a következő kódot a **minden projekthez** `repositories`  szakasz:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Nyissa meg a **Build. gradle (modul: My_Application)**.

3. Győződjön meg arról **minSdkVersion** , hogy a `defaultConfig` szakasz minSdkVersion értéke 21 vagy újabb.

4. Adja hozzá a következő kódot az Android szakaszhoz:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Adja hozzá a következő kódot a `dependencies` szakaszhoz:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. A fő eszköztáron kattintson a **fájl** elemre, majd válassza a **Gradle-fájlokkal rendelkező szinkronizálási projekt** lehetőséget.

7. Nyissa meg a következő fájlt: `res > layout > activity_main.xml`. Kattintson a `Code` jobb felső sarokban látható Nézet elemre. Adja hozzá a következő XML-t a `<androidx.constraintlayout.widget.ConstraintLayout>` elemhez.
    
    ```XML
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

8. A `java > com.example.myapplication > MainActivity.java` fájlban a következőket kell tennie:

    * importálja a Azure Maps SDK-t.
    * adja meg a Azure Maps hitelesítési adatait.
    * a Térkép vezérlőelem példányának beolvasása a **onCreate** metódusban.

    Ha nem szeretné, hogy a rendszer minden alkalmazás nézethez adja meg a hitelesítési adatokat, globálisan meghívja a hitelesítési adatokat `AzureMaps.setSubscriptionKey` . Azt is megteheti `AzureMaps.setAadProperties` , hogy Azure Active Directory használatával szeretne hitelesítést végezni.

    A Térkép vezérlőelem felülbírálja a MainActivity osztály következő életciklus-metódusait. Ezek a módszerek az Android OpenGL-életciklusának kezeléséért felelősek.

    * onCreate (csomag)
    * onStart ()
    * onResume()
    * onPause ()
    * onStop ()
    * onDestroy()
    * onSaveInstanceState (csomag)
    * onLowMemory()

    Szerkessze a `MainActivity.java` fájlt a következőképpen:

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

>[!WARNING]
>Előfordulhat, hogy a Android Studio nem importálta a szükséges osztályokat.  Ennek eredményeképpen a kódnak néhány feloldhatatlan-hivatkozása lesz. A szükséges osztályok importálásához egyszerűen vigye az egérmutatót az egyes megoldatlan hivatkozások fölé, és nyomja meg a `Alt + Enter` (Option + Return on a Mac-t).

Android Studio eltarthat néhány másodpercig az alkalmazás felépítéséhez. A Build befejezése után tesztelheti az alkalmazást az emulált Android-eszközön. Ehhez hasonló térképnek kell megjelennie:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps Android-alkalmazásokban":::

## <a name="localizing-the-map"></a>A Térkép honosítása

A Azure Maps Android SDK három különböző módszert biztosít a Térkép nyelvi és területi beállításainak beállításához.

1. Nyelvi és területi beállítások megadása statikus metódusok meghívásával a AzureMaps osztályban.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Adja meg a nyelv és a területi beállításokat a Térkép vezérlőelem XML-kódjában.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Adja meg a nyelvi és a területi beállításokat a Térkép vezérlőelem metódusok meghívásával. Ez a beállítás lehetővé teszi a beállítások módosítását futásidőben.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Itt látható egy példa arra, hogy milyen nyelven van beállítva a Azure Maps `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, a feliratokat ábrázoló Térkép ábrázolása francia nyelven":::

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="navigating-the-map"></a>Navigálás a térképen

A Térkép több különböző módon nagyítható, megpárolt, elforgatható és felhelyezhető. A következő részletek a Térkép különböző módjaira mutatnak.

**A Térkép nagyítása**

- A térképhez koppintson a két ujjal és a csípéssel együtt a nagyításhoz vagy az ujjak egymáshoz közelítéséhez.
- Egy szint nagyításához koppintson duplán a térképre.
- Dupla koppintással két ujjal nagyíthatja ki a térképet egy szinttel.
- Koppintás kétszer; a második koppintásnál tartsa az ujját a térképen, és húzza a nagyításhoz vagy a kicsinyítéshez a nagyításhoz.

**A Térkép pásztázása**

- Érintse meg a térképet, és húzza a kívánt irányba.

**A Térkép elforgatása**

- Érintse meg a térképet két ujjal, és forgassa el.

**A Térkép feldobása**

- Érintse meg a térképet két ujjal, és húzza őket egymáshoz.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan adhat hozzá átfedéses információkat a térképen:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása Android-térképhez](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Térkép stílusainak módosítása Android-térképeken](./set-android-map-styles.md)