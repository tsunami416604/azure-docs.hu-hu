---
title: 'Oktatóanyag: Új Android-alkalmazás létrehozása'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre új Android-alkalmazást az Azure Spatial Anchors használatával.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1773ef81a5b727187a9a69ccc7ce7ad0421fb2c
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80246772"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Oktatóanyag: Lépésenkénti útmutató egy új Android-alkalmazás létrehozásához az Azure Spatial Anchors használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy új Android-alkalmazást, amely integrálja az ARCore-funkciókat az Azure Spatial Anchors szolgáltatással.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Windows vagy macOS rendszerű készülék <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+ rendszerrel.</a>
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztő engedélyezve és</a> <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore képes</a> Android készülék.

## <a name="getting-started"></a>Első lépések

Indítsa el az Android Studio alkalmazást. Az **Üdvözli** az Android Studio ablakban kattintson **az Új Android Studio projekt indítása**elemre. Ha már meg nyitott egy projektet, válassza az**Új projekt** **fájlja**->lehetőséget.

Az **Új projekt létrehozása** ablak **Telefon és táblagép** csoportban válassza a Tevékenység **kiürítése**lehetőséget, majd kattintson a **Tovább**gombra. Ezután a **Minimális API szint**csoportban válassza `Java`a `API 26: Android 8.0 (Oreo)`lehetőséget, és győződjön meg arról, hogy a **nyelv** beállítása a. Előfordulhat, hogy módosítani szeretné a Projekt nevét & a helyet, valamint a csomag nevét. Hagyja meg a többi lehetőséget, ahogy vannak. Kattintson a **Befejezés** gombra. Az **összetevő-telepítő** futni fog. Ha ez megtörtént, kattintson **a Befejezés gombra.** Némi feldolgozás után az Android Studio megnyitja az IDE-t.

## <a name="trying-it-out"></a>Próbálja ki

Az új alkalmazás teszteléséhez csatlakoztassa a fejlesztői eszközét a fejlesztői géphez USB-kábellel. Kattintson **a Futtatás**->**"alkalmazás" elemre.** A **Telepítési cél kiválasztása** ablakban jelölje ki az eszközt, majd kattintson az **OK**gombra. Az Android Studio telepíti az alkalmazást a csatlakoztatott eszközre, és elindítja azt. Most látnia kell a "Hello World!" megjelenik az eszközön futó alkalmazásban. Kattintson a->**"Alkalmazás" leállítása gombra.** **Run**

## <a name="integrating-_arcore_"></a>Az _ARCore integrálása_

<a href="https://developers.google.com/ar/discover/" target="_blank">_Az ARCore_</a> a Google platformja a kiterjesztett valóság élményeinek építéséhez, lehetővé téve a készülék számára, hogy nyomon kövesse pozícióját, miközben mozog, és saját tudását építi a valós világról.

Módosítsa `app\manifests\AndroidManifest.xml` a következő bejegyzéseket `<manifest>` a gyökércsomópontba. Ez a kódrészlet néhány dolgot tesz:

- Ez lehetővé teszi az alkalmazás számára, hogy hozzáférjen a készülék kamerájához.
- Azt is biztosítja, hogy az alkalmazás csak az ARCore-t támogató eszközökön látható legyen a Google Play Áruházban.
- Ez beállítja a Google Play Áruház at letölteni és telepíteni ARCore, ha még nincs telepítve, amikor az alkalmazás telepítve van.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Módosítsa `Gradle Scripts\build.gradle (Module: app)` a következő bejegyzéssel. Ez a kód biztosítja, hogy az alkalmazás az ARCore 1.8-as verzióját célozza meg. A módosítás után értesítést kaphat a Gradle-től, amelyben a szinkronizálást kéri: kattintson a **Szinkronizálás most**gombra.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>_Jelenetűrlap integrálása_

