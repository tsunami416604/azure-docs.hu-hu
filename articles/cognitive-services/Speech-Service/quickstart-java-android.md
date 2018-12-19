---
title: 'Gyors útmutató: Ismeri fel a beszéd, a Java (Android) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Megismerheti a beszédfelismerés használatát Androidon, Java nyelven a Speech Service SDK segítségével
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 6d245b457eca78dc029bde923616b4d84e04b997
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599015"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerést a Java Android rendszeren a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből elsajátíthatja fogja, hogyan hozhat létre egy Java-alkalmazást az Android a Cognitive Services beszédfelismerő SDK a beszédfelismerés lefényképezze.
Az alkalmazás a Microsoft Cognitive Services beszédfelismerő SDK Maven csomag, az 1.2.0-s vagy annál újabb verzió és az Android Studio 3.1 alapul.
A Speech SDK jelenleg a 32/64 bites ARM, vagy Intel x86/x64 processzorokat használó Android-eszközökkel kompatibilis.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége van a Speech Service előfizetői azonosítójára, amelyet ingyenesen is beszerezhet. További részletekért tekintse át a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

1. Indítsa el az Android Studio alkalmazást, majd az üdvözlőablakban válassza a **Start a new Android Studio project** (Új Android Studio-projekt indítása) elemet.

    ![Az Android Studio üdvözlőablakának képernyőképe](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Megnyílik a **Create New Project** (Új projekt létrehozása) varázsló. A **Create Android Project** (Android-projekt létrehozása) képernyőn adja meg a **Quickstart** (Rövid útmutató) értéket az **alkalmazás neveként**, a **samples.speech.cognitiveservices.microsoft.com** legyen a **vállalati tartomány**, és válasszon egy projektkönyvtárat. A C++ és a Kotlin jelölőnégyzeteket ne jelölje be, és kattintson a **Next** (Tovább) gombra.

   ![A Create New Project (Új projekt létrehozása) varázsló képernyőképe](media/sdk/qs-java-android-02-create-android-project.png)

1. A **Target Android Devices** (Android-céleszközök) képernyőn csak a **Phone and Tablet** (Telefon és táblagép) lehetőséget jelölje be. Alatta a legördülő listában válassza ki a **API 23: Android 6.0 (marshmallow rendszert)**, és válassza ki **tovább**.

   ![A Create New Project (Új projekt létrehozása) varázsló képernyőképe](media/sdk/qs-java-android-03-target-android-devices.png)

1. Az **Add an Activity to Mobile** (Tevékenység hozzáadása mobileszközhöz) képernyőn válassza az **Empty Activity** (Üres tevékenység) elemet, majd kattintson a **Next** (Tovább) gombra.

   ![A Create New Project (Új projekt létrehozása) varázsló képernyőképe](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. A **Configure Activity** (Tevékenység konfigurálása) képernyőn adja meg a **MainActivity** tevékenység-, és az **activity\_main** elrendezésnevet. Jelölje be mindkét jelölőnégyzetet, majd kattintson a **Finish** (Befejezés) gombra.

   ![A Create New Project (Új projekt létrehozása) varázsló képernyőképe](media/sdk/qs-java-android-05-configure-activity.png)

Az Android Studio előkészíti az új Android-projektet. Ezután úgy kell konfigurálnia a projektet, hogy az képes legyen használni a Speech SDK-t és a Java 8-at.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.2.0`.

A beszédfelismerés SDK for Android van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak és Android-engedélyek megadása kötelező.
Egy Maven-adattárban üzemel, a következő helyen: https://csspeechstorage.blob.core.windows.net/maven/.

Készítse elő a projektet a Speech SDK használatára. A Project Structure (Projektstruktúra) ablak megnyitásához az Android Studio menüsávján válassza a **File (Fájl)** > **Project Structure (Projektstruktúra)** elemet. A Project Structure (Projektstruktúra) ablakban hajtsa végre a következő módosításokat:

1. Az ablak bal oldalán található listából válassza ki a **Project** (Projekt) elemet. A **Default Library Repository** (Kódtár alapértelmezett adattára) értékéhez fűzzön hozzá egy vesszőt, majd a Maven-adattár aposztrófok közé zárt URL-címét. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ugyanezen képernyő bal oldalán válassza ki az **alkalmazást**. Ezután kattintson a **Dependencies** (Függőségek) fülre az ablak felső részén. Kattintson a zöld plusz jelre (+), majd a legördülő menüből válassza a **Library dependency** (Kódtárfüggőség) elemet.

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](media/sdk/qs-java-android-07-add-module-dependency.png)

1. A megnyíló ablakban adja meg az androidos Speech SDK nevét és verzióját: `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`. Ezután kattintson az **OK** gombra.
   A Speech SDK-nak ezek után meg kell jelennie a függőségek listáján, a következő módon:

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Válassza a **Properties** (Tulajdonságok) lapot. A **Source Compatibility** (Forráskompatibilitás) és a **Target Compatibility** (Célkompatibilitás) beállításnál is az **1.8** értéket válassza.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Az **OK** gombra kattintva zárja be a Project Structure (Projektstruktúra) ablakot, és alkalmazza a módosításokat a projektre.

## <a name="create-user-interface"></a>A felhasználói felület létrehozása

Létrehozunk egy alapvető felhasználói felületet az alkalmazáshoz. Kezdje el szerkeszteni a fő tevékenység (`activity_main.xml`) elrendezését. Kezdetben az elrendezés az alkalmazás nevével és a egy "Hello World!" szöveget tartalmazza TextView címsor magában foglalja.

* Kattintson a TextView elemre. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `hello`.

* Az `activity_main.xml` ablak bal felső részén található Palette (Paletta) részről húzzon át egy gombot a szöveg fölötti üres részre.

* A gomb attribútumai jobb oldalon láthatók. Az `onClick` attribútum értékét állítsa a következőre: `onSpeechButtonClicked`. Ezen a néven írni fogunk később egy metódust, amely kezelni fogja a gombeseményt.  Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `button`.

* A tervező felső részén található varázspálca ikonra kattintva kikövetkeztetheti az elrendezés korlátozásait.

  ![A varázspálca ikon képernyőképe](media/sdk/qs-java-android-10-infer-layout-constraints.png)

A szöveg és a felhasználói felületének grafikus ábrázolása kell kinéznie:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `MainActivity.java` forrásfájlt. Cserélje a teljes kódot a fájlban a következőre.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Az `onCreate` metódus egy olyan kódot tartalmaz, amely mikrofon- és internet-engedélyeket kér, majd elindítja a natív platform kötését. A natív platform kötését csak egyszer kell elvégezni. Erre érdemes mihamarabb, már az alkalmazás inicializálása során sort keríteni.

   * Ahogy korábban már említettük, az `onSpeechButtonClicked` metódus kezeli azt, ha a gombra kattintanak. A gomb lenyomása aktiválja a diktálás funkció elindítását.

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg róla, hogy engedélyezte a [fejlesztői módot és az USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás kiépítéséhez nyomja le a Ctrl+F9 billentyűparancsot, vagy válassza a **Build (Kiépítés)** > **Make Project (Projekt létrehozása)** elemet a menüsávon.

1. Az alkalmazás futtatásához nyomja le a Shift+F10 billentyűparancsot, vagy válassza a **Run (Futtatás)** > **Run 'app' („Alkalmazás” futtatása)** elemeket.

1. A megjelenő, az üzembehelyezési cél megadására szolgáló ablakban válassza ki az Android-eszközt.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](media/sdk/qs-java-android-12-deploy.png)

Nyomja meg az alkalmazásban található gombot a szövegfelismerés aktiválásához. A következő 15 másodpercben kimondott (angol nyelvű) beszédet a rendszer elküldi a Speech Service-nek, amely írott szöveggé alakítja. Az eredmény az Android-alkalmazásban és az Android Studio logcat ablakában jelenik meg.

![Az Android-alkalmazás képernyőképe](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/java-android` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a Javához készült Speech SDK-val](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
