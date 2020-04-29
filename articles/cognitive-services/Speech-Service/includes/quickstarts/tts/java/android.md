---
title: 'Gyors útmutató: beszédfelismerési beszéd, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szintetizálhatja a beszédfelismerést Java nyelven Androidon a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: d114e75a08f31a664772b84e19ec4d93b453af0b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275365"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)

## <a name="create-user-interface"></a>A felhasználói felület létrehozása

Létrehozunk egy alapvető felhasználói felületet az alkalmazáshoz. Kezdje el szerkeszteni a fő tevékenység (`activity_main.xml`) elrendezését. Kezdetben az elrendezés tartalmaz egy címsort az alkalmazás nevével, valamint egy olyan TextView, amely a ""Helló világ!"alkalmazás!" szöveget tartalmazza.

1. Kattintson a TextView elemre. Módosítsa az ID attribútumot a jobb felső sarokban `outputMessage`, és húzza az alsó képernyőre. Törölje a szövegét.

1. Az `activity_main.xml` ablak bal felső részén található Palette (Paletta) részről húzzon át egy gombot a szöveg fölötti üres részre.

1. A gomb attribútumai jobb oldalon láthatók. Az `onClick` attribútum értékét állítsa a következőre: `onSpeechButtonClicked`. Ezen a néven írni fogunk később egy metódust, amely kezelni fogja a gombeseményt.  Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `button`.

1. Húzzon egy egyszerű szöveget a gomb fölé a szóköz fölé. módosítsa az ID attribútumát `speakText`a értékre, és módosítsa a `Hi there!`Text attribútumot a következőre:.

1. A tervező felső részén található varázspálca ikonra kattintva kikövetkeztetheti az elrendezés korlátozásait.


    ![A varázspálca ikon képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

A felhasználói felület szöveg-és grafikus ábrázolásának ekkor a következőképpen kell kinéznie:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `MainActivity.java` forrásfájlt. Cserélje a teljes kódot a fájlban a következőre.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Ahogy korábban már említettük, az `onSpeechButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gombnyomással megnyomhatja A beszédfelismerési szintézist.

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg róla, hogy engedélyezte a [fejlesztői módot és az USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön. Alternatív megoldásként hozzon létre egy [Android-emulátort](https://developer.android.com/studio/run/emulator).

1. Az alkalmazás létrehozásához nyomja le a CTRL + F9 billentyűkombinációt, vagy válassza a menüsávon a **Létrehozás** > **projekt** létrehozása lehetőséget.

1. Az alkalmazás elindításához nyomja le a SHIFT + F10 billentyűkombinációt, **vagy válassza** > az**alkalmazás futtatása**parancsot.

1. A megjelenő központi telepítési cél ablakban válassza ki az androidos eszközt vagy emulátort.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Írjon be egy szöveget, és nyomja meg az alkalmazás gombját a beszédfelismerési szintézis szakasz megkezdéséhez. A szintetizált hang az alapértelmezett beszélőtől fog megjelenni, és `speech synthesis succeeded` megtekintheti a képernyőn megjelenő adatokat.

![Az Android-alkalmazás képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
