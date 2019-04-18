---
title: 'Gyors útmutató: Recognize speech, Java (Android) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést a Java Android rendszeren a Speech SDK-val
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 690656449fdb86c200a8978f0e17db562e4abbca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009178"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerést a Java Android rendszeren a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből elsajátíthatja fogja, hogyan hozhat létre egy Java-alkalmazást az Android a Cognitive Services beszédfelismerő SDK a beszédfelismerés lefényképezze.
Az alkalmazás a Speech SDK Maven-csomag, a verzió 1.4.0-s és az Android Studio 3.3-as alapul.
A Speech SDK jelenleg a 32/64 bites ARM, vagy Intel x86/x64 processzorokat használó Android-eszközökkel kompatibilis.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez beszédszolgáltatások előfizetési kulcs szükséges. amelyet ingyenesen is beszerezhet. Lásd: [próbálja ki ingyenesen a beszédszolgáltatások](get-started.md) részleteiről.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

1. Indítsa el az Android Studio alkalmazást, majd az üdvözlőablakban válassza a **Start a new Android Studio project** (Új Android Studio-projekt indítása) elemet.

    ![Az Android Studio üdvözlőablakának képernyőképe](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. A **válassza ki a projekt** varázsló jelenik meg, válasszon **telefon és táblagép** és **üres tevékenység** tevékenység kiválasztási mezőben. Kattintson a **Tovább** gombra.

   ![Válassza ki a projekt varázsló képernyőképe](media/sdk/qs-java-android-02-target-android-devices.png)

1. Az a **projekt konfigurálásához** képernyőn írja be **rövid** , **neve**, **samples.speech.cognitiveservices.microsoft.com** , **Csomagnév**, és válassza ki a projekt könyvtárában. A **minimális API-szintet** válasszon **API 23: Android 6.0 (marshmallow rendszert)**, hagyja az összes többi jelölőnégyzet nincs bejelölve, és válassza **Befejezés**.

   ![Képernyőfelvétel a projekt varázsló konfigurálása](media/sdk/qs-java-android-03-create-android-project.png)

Az Android Studio előkészíti az új Android-projektet. Ezután úgy kell konfigurálnia a projektet, hogy az képes legyen használni a Speech SDK-t és a Java 8-at.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.4.0`.

A beszédfelismerés SDK for Android van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak és Android-engedélyek megadása kötelező.
A Maven tárházból, https vannak tárolva:\//csspeechstorage.blob.core.windows.net/maven/.

Készítse elő a projektet a Speech SDK használatára. A Project Structure (Projektstruktúra) ablak megnyitásához az Android Studio menüsávján válassza a **File (Fájl)** > **Project Structure (Projektstruktúra)** elemet. A Project Structure (Projektstruktúra) ablakban hajtsa végre a következő módosításokat:

1. Az ablak bal oldalán található listából válassza ki a **Project** (Projekt) elemet. A **Default Library Repository** (Kódtár alapértelmezett adattára) értékéhez fűzzön hozzá egy vesszőt, majd a Maven-adattár aposztrófok közé zárt URL-címét. "https:\//csspeechstorage.blob.core.windows.net/maven/"

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ugyanezen képernyő bal oldalán válassza ki az **alkalmazást**. Ezután kattintson a **Dependencies** (Függőségek) fülre az ablak felső részén. Kattintson a zöld plusz jelre (+), majd a legördülő menüből válassza a **Library dependency** (Kódtárfüggőség) elemet.

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](media/sdk/qs-java-android-07-add-module-dependency.png)

1. A megnyíló ablakban adja meg az androidos Speech SDK nevét és verzióját: `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0`. Ezután kattintson az **OK** gombra.
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

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

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

Nyomja meg az alkalmazásban található gombot a szövegfelismerés aktiválásához. A következő, angol nyelvű beszéd 15 másodperc lesz a Speech szolgáltatásoknak küldött, és megjelenített érzéseket. Az eredmény az Android-alkalmazásban és az Android Studio logcat ablakában jelenik meg.

![Az Android-alkalmazás képernyőképe](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Java-példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
