---
title: 'Oktatóanyag: új Android-alkalmazás létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy új Android-alkalmazást az Azure térbeli horgonyok használatával.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c02595ebdb2f011dcc94b517771a79a4b2df754d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161720"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Oktatóanyag: lépésenkénti útmutató új Android-alkalmazás létrehozásához az Azure térbeli horgonyok használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy új Android-alkalmazást, amely integrálja az ARCore funkciókat az Azure térbeli Horgonyokkal.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Windows vagy macOS rendszerű számítógép, <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.

## <a name="getting-started"></a>Bevezetés

Indítsa el az Android Studio. Az **üdvözli Android Studio** ablakban kattintson az **új Android Studio projekt indítása**lehetőségre. Ha már meg van nyitva egy projekt, válassza a **fájl**->**új projekt**lehetőséget.

Az **új projekt létrehozása** ablakban a **telefon és a tábla** területen válassza az **üres tevékenység**lehetőséget, majd kattintson a **tovább**gombra. Ezután a **minimális API-szint**területen válassza a `API 26: Android 8.0 (Oreo)`lehetőséget, és győződjön meg arról, hogy a **nyelv** beállítása `Java`. Érdemes lehet módosítani a projekt nevét & helyet és a csomag nevét. Hagyja meg a többi beállítást. Kattintson a **Finish** (Befejezés) gombra. Ekkor elindul az **összetevő-telepítő** . Ha elkészült, kattintson a **Befejezés**gombra. Néhány feldolgozás után a Android Studio megnyitja az IDE-t.

## <a name="trying-it-out"></a>Kipróbálás

Az új alkalmazás teszteléséhez csatlakoztassa a fejlesztői eszközöket a fejlesztői számítógéphez USB-kábellel. Kattintson a **Futtatás** gombra->**futtassa az "app"** parancsot. A **telepítési cél kiválasztása** ablakban válassza ki az eszközt, majd kattintson **az OK**gombra. Android Studio telepíti az alkalmazást a csatlakoztatott eszközön, és elindítja azt. Ekkor megjelenik a ""Helló világ!"alkalmazás!" az eszközön futó alkalmazásban jelenik meg. Kattintson a **futtatás**-> **"alkalmazás leállítása"** elemre.

## <a name="integrating-_arcore_"></a>A _ARCore_ integrálása

A <a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> a Google platformja a kibővített valósággal kapcsolatos tapasztalatok kiépítése érdekében, amely lehetővé teszi, hogy az eszköz nyomon követhesse az általa áthelyezett pozíciót, és létrehozza a valós világ saját megértését.

Módosítsa `app\manifests\AndroidManifest.xml` úgy, hogy a legfelső `<manifest>` csomóponton belül szerepeljenek a következő bejegyzések. Ez a kódrészlet néhány dolgot tartalmaz:

- Lehetővé teszi az alkalmazás számára az eszköz kamerájának elérését.
- Azt is biztosítja, hogy az alkalmazás csak a ARCore támogató eszközök Google Play Áruház látható legyen.
- A Google Play Áruház a ARCore letöltésére és telepítésére konfigurálja, ha már nincs telepítve az alkalmazás telepítésekor.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Módosítsa `Gradle Scripts\build.gradle (Module: app)` a következő bejegyzés belefoglalásával. Ez a kód biztosítja, hogy az alkalmazás az 1,8-es ARCore-verziót célozza meg. A módosítás után értesítést kaphat a szinkronizálást kérő Gradle: kattintson a **szinkronizálás most**elemre.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>A _Sceneform_ integrálása

A <a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> megkönnyíti a valósághű 3D-jelenetek megjelenítését a kibővített Reality-alkalmazásokban anélkül, hogy az OpenGL megismerése nélkül kellene megtanulnia.

Módosítsa `Gradle Scripts\build.gradle (Module: app)` a következő bejegyzések belefoglalásához. Ez a kód lehetővé teszi, hogy az alkalmazás a Java 8 nyelvi szerkezetét használja, amelyhez `Sceneform` szükséges. Azt is biztosítja, hogy az alkalmazás a 1,8-es verzióban `Sceneform`, mivel az alkalmazás által használt ARCore-verziónak meg kell egyeznie. A módosítás után értesítést kaphat a szinkronizálást kérő Gradle: kattintson a **szinkronizálás most**elemre.

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

