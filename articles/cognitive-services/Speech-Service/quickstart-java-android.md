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
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 22970eb58dc244405e522d46fcaa380151076062
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603100"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerést a Java Android rendszeren a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből elsajátíthatja fogja, hogyan hozhat létre egy Java-alkalmazást az Android a Cognitive Services beszédfelismerő SDK a beszédfelismerés lefényképezze.
Az alkalmazás a Speech SDK Maven-csomag, az 1.6.0-s verziójának és az Android Studio 3.3-as alapul.
A Speech SDK jelenleg a 32/64 bites ARM, vagy Intel x86/x64 processzorokat használó Android-eszközökkel kompatibilis.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez beszédszolgáltatások előfizetési kulcs szükséges. amelyet ingyenesen is beszerezhet. Lásd: [próbálja ki ingyenesen a beszédszolgáltatások](get-started.md) részleteiről.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

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

1. Az alkalmazás kiépítéséhez nyomja le a Ctrl+F9 billentyűparancsot, vagy válassza a **Build (Kiépítés)**  > **Make Project (Projekt létrehozása)** elemet a menüsávon.

1. Az alkalmazás futtatásához nyomja le a Shift+F10 billentyűparancsot, vagy válassza a **Run (Futtatás)**  > **Run 'app' („Alkalmazás” futtatása)** elemeket.

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