[_A Sceneform_](https://developers.google.com/sceneform/develop/) egyszerűvé teszi a valósághű 3D-s jelenetek renderelését az Augmented Reality alkalmazásokban, anélkül, hogy meg kellene tanulnia az OpenGL-t.

Módosítsa `Gradle Scripts\build.gradle (Module: app)` a következő bejegyzéseket is. Ez a kód lehetővé teszi az alkalmazás számára, hogy a Java 8-ból származó nyelvi konstrukciókat használjon, ehhez `Sceneform` szükséges. Azt is biztosítja, `Sceneform` hogy az alkalmazás az 1.8-as verziót célozza meg, mivel meg kell egyeznie az alkalmazás által használt ARCore verziójával. A módosítás után értesítést kaphat a Gradle-től, amelyben a szinkronizálást kéri: kattintson a **Szinkronizálás most**gombra.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Nyissa `app\res\layout\activity_main.xml`meg a , és `<TextView>` cserélje le a meglévő Hello Wolrd elemet a következő ArFragment. Ez a kód azt eredményezi, hogy a kamera képcsatornája megjelenik a képernyőn, lehetővé téve az ARCore számára, hogy nyomon kövesse az eszköz helyzetét mozgás közben.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Telepítse újra](#trying-it-out) az alkalmazást az eszközre, hogy még egyszer ellenőrizze azt. Ezúttal meg kell kérni a kamera engedélyeket. A jóváhagyást követően látnia kell a kamerakép renderelését a képernyőn.

## <a name="place-an-object-in-the-real-world"></a>Tárgy elhelyezése a való világban

Hozzunk létre & objektumot helyezzen el az alkalmazás használatával. Először adja hozzá a `app\java\<PackageName>\MainActivity`következő importot a következőbe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Ezután adja hozzá a következő `MainActivity` tagváltozókat az osztályhoz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Ezután adja hozzá a `app\java\<PackageName>\MainActivity` `onCreate()` következő kódot a metódushoz. Ez a kód összeköt egy `handleTap()`figyelőt, a neve , amely észleli, ha a felhasználó megérinti a képernyőt az eszközön. Ha a koppintás olyan valós felületen van, amelyet az ARCore nyomon követése már felismert, a figyelő futni fog.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Végül, adjuk `handleTap()` hozzá a következő módszert, amely összeköti mindent. Létrehoz egy gömböt, és a megérintett helyre helyezi. A gömb kezdetben fekete `this.recommendedSessionProgress` lesz, mivel most nullára van állítva. Ezt az értéket később módosítja a beállítás.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Telepítse újra](#trying-it-out) az alkalmazást az eszközre, hogy még egyszer ellenőrizze azt. Ezúttal mozoghat az eszközön, hogy az ARCore elkezdhesse felismerni a környezetet. Ezután érintse meg a képernyőt, hogy hozzon létre & helyezze a fekete gömb a felszínen az Ön által kiválasztott.

## <a name="attach-a-local-azure-spatial-anchor"></a>Helyi Azure térbeli horgony csatolása

Módosítsa `Gradle Scripts\build.gradle (Module: app)` a következő bejegyzéssel. Ez a kód biztosítja, hogy az alkalmazás az Azure Spatial Anchors 2.2.0-s verzióját célozza meg. Ez azt mondta, az Azure Spatial Anchors bármely friss verziójára való hivatkozásnak működnie kell. A kibocsátási megjegyzéseket [itt találja.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Kattintson `app\java\<PackageName>` ->a jobb gombbal **az Új**->**Java osztály elemre.** Állítsa **a Nevet** a _MyFirstApp_és a **Superclass** az _android.app.Application alkalmazásra._ Hagyja meg a többi lehetőséget, ahogy vannak. Kattintson az **OK** gombra. Létrejön `MyFirstApp.java` egy nevű fájl. Adja hozzá a következő importálást:

```java
import com.microsoft.CloudServices;
```

Ezután adja hozzá a `MyFirstApp` következő kódot az új osztályban, amely biztosítja az Azure Spatial Anchors inicializálása az alkalmazás környezetében.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Most módosítsa `app\manifests\AndroidManifest.xml` a következő bejegyzést a `<application>` gyökércsomóponton belül. Ez a kód csatlakoztatja az alkalmazáshoz létrehozott alkalmazásosztályt.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Vissza `app\java\<PackageName>\MainActivity`a alkalmazásban adja hozzá a következő behozatalt:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Ezután adja hozzá a következő `MainActivity` tagváltozókat az osztályhoz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Ezután adjuk hozzá a `initializeSession()` következő `mainActivity` módszert az osztályodhoz. A megnevezett, biztosítja az Azure Spatial Anchors munkamenet jön létre, és megfelelően inicializált indításakor az alkalmazás.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Most, nézzük horog `initializeSession()` a `onCreate()` módszer a módszer a módszer. Emellett gondoskodunk arról is, hogy a kameraképből származó képkockák feldolgozásra az Azure Spatial Anchors SDK-ba kerüljön.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Végül adja hozzá a `handleTap()` következő kódot a metódushoz. Ez csatolja a helyi Azure Spatial Anchor a fekete gömb, hogy mi vagyunk forgalomba a valós világban.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Telepítse újra](#trying-it-out) az alkalmazást még egyszer. Mozoghat a készüléken, koppintson a képernyőre, és helyezzen el egy fekete gömböt. Ezúttal azonban a kód egy helyi Azure Spatial Anchor létrehozása és csatolása lesz a gömbhöz.

Mielőtt továbbhaladna, létre kell hoznia egy Azure Spatial Anchors-fiókazonosítót és kulcsot, ha még nem rendelkezik velük. A beszerzésükhöz kövesse a következő szakaszt.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Töltse fel a helyi horgonyt a felhőbe

Miután rendelkezik az Azure Spatial Anchors fiókazonosítójának és kulcsának, visszatérhetünk a programba, `app\java\<PackageName>\MainActivity`és hozzáadhatjuk a következő importálást:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Ezután adja hozzá a következő `MainActivity` tagváltozókat az osztályhoz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Most adja hozzá a `initializeSession()` következő kódot a módszerhez. Először is, ez a kód lehetővé teszi, hogy az alkalmazás figyelheti az Azure Spatial Anchors SDK által a képcsatorna keretét gyűjtő folyamat előrehaladását. A hogy ez nem, a szín a gömb kezd változni az eredeti fekete, a szürke. Ezután fehérré válik, amint elegendő képkocka kerül összeadására, hogy beküldje a horgonyt a felhőbe. Másodszor, ez a kód biztosítja a felhő alapú háttér-szolgáltatással való kommunikációhoz szükséges hitelesítő adatokat. Itt állíthatja be az alkalmazást a fiókazonosító és a kulcs használatára. A [Térbeli horgonyok erőforrás beállításakor](#create-a-spatial-anchors-resource)szövegszerkesztőbe másolta őket.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Ezután adja `uploadCloudAnchorAsync()` hozzá a `mainActivity` következő módszert az osztályon belül. A megnevezett módszer aszinkron módon megvárja, amíg elegendő képkocka kerül összegyűjtésre az eszközről. Amint ez megtörténik, a gömb színét sárgára váltja, majd megkezdi a helyi Azure Spatial Anchor feltöltését a felhőbe. A feltöltés befejezése után a kód egy horgonyazonosítót ad vissza.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Végül, kössünk össze mindent. A `handleTap()` módszerben adja hozzá a következő kódot. Ez fogja `uploadCloudAnchorAsync()` meghívni a módszert, amint a gömb jön létre. Miután a metódus visszatér, az alábbi kód egy utolsó frissítést hajt végre a gömbön, és a színét kékre változtatja.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Telepítse újra](#trying-it-out) az alkalmazást még egyszer. Mozoghat a készüléken, koppintson a képernyőre, és helyezze el a gömböt. Ezúttal azonban a gömb színe fekete ről fehérre változik, mivel a kamerakereteket gyűjtik. Amint elegendő keretünk lesz, a gömb sárgává változik, és a felhőfeltöltés elindul. Amint a feltöltés befejeződik, a gömb kékre vált. Opcionálisan az `Logcat` Android Studio ablakát is használhatja az alkalmazás által küldött naplóüzenetek figyelésére. Például a munkamenet előrehaladása a keretrögzítés során, és a horgonyazonosító, amelyet a felhő a feltöltés befejezése után ad vissza.

## <a name="locate-your-cloud-spatial-anchor"></a>Keresse meg a felhőtérbeli horgonyt

Az egyik a horgony feltöltött a felhőbe, készen állunk, hogy megpróbálja megtalálni újra. Először adjuk hozzá a következő importálást a kódhoz.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Ezután adjuk hozzá a következő `handleTap()` kódot a módszerhez. Ez a kód:

- Távolítsuk el a meglévő kék gömböt a képernyőről.
- Inicializálja újra az Azure Spatial Anchors munkamenetet. Ez a művelet biztosítja, hogy a horgony fogunk megtalálni származik a felhő helyett a helyi horgonyt hoztunk létre.
- Adjon ki lekérdezést a felhőbe feltöltött horgonyhoz.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Most csatlakoztassa a kódot, amely akkor lesz meghívva, amikor a lekérdező horgony található. A `initializeSession()` módszeren belül adja hozzá a következő kódot. Ez a kódrészlet létrehoz & zöld gömböt helyez el, amint a felhő térbeli horgony található. Azt is lehetővé teszi a képernyő megérinti újra, így ismételje meg az egész forgatókönyvet még egyszer: hozzon létre egy másik helyi horgony, töltse fel, és keresse meg újra.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Ennyi az egész! [Telepítse újra](#trying-it-out) az alkalmazást még egyszer utoljára, hogy kipróbálja a teljes forgatókönyvet. Mozogjon a készüléken, és helyezze el a fekete gömböt. Ezután mozgassa a készüléket, hogy rögzítse a kamerakereteket, amíg a gömb sárgára nem vált. A helyi horgony lesz feltöltve, és a gömb kéklesz. Végül érintse meg még egyszer a képernyőt, hogy a helyi horgony törlődjön, majd lekérdezzük a felhőbeli megfelelőjét. Folytassa az eszköz mozgatásával, amíg a felhőbeli térbeli horgony meg nem található. A zöld gömbnek a megfelelő helyen kell megjelennie, és öblítheti & megismételheti az egész forgatókönyvet.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