Nyissa meg a `app\res\layout\activity_main.xml`, és cserélje le a meglévő Hello Wolrd `<TextView>` elemet a következő ArFragment. Ez a kód azt eredményezi, hogy a kamera hírcsatornája megjelenik a képernyőn, így a ARCore nyomon követheti az eszköz pozícióját.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Telepítse](#trying-it-out) újra az alkalmazást az eszközére, hogy még egyszer érvényesítse. Ekkor meg kell adnia a kamera engedélyeit. A jóváhagyást követően látnia kell a kamera hírcsatornájának megjelenítését a képernyőn.

## <a name="place-an-object-in-the-real-world"></a>Egy objektum elhelyezése a valós világban

Hozzuk létre & helyezzen egy objektumot az alkalmazás használatával. Először adja hozzá a következő importálásokat a `app\java\<PackageName>\MainActivity`ba:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Ezután adja hozzá a következő tag-változókat a `MainActivity` osztályhoz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Ezután adja hozzá a következő kódot a `app\java\<PackageName>\MainActivity` `onCreate()` metódushoz. Ez a kód egy `handleTap()`nevű figyelőt fog összekapcsolni, amely akkor fogja felderíteni, ha a felhasználó az eszközön koppint a képernyőre. Ha a koppintás úgy történik, hogy egy valós, a ARCore által már ismert felszínen fut, a figyelő futni fog.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Végül adja hozzá a következő `handleTap()` metódust, amely összekapcsolja az összes együtt. Létrehoz egy gömbet, és elhelyezi a kitalált helyen. A gömb kezdetben feketévé válik, mert `this.recommendedSessionProgress` nullára van állítva. Ez az érték később lesz módosítva.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Telepítse](#trying-it-out) újra az alkalmazást az eszközére, hogy még egyszer érvényesítse. Ezúttal áthelyezheti az eszközt, hogy ARCore a környezet felismerésének megkezdéséhez. Ezután koppintson a képernyőre a létrehozásához & helyezze el a fekete gömbet a választott felületen.

## <a name="attach-a-local-azure-spatial-anchor"></a>Helyi Azure térbeli horgony csatolása

Módosítsa `Gradle Scripts\build.gradle (Module: app)` a következő bejegyzés belefoglalásával. Ez a kód biztosítja, hogy az alkalmazás az Azure térbeli horgonyok 2.2.0-os verzióját célozza meg. Ez azt mondta, hogy az Azure térbeli horgonyok legújabb verziójára hivatkozva kell működni. A kibocsátási megjegyzések [itt találhatók.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Kattintson a jobb gombbal a `app\java\<PackageName>`->**új**->**Java-osztály**elemre. Állítsa a nevet _MyFirstApp_, a **Szuperosztály** pedig az _Android. app. Application_értékre. Hagyja meg a többi beállítást. Kattintson az **OK** gombra. Létrejön egy `MyFirstApp.java` nevű fájl. Adja hozzá a következő importálást:

```java
import com.microsoft.CloudServices;
```

Ezután adja hozzá a következő kódot az új `MyFirstApp` osztályban, amely biztosítja, hogy az Azure térbeli horgonyok inicializálva legyenek az alkalmazás környezetében.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Most módosítsa `app\manifests\AndroidManifest.xml` a következő bejegyzés belefoglalásával a root `<application>` csomóponton belül. Ez a kód összekapcsolja az alkalmazásba létrehozott használati osztályt.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

`app\java\<PackageName>\MainActivity`újra adja hozzá a következő importálásokat:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Ezután adja hozzá a következő tag-változókat a `MainActivity` osztályhoz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Ezután adja hozzá a következő `initializeSession()` metódust a `mainActivity` osztályon belül. A meghívást követően az Azure térbeli horgonyok munkamenetét a rendszer az alkalmazás indításakor hozza létre és megfelelően inicializálja.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Most pedig Ismerkedjen meg `initializeSession()` metódussal a `onCreate()` metódussal. Emellett biztosítjuk, hogy a fényképezőgép-hírcsatornából származó képkockákat a rendszer az Azure térbeli horgonyok SDK-ba küldi a feldolgozáshoz.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Végül adja hozzá a következő kódot a `handleTap()` metódushoz. Egy helyi Azure térbeli horgonyt fog csatolni a valós világban elhelyezett fekete gömbhöz.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

Még egyszer [telepítse](#trying-it-out) újra az alkalmazást. Mozgassa az eszközt, koppintson a képernyőre, és helyezzen el egy fekete gömbet. Ezúttal azonban a kód egy helyi Azure térbeli horgonyt hoz létre és csatol a szférához.

Mielőtt továbblépne, létre kell hoznia egy Azure térbeli horgonyok fiókjának azonosítóját és kulcsát, ha még nem tette meg. A beszerzéséhez kövesse az alábbi szakaszt.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Töltse fel a helyi horgonyt a felhőbe

Ha már rendelkezik az Azure-beli térbeli horgonyok fiókjának azonosítójával és kulcsával, visszatérhet a `app\java\<PackageName>\MainActivity`ba, és hozzáadhatja a következő importálásokat:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Ezután adja hozzá a következő tag-változókat a `MainActivity` osztályhoz:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Most adja hozzá a következő kódot a `initializeSession()` metódushoz. Első lépésként ez a kód lehetővé teszi, hogy az alkalmazás figyelje az Azure térbeli horgonyok SDK által végrehajtott előrehaladást, mivel a begyűjti a képkockákat a kamerás hírcsatornából. Ahogy igen, a gömb színe az eredeti fekete, szürke színre változik. Ezután a rendszer bekapcsolja a fehér keretet, hogy a horgonyt a felhőbe küldje el. Másodszor, ez a kód megadja a Felhőbeli háttérrel való kommunikációhoz szükséges hitelesítő adatokat. Itt konfigurálhatja az alkalmazást, hogy a fiók azonosítóját és kulcsát használja. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Ezután adja hozzá a következő `uploadCloudAnchorAsync()` metódust a `mainActivity` osztályon belül. Ezt követően a metódus aszinkron módon várakozik, amíg az eszközről elegendő keretet nem gyűjt. Amint ez megtörténik, a rendszer a gömb színét sárga értékre cseréli, majd elkezdi feltölteni a helyi Azure térbeli horgonyt a felhőbe. A feltöltés befejeződése után a kód egy szerkesztőpont-azonosítót ad vissza.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Végül Ismerkedjen meg egymással. A `handleTap()` metódusban adja hozzá a következő kódot. A rendszer a gömb létrehozása után azonnal meghívja a `uploadCloudAnchorAsync()` metódust. A metódus visszatérése után az alábbi kód egy végső frissítést hajt végre a szférában, és a színét kékre változtatja.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

Még egyszer [telepítse](#trying-it-out) újra az alkalmazást. Mozgassa az eszközt, koppintson a képernyőre, és helyezze el a gömbjét. Ezúttal azonban a gömb színe feketén fehérre változik, mivel a rendszer begyűjti a fényképezőgép kereteit. Ha elegendő keret áll rendelkezésre, a gömb sárga színűre vált, a Felhőbeli feltöltés pedig elindul. Miután a feltöltés befejeződik, a gömb kék színűre vált. Igény szerint a Android Studio `Logcat` ablakát is használhatja az alkalmazás által küldött naplóüzenetek figyelésére. Például a munkamenet előrehaladása a keret rögzítése során és a felhő által a feltöltés befejeződése után visszaadott szerkesztőpont-azonosító.

## <a name="locate-your-cloud-spatial-anchor"></a>A Felhőbeli térbeli horgony megkeresése

A rendszer feltölti az egyik horgonyt a felhőbe, készen áll arra, hogy újra megpróbálja megkeresni. Először adja hozzá a következő importálásokat a kódjához.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Ezután adja hozzá a következő kódot a `handleTap()` metódushoz. A kód a következőket teszi:

- Távolítsa el a meglévő kék gömbet a képernyőről.
- Inicializálja az Azure térbeli horgonyok munkamenetét. Ez a művelet biztosítja, hogy a keresett horgony a felhőből származik, a létrehozott helyi horgony helyett.
- Adjon meg egy lekérdezést a felhőbe feltöltött horgonyhoz.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Most pedig Ismerkedjen meg a kóddal, amely akkor fog megjelenni, amikor a lekérdezett horgony található. A `initializeSession()` metóduson belül adja hozzá a következő kódot. Ez a kódrészlet létrehoz & egy zöld gömbet, miután a Felhőbeli térbeli horgony található. Emellett ismét lehetővé teszi a képernyő koppintását, így még egyszer megismételheti az egész forgatókönyvet: hozzon létre egy másik helyi horgonyt, töltse fel, majd keresse meg újra.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Ennyi az egész! [Telepítse](#trying-it-out) újra az alkalmazást, és fejezze be a teljes forgatókönyv végét. Helyezze át az eszközt, és helyezze a fekete gömbbe. Ezután ne helyezze át az eszközt úgy, hogy rögzítse a kamera-kereteket, amíg a gömb sárga színűvé nem válik. A rendszer feltölti a helyi horgonyt, és a gömb kék színűre vált. Végül koppintson még egyszer a képernyőre, hogy a helyi horgony el legyen távolítva, majd lekérdezjük a Felhőbeli megfelelőjét. Folytassa az eszköz áthelyezését, amíg a Felhőbeli térbeli horgony nem található. Egy zöld gömbnek a megfelelő helyen kell megjelennie, és kiöblítheti & ismételje meg a teljes forgatókönyvet.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
