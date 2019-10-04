---
title: 'Gyors útmutató: Beszédszintetizátor-beszéd, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szintetizálhatja a beszédfelismerést Java nyelven Androidon a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804040"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés a Java-ban Androidon a Speech SDK használatával

A [beszédfelismerés](quickstart-java-android.md) és a [hang-első virtuális asszisztens](quickstart-virtual-assistant-java-android.md)is elérhetővé teszi a gyors üzembe helyezést.

Ebből a cikkből megtudhatja, hogyan fejleszthet Java-alkalmazásokat az Androidhoz a Cognitive Services Speech SDK használatával a beszéd szövegből való szintéziséhez.
Az alkalmazás a Speech SDK Maven-csomagon, a 1.7.0 és a Android Studio 3,3-as verzión alapul.
A Speech SDK jelenleg a 32/64 bites ARM, vagy Intel x86/x64 processzorokat használó Android-eszközökkel kompatibilis.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>A felhasználói felület létrehozása

Létrehozunk egy alapvető felhasználói felületet az alkalmazáshoz. Kezdje el szerkeszteni a fő tevékenység (`activity_main.xml`) elrendezését. Kezdetben az elrendezés tartalmaz egy címsort az alkalmazás nevével, valamint egy olyan TextView, amely a ""Helló világ!"alkalmazás!" szöveget tartalmazza.

1. Kattintson a TextView elemre. Módosítsa az ID attribútumot a jobb felső sarokban `outputMessage` értékre, majd húzza az alsó képernyőre. Törölje a szövegét.

1. Az `activity_main.xml` ablak bal felső részén található Palette (Paletta) részről húzzon át egy gombot a szöveg fölötti üres részre.

1. A gomb attribútumai jobb oldalon láthatók. Az `onClick` attribútum értékét állítsa a következőre: `onSpeechButtonClicked`. Ezen a néven írni fogunk később egy metódust, amely kezelni fogja a gombeseményt.  Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `button`.

1. Húzzon egy egyszerű szöveget a gomb fölé a szóköz fölé. módosítsa az ID attribútumát `speakText` értékre, és módosítsa a Text (szöveg) attribútumot `Hi there!` értékre.

1. A tervező felső részén található varázspálca ikonra kattintva kikövetkeztetheti az elrendezés korlátozásait.


    ![A varázspálca ikon képernyőképe](media/sdk/qs-java-android-10-infer-layout-constraints.png)

A felhasználói felület szöveg-és grafikus ábrázolásának ekkor a következőképpen kell kinéznie:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `MainActivity.java` forrásfájlt. Cserélje a teljes kódot a fájlban a következőre.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Ahogy korábban már említettük, az `onSpeechButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gombnyomással megnyomhatja A beszédfelismerési szintézist.

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg róla, hogy engedélyezte a [fejlesztői módot és az USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön. Alternatív megoldásként hozzon létre egy [Android-emulátort](https://developer.android.com/studio/run/emulator).

1. Az alkalmazás kiépítéséhez nyomja le a Ctrl+F9 billentyűparancsot, vagy válassza a **Build (Kiépítés)**  > **Make Project (Projekt létrehozása)** elemet a menüsávon.

1. Az alkalmazás futtatásához nyomja le a Shift+F10 billentyűparancsot, vagy válassza a **Run (Futtatás)**  > **Run 'app' („Alkalmazás” futtatása)** elemeket.

1. A megjelenő központi telepítési cél ablakban válassza ki az androidos eszközt vagy emulátort.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](media/sdk/qs-java-android-12-deploy.png)

Írjon be egy szöveget, és nyomja meg az alkalmazás gombját a beszédfelismerési szintézis szakasz megkezdéséhez. A szintetizált hang az alapértelmezett beszélőtől fog megjelenni, és megtekintheti a képernyőn megjelenő `speech synthesis succeeded` információt.

![Az Android-alkalmazás képernyőképe](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
