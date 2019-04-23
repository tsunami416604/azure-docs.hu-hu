---
title: Oktatóanyag – részletes útmutató Azure térbeli horgonyok használatával új Android-alkalmazás létrehozása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan hozhat létre Azure térbeli horgonyok használatával új Android-alkalmazás.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64f78b04d433c81302499addf15c3d19621bbf9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788343"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Oktatóanyag: Azure térbeli horgonyok használatával új Android-alkalmazás létrehozásának részletes útmutatóját

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy új Android-alkalmazást, amely ARCore funkció integrálható az Azure térbeli horgonyok.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- A gép egy Windows vagy MacOS rendszerű <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3-as</a>.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">engedélyezve fejlesztői</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">képes ARCore</a> Android-eszközön.

## <a name="getting-started"></a>Első lépések

Indítsa el az Android Studio. Az a **üdvözli Android Studio** ablakban kattintson a **indítsa el az új Android Studio-projekt**. Vagy, ha a projekt már meg van nyitva, válassza ki a **fájl**->**új projekt**.

Az a **új projekt létrehozása** ablak alatt a **telefon és táblagép** válassza **üres tevékenység**, és kattintson a **tovább**. Ezután a **minimális API-szintet**, válassza a `API 26: Android 8.0 (Oreo)`, és ellenőrizze, hogy a **nyelvi** értékre van állítva `Java`. Előfordulhat, hogy módosítani szeretné a projekt nevét és helyét és a csomag nevét. A többi beállítást hagyja őket változatlanul. Kattintson a **Befejezés** gombra. A **összetevőt telepítő** fog futni. Ha elkészült, kattintson a **Befejezés**. Néhány feldolgozása után az Android Studio megnyílik az IDE.

## <a name="trying-it-out"></a>Kipróbálás

Az új alkalmazás teszteléséhez, csatlakoztassa az developer-kompatibilis eszközt egy USB-kábellel a fejlesztői gépére. Kattintson a **futtatása**->**"Alkalmazás" futtatása**. Az a **válassza ki a központi telepítési cél** ablakban válassza ki az eszközt, és kattintson a **OK**. Az Android Studio telepíti az alkalmazást a csatlakoztatott eszközön, és elindítja azt. Most már megjelenik a "Hello World!" megjelenik az alkalmazás fut az eszközön. Kattintson a **futtatása**->**Stop "alkalmazás"**.

## <a name="integrating-arcore"></a>Integrálás _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_ </a> a Google-platform, amellyel a kibővített valóság élményt, az eszköz nyomon pozícióját, helyezi át, és létrehozza a való világból a saját ismeretekkel engedélyezése.

Módosítsa `app\manifests\AndroidManifest.xml` tartalmazza a következő bejegyzések belül gyökeréhez `<manifest>` csomópont. Ez a kódrészlet néhány dolgot:

- Az alkalmazás hozzáférjen az eszköz kamerájának engedélyezi.
- Az alkalmazás csak akkor látható, a Google Play Store a ARCore támogató eszközökre is biztosítja.
- Töltse le és telepítse a ARCore, ha nincs telepítve már, ha telepíti az alkalmazást a Google Play Store konfigurálja azt.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Módosítsa `Gradle Scripts\build.gradle (Module: app)` tartalmazza a következő bejegyzés. Ez a kód biztosítja, hogy az alkalmazás célozza meg benne ARCore 1.5-ös verziójának (támogatása ARCore 1.6-os + Azure térbeli horgonyok a hamarosan elérhető lesz). A módosítás után előfordulhat, hogy értesítést kap a gradle-t lát szinkronizálni: kattintson a **szinkronizálás**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.5.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integrálás _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_ </a> egyszerűen lehet renderelni a kibővített valóság alkalmazások, valósághű 3D jelenetek OpenGL elsajátítása nélkül.

Módosítsa `Gradle Scripts\build.gradle (Module: app)` tartalmazza a következő bejegyzéseket. Ez a kód lehetővé teszi az alkalmazások a nyelvi szerkezetek a Java 8, amely `Sceneform` igényel. Is biztosítani fogja az alkalmazás célok `Sceneform` verzió 1.5-ös, mivel az alkalmazás ARCore verziójának egyeznie kell. A módosítás után előfordulhat, hogy értesítést kap a gradle-t lát szinkronizálni: kattintson a **szinkronizálás**.

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
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.5.0'
    ...
}
```

Nyissa meg a `app\res\layout\activity_main.xml`, és cserélje le a meglévő vállalati Hello Wolrd `<TextView>` a következő ArFragment elemet. Ez a kód a kamera adásának engedélyezése ARCore nyomon követéséhez az eszköz beosztás szerint áthelyezi azt a képernyőn megjelenő miatt.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Ismételt üzembe helyezése](#trying-it-out) az alkalmazást még egyszer ellenőrizni, hogy az eszköz. Ennek során kell megkérdezi, hogy a kamera engedélyek. Amint jóváhagyják, megtekintheti a kamera renderelési hírcsatorna a képernyőn.

## <a name="place-an-object-in-the-real-world"></a>Az objektum helyét a való világból

Hozzunk létre & az alkalmazás használatát egy objektum helyét. Először adja hozzá az alábbi importálásokat be a `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Adja hozzá az alábbi tag változókat a `MainActivity` osztály:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Ezután adja hozzá az alábbi kódot a `app\java\<PackageName>\MainActivity` `onCreate()` metódust. Ez a kód fogja kapcsolni egy figyelő nevű `handleTap()`, amikor a felhasználó koppint a képernyőn az eszközön észleléséhez használni. Ha a koppintson kell ARCore a nyomon követési által felismerhető valós felületen történik, a figyelő fog futni.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Végül adja hozzá a következő `handleTap()` módszer, amely mindent lesz alkalmazássá. Hozzon létre egy sphere lesz, és helyezze el a lapfüléhez helyét. A sphere kezdetben óta fekete `this.recommendedSessionProgress` most nullára van beállítva. Ezt az értéket később kell beállítani.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Ismételt üzembe helyezése](#trying-it-out) az alkalmazást még egyszer ellenőrizni, hogy az eszköz. Ezúttal áthelyezheti körül az eszköz indítása, a környezet FELISMERVE ARCore beolvasása. Ezután koppintson a képernyő létrehozása, és helyezze el a fekete sphere tetszőleges felületén.

## <a name="attach-a-local-azure-spatial-anchor"></a>Egy helyi Azure térbeli kapcsolati alapot csatolása

Módosítsa `Gradle Scripts\build.gradle (Module: app)` tartalmazza a következő bejegyzés. Ez a kód gondoskodik arról, hogy a tárolók Azure térbeli horgonyok Alkalmazásverzió 1.0.2-es. Mindemellett hivatkozó bármely Azure térbeli horgonyok legújabb verzióját is működnie kell.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

Kattintson a jobb gombbal `app\java\<PackageName>` -> **új**->**Java-osztály**. Állítsa be **neve** való _Első_alkalmazás_, és **szuperosztály** való _android.app.Application_. A többi beállítást hagyja őket változatlanul. Kattintson az **OK** gombra. Fájl neve `MyFirstApp.java` jön létre. Adja hozzá a következő importálási hozzá:

```java
import com.microsoft.CloudServices;
```

Ezután adja hozzá a következő kódot az új `MyFirstApp` osztály, amely biztosítja az Azure térbeli horgonyok inicializálása során az alkalmazás-környezetet.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Most módosíthatja `app\manifests\AndroidManifest.xml` tartalmazza a következő bejegyzés belül gyökeréhez `<application>` csomópont. Ez a kód fogja kapcsolni az alkalmazás osztályának hozott létre az alkalmazásba.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Térjen vissza `app\java\<PackageName>\MainActivity`, adja hozzá a bele az alábbi importálásokat:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Adja hozzá az alábbi tag változókat a `MainActivity` osztály:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Ezután vegyünk fel a következő `initializeSession()` belül metódus az `mainActivity` osztály. Miután nevű, biztosítani fogja az Azure térbeli horgonyok munkamenet létrejött, és az alkalmazás indításakor megfelelően inicializálva.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Most tekintsük környezet igénybe vételét a `initializeSession()` metódus be a `onCreate()` metódus. Ezenkívül fog biztosítható keretek, a kamera adásának küldött Azure térbeli horgonyok SDK-feldolgozás céljából.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Végül adja hozzá az alábbi kódot a `handleTap()` metódust. Ez egy helyi Azure térbeli kapcsolati alapot csatolása a fekete sphere, amely azt a való világból elhelyezése.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Ismételt üzembe helyezése](#trying-it-out) az alkalmazást még egyszer. Navigálás az eszközt, koppintson a képernyő, és helyezze el egy fekete sphere. Ennek során, a kód létrehozásához és egy helyi Azure térbeli kapcsolati alapot csatolása a sphere.

A folytatás előtt minden, szüksége lesz egy Azure térbeli horgonyokat létrehozni fiók azonosítóját és kulcsát, ha már nincs rájuk. Hajtsa végre a következő szakaszban azok beszerzési.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>A helyi forráshorgony feltöltése a felhőbe

Miután a térbeli horgonyok Azure-fiók azonosítója és kulcsa, hogy is lépjen vissza az `app\java\<PackageName>\MainActivity`, adja hozzá a bele az alábbi importálásokat:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Adja hozzá az alábbi tag változókat a `MainActivity` osztály:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Ezután adja hozzá az alábbi kódot a `initializeSession()` metódust. Először ezt a kódot, nyomon követheti az alkalmazás lehetővé teszi, hogy az Azure térbeli horgonyok SDK lehetővé teszi, keretek gyűjti a kamera adásának. Mint a sphere színét indul el az eredeti fekete, a szürke történő módosítása. Azt fogja kapcsolja fehér után elég keretek gyűjtött a forráshorgony a felhőbe való elküldéséhez. Ez a kód második, a felhő háttérrendszere folytatott kommunikációhoz szükséges a hitelesítő adatokat biztosít. Itt látható, ahol konfigurálhatja az alkalmazás a fiók azonosítóját és kulcsát használjuk. Másolja őket egy szövegszerkesztőbe, amikor [beállítása a térbeli horgonyok erőforrás](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Ezután adja hozzá a következő `uploadCloudAnchorAsync()` belül metódus az `mainActivity` osztály. Miután nevű, ezt a módszert aszinkron módon megvárja, amíg elegendő keretek gyűjtött az eszközről. Amint történik, a sphere színe átvált a sárga, és majd el is indítja a helyi Azure térbeli Forráshorgony feltöltését a felhőbe. Miután a feltöltés befejezését követően a kód a horgony azonosítója adja vissza.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Végezetül hozzunk környezet igénybe vételét minden együtt. Az a `handleTap()` metódust, adja hozzá a következő kódot. Ez meghívja a `uploadCloudAnchorAsync()` metódus, amint a sphere jön létre. Ha a metódus visszatért, az alábbi kód elvégzi a sphere, a kék szín módosítása egy utolsó frissítése.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Ismételt üzembe helyezése](#trying-it-out) az alkalmazást még egyszer. Navigálás az eszközt, koppintson a képernyő, és helyezze el a sphere. Megadott idő, a sphere annak színe megváltozik fekete-fehér felé, a kamera keretek gyűjtött. Amint rendelkezésünkre áll elég keretek, a sphere sárga be fog kapcsolni, és a felhő feltöltés indul. Miután a feltöltés befejezését követően a sphere kék fog kapcsolni. Igény szerint is használhatja a `Logcat` ablak belül a naplózási üzenetek figyeléséhez Android Studio az alkalmazás által küldött. Például a munkamenet folyamata alatt rögzíti, és a horgony azonosítója, győződjön meg arról, hogy a felhő egyszer a feltöltés vissza befejeződött.

## <a name="locate-your-cloud-spatial-anchor"></a>Keresse meg a felhő térbeli forráshorgony

A horgony egy feltöltött a felhőbe, készen állunk való elhelyezése ismét megkísérli. Első lépésként adjunk hozzá az alábbi importálásokat elhelyezni a kódban.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Ezután adjunk hozzá az alábbi kódot a `handleTap()` metódust. Ez a kód fogja végrehajtani:

- Távolítsa el a meglévő kék sphere a képernyőn.
- Inicializálja újra az Azure térbeli horgonyok munkamenet. Ez a művelet biztosítja, hogy a horgony, keresse meg fogjuk származik-e helyett a helyi jegyzetobjektum hoztunk létre a felhőben.
- Adjon ki egy lekérdezést a horgony, hogy feltöltött a felhőbe.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Most tekintsük környezet igénybe vételét a kódot, amely akkor kell meghívni, ha azt a lekérdezésekor a horgony helye. Belül a `initializeSession()` metódust, adja hozzá a következő kódot. Ez a kódrészlet fog & létrehozása után a cloud térbeli jegyzetobjektum megtalálható, helyezze el egy zöld sphere. Azt is engedélyezheti a képernyőn koppintson újra, így a teljes forgatókönyv még egyszer ismételje meg: hozzon létre egy másik helyi forráshorgony, töltse fel, és keresse meg újra.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Ennyi az egész! [Ismételt üzembe helyezése](#trying-it-out) az egyik alkalmazás utolsó időpont, amikor a teljes forgatókönyv teljes körű kipróbálhatja. Navigálás az eszközt, és helyezze el a fekete sphere. Ezt követően az eszköz kamerájának keretek rögzítéséhez, mindaddig, amíg a sphere sárga színűre váltott tartsa áthelyezése. A helyi forráshorgony fel lesz töltve, és a sphere kék fog kapcsolni. Végül koppintson a képernyő még egyszer, hogy a helyi forráshorgony törlődik, és majd azt fogja lekérdezni a felhő párjukhoz. Továbbra is a Navigálás az eszközt, amíg a térbeli felhőbeli horgony nem található. A megfelelő helyre meg kell jelennie egy zöld sphere, és le, és ismételje meg újra a teljes forgatókönyvet.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
